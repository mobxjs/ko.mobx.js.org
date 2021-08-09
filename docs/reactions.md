---
title: reaction으로 부수효과 실행하기
sidebar_label: Reactions {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# reaction으로 부수효과 실행하기 {🚀}

reaction은 MobX의 모든 것을 포함하고 있기 때문에 꼭 이해하고 넘어가야 할 개념입니다.
reaction의 목표는 자동으로 발생하는 부수효과를 모델링 하는 것입니다.
reaction의 중요성은 observable state에 대한 소비자(consumer)를 만들어내거나 무언가 _관련된_ 요소가 바뀔 때 _자동적으로_ 부수효과를 실행하는 데 있습니다.

그러나 이러한 점을 염두에 두고, 본 페이지에서 논의되는 API는 되도록 사용하지 않아야 한다는 점을 깨달아야 합니다.
해당 API들은 mobx-react와 같은 라이브러리 또는 애플리케이션 내의 특정한 추상화에서 추상화됩니다.

하지만 일단 MobX를 이해하기 위해서 reaction이 어떻게 만들어지는지를 살펴봅시다.
가장 단순한 방법은 [`autorun`](#autorun) 유틸리티를 사용하는 것입니다.
그 외에는 [`reaction`](#reaction), [`when`](#when)이 있습니다.

## Autorun

사용 방법:

-   `autorun(effect: (reaction) => void)`

`autorun` 함수는 변화를 감지할 때마다 실행하는 함수 한 개를 수용하며, `autorun` 자체를 생성할 때도 한 번 실행됩니다.
`autorun`은 `observable` 또는 `computed`로 주석 설정한 observable state의 변화에만 반응합니다.

### 트래킹 동작 원리

autorun은 _반응형 컨텍스트_ 내의 `effect`를 실행함으로써 동작합니다. 제공된 함수가 실행되는 동안 MobX는 effect로부터 직접적으로 혹은 간접적으로 _읽어들이는_ 모든 observable 및 computed 값을 트래킹 합니다.
함수 동작이 끝나면 MobX는 읽어들인 모든 observable 항목들을 모으고 구독하며, 해당 항목들 중 일부가 다시 변경될 때까지 기다립니다.
변경이 완료되면 `autorun`은 다시 트리거 되고 전체 과정을 반복합니다.

![autorun](assets/autorun.png)

위 이미지는 하단의 예시가 작동하는 과정을 설명합니다.

### 예시

```javascript
import { makeAutoObservable, autorun } from "mobx"

class Animal {
    name
    energyLevel

    constructor(name) {
        this.name = name
        this.energyLevel = 100
        makeAutoObservable(this)
    }

    reduceEnergy() {
        this.energyLevel -= 10
    }

    get isHungry() {
        return this.energyLevel < 50
    }
}

const giraffe = new Animal("Gary")

autorun(() => {
    console.log("Energy level:", giraffe.energyLevel)
})

autorun(() => {
    if (giraffe.isHungry) {
        console.log("Now I'm hungry!")
    } else {
        console.log("I'm not hungry!")
    }
})

console.log("Now let's change state!")
for (let i = 0; i < 10; i++) {
    giraffe.reduceEnergy()
}
```

위 코드를 실행하면 다음과 같은 결과를 얻습니다.

```
Energy level: 100
I'm not hungry!
Now let's change state!
Energy level: 90
Energy level: 80
Energy level: 70
Energy level: 60
Energy level: 50
Energy level: 40
Now I'm hungry!
Energy level: 30
Energy level: 20
Energy level: 10
Energy level: 0
```

위 코드의 처음 두 줄에 보이듯이, 두 개의 `autorun` 함수는 초기화될 때 한 번 실행됩니다.
`for` 루프가 없어도 해당 두 줄은 보일 것입니다.

`reduceEnergy` action으로 `energyLevel`를 변경하기 위해 `for` 루프를 실행하면, `autorun` 함수가 observable state의 변화를 감지하는 '모든 순간' 새로운 로그를 출력합니다.

1.  함수 _"Energy level"_의 측면에서 '모든 순간'이란 observable 속성을 가진 `energyLevel`이 변경되는 10회입니다.

2.  함수 _"Now I'm hungry"_의 측면에서  '모든 순간'이란 computed 속성을 가진 `isHungry`가 변경되는 1회입니다.

## Reaction

사용 방법:

-   `reaction(() => value, (value, previousValue, reaction) => { sideEffect }, options?)`.

`reaction`은 `autorun`과 유사하지만 추적할 observable에 대해 보다 세밀하게 제어할 수 있습니다.
`reaction`은 다음과 같이 두 개의 함수를 취합니다. 첫 번째 _data_ 함수는 트래킹 되어 두 번째 _effect_ 함수에 대한 input으로 사용되는 데이터를 반환합니다.
부수효과는 _오직_ data 함수에서 _액세스 된_ 데이터에만 반응하며, 이는 effect 함수에 실제로 사용되는 데이터보다 적을 수 있다는 점에 유의해야 합니다.

일반적인 패턴은 _data_ 함수에서 부수 효과에 필요한 항목을 생성하여 effect가 트리거 되는 시점을 보다 정확하게 제어하는 것입니다.
기본적으로 _effect_ 함수가 트리거 되기 위해서는 _data_ 함수의 결과가 변경되어야 합니다.
`autorun`과 달리 부수효과는 초기화될 때 실행되지 않으며, 데이터 표현(expression)이 처음으로 새로운 값을 반환할 때에만 실행됩니다.

<details id="reaction-example"><summary>**예시:** data 함수와 effect 함수<a href="#reaction-example" class="tip-anchor"></a></summary>

하단의 예제에서 reaction은 `isHungry`가 바뀔 때만 한 번 트리거 됩니다.
_effect_ 함수에서 사용된 `giraffe.energyLevel`의 변경 사항은 _effect_ 함수를 실행시키지 않습니다.
이때에도 `reaction`이 반응하기를 원한다면, _data_ 함수에서도 `giraffe.energyLevel`에 액세스하여 반환해야 합니다.

```javascript
import { makeAutoObservable, reaction } from "mobx"

class Animal {
    name
    energyLevel

    constructor(name) {
        this.name = name
        this.energyLevel = 100
        makeAutoObservable(this)
    }

    reduceEnergy() {
        this.energyLevel -= 10
    }

    get isHungry() {
        return this.energyLevel < 50
    }
}

const giraffe = new Animal("Gary")

reaction(
    () => giraffe.isHungry,
    isHungry => {
        if (isHungry) {
            console.log("Now I'm hungry!")
        } else {
            console.log("I'm not hungry!")
        }
        console.log("Energy level:", giraffe.energyLevel)
    }
)

console.log("Now let's change state!")
for (let i = 0; i < 10; i++) {
    giraffe.reduceEnergy()
}
```

출력 결과:

```
Now let's change state!
Now I'm hungry!
Energy level: 40
```

</details>

## When

사용 방법:

-   `when(predicate: () => boolean, effect?: () => void, options?)`
-   `when(predicate: () => boolean, options?): Promise`

`when`은 `true`를 반환할 때까지 주어진 _predicate_ 함수를 관찰하고 실행합니다.
`true`가 반환되면 지정된 _effect_ 함수를 실행하고 자동 실행기를 삭제(dispose)합니다.

`when` 함수는 disposer를 반환하므로 두 번째 `effect` 함수를 전달하지 않는 한 수동으로 취소할 수 있으며, 이 경우 `Promise`가 반환됩니다.

<details id="when-example">
  <summary>**예시:** 반응적으로 dispose 하기<a href="#when-example" class="tip-anchor"></a></summary>

`when`은 무언가를 반응적으로 dispose 하거나 취소하는 데 굉장히 유용합니다.
예시:

```javascript
import { when, makeAutoObservable } from "mobx"

class MyResource {
    constructor() {
        makeAutoObservable(this, { dispose: false })
        when(
            // 만약...
            () => !this.isVisible,
            // ...그러면
            () => this.dispose()
        )
    }

    get isVisible() {
        // 해당 항목이 visible인지 식별합니다.
    }

    dispose() {
        // 리소스를 정리합니다.
    }
}
```

`isVisible`이 `false`가 되자마자 `dispose`를 호출하여 `MyResource`를 정리합니다.

</details>

### `await when(...)`

`effect` 함수가 제공되지 않으면 `when`은 `Promise`를 반환합니다. `async ∙ await`과 함께 사용하면 observable state의 변경 사항을 기다릴 수 있게 해줍니다.

```javascript
async function() {
	await when(() => that.isVisible)
	// 등등...
}
```

`when`을 조기에 취소하고 싶다면 자체적으로 반환된 promise에 `.cancel()`을 호출하면 됩니다.

## 규칙

반응형 컨텍스트에 적용되는 몇 가지 규칙이 있습니다.

1. observable이 변경될 경우 영향을 받는 reaction이 기본적으로 즉시(동기적으로) 실행합니다. 그러나 현재의 가장 바깥쪽 (trans)action이 완료되기 전까지는 실행되지 않습니다.
2. autorun은 제공된 함수가 동기적으로 실행되는 동안 읽어지는 observable만을 트래킹 합니다. 비동기적으로 발생하는 것은 트래킹 하지 않습니다.
3. action은 _트래킹 대상이 아니기_ 때문에,  autorun은 autorun 내의 action에서 읽어지는 observable을 트래킹 하지 않습니다.

MobX이 구체적으로 무엇에 반응하고, 반응하지 않는지에 대해 더 많은 예시를 보려면 [반응성 이해하기](understanding-reactivity.md) 섹션을 확인하세요.
트래킹 작동 방법에 대한 자세한 기술 정보는 [Becoming fully reactive: an in-depth explanation of MobX](https://hackernoon.com/becoming-fully-reactive-an-in-depth-explanation-of-mobservable-55995262a254) 포스트를 참고하세요.


## 항상 reaction dispose 하기

`autorun`, `reaction` 그리고 `when`에 전달되는 함수는 관찰하는 모든 객체가 자체적으로 가비지 수집을 하는 경우에만 가비지 수집됩니다. 
일반적으로 이 함수들은 사용하는 observable이 새롭게 변경될 때까지 계속 대기합니다.
이러한 대기 상태를 멈추기 위해, 모든 함수는 '대기 상태를 중단하고 함수에서 사용한 observable의 구독을 취소하는 역할'의 disposer 함수를 반환합니다.

```javascript
const counter = observable({ count: 0 })

// autorun을 세팅하고 0을 출력합니다.
const disposer = autorun(() => {
    console.log(counter.count)
})

// 출력 값: 1
counter.count++

// autorun을 중단합니다.
disposer()

// 아무 것도 출력하지 않습니다.
counter.count++
```

메서드의 부수효과가 더 이상 필요하지 않은 경우 즉시 해당 메서드에서 반환되는 disposer 함수를 사용하는 것이 좋습니다.
그렇지 않으면 메모리 누수가 발생할 수 있습니다.

`reaction`과 `autorun`의 effect 함수에 대해 두 번째 인수로 전달된 `reaction` 인수는 `reaction.dispose()`을 호출하여 reaction을 조기에 정리하는 데에도 활용될 수 있습니다.

<details id="mem-leak-example"><summary>**예시:** 메모리 누수<a href="#mem-leak-example" class="tip-anchor"></a></summary>

```javascript
class Vat {
    value = 1.2

    constructor() {
        makeAutoObservable(this)
    }
}

const vat = new Vat()

class OrderLine {
    price = 10
    amount = 1
    constructor() {
        makeAutoObservable(this)

        // 이 autorun은 `this`로부터 받은 observable만 사용하므로 
        // 현재 Orderline의 인스턴스와 함께 GC(Garbage Collection)로 설정될 것입니다. 
        // OrderLine 인스턴스가 제거되면 꼭 해당 autorun을 dispose할 필요는 없습니다.
        this.disposer1 = autorun(() => {
            doSomethingWith(this.price * this.amount)
        })

        // vat에서 이 autorun을 알리기 위한 참조를 유지하며, 
        // 결과적으로 'this'가 범위 안에 유지되기 때문에
        // 해당 autorun은 현재 Orderline의 인스턴스와 함께 GC로 설정되지 않습니다.
        this.disposer2 = autorun(() => {
            doSomethingWith(this.price * this.amount * vat.value)
        })
    }

    dispose() {
        // 따라서 미묘한 메모리 문제를 피하기 위해
        // reaction이 더 이상 필요하지 않은 경우 항상 disposer를 호출하세요.
        this.disposer1()
        this.disposer2()
    }
}
```

</details>

## reaction을 조금만 사용하세요!

이미 언급했지만 reaction을 생성할 일은 드물 것입니다.
애플리케이션에서는 이러한 API를 직접적으로 사용하지 않을 것이며, reaction을 구성하는 유일한 방법은 Mobx-react 바인딩의 'observer'와 같은 간접적인 것입니다.

reaction을 세팅하기 전에 하단의 원칙을 준수하고 있는지를 먼저 확인해보세요.

1. **원인(cause)과 영향(effect) 사이에 직접적인 관계가 없는 경우에만 reaction을 사용하세요.** 부수 효과가 제한된 일련의 event ∙ action에 대응하여 발생하는 경우, 특정 action에서 effect를 직접적으로 트리거 하는 편이 종종 더 명확합니다. 
예를 들어 폼 제출 버튼을 누르면 네트워크 요청이 게시될 경우, 간접적으로 reaction을 사용하는 것보다는 해당 effect를 `onClick` 이벤트에 대한 응답으로써 직접적으로 트리거 하는 것이 더 명확합니다.
폼 상태에 대한 변경사항이 자동으로 로컬 저장소에 있어야 하는 경우, reaction을 사용하면 모든 개별 `onChange` 이벤트에서 해당 effect를 트리거 하지 않아도 되므로 유용할 수 있습니다.
1. **reaction은 다른 observable을 업데이트하면 안 됩니다.** reaction으로 다른 observable을 수정할 건가요? 만약 그렇다면, 일반적으로 업데이트할 observable은 [`computed`](computeds.md) 값으로 주석을 달아야 합니다. 예를 들어 todo 컬렉션이 변경된 경우 `remainingTodos`의 양을 계산하기 위해 reaction을 사용하는 것이 아니라, `remainingTodos`를 computed 값으로 주석 처리해야 합니다.
그러면 코드를 훨씬 더 명확하고 쉽게 디버깅할 수 있습니다. reaction은 새로운 데이터를 계산하는 것이 아니라, effect를 유발하는 용도로 사용되어야 합니다.
1. **reaction은 독립적이어야 합니다.** 코드가 먼저 실행되어야 하는 다른 reaction에 의존하나요? 이 경우 첫 번째 규칙을 위반했을 수 있으며, 의존하고 있는 reaction에 새로 생성하려는 reaction을 병합해야 합니다. MobX는 reaction이 실행되는 순서를 보장하지 않습니다.

실제로 작업을 하다 보면 상단의 원칙과 부합하지 않는 경우가 있을 수 있습니다. 위 목록은 _법칙_이 아닌 _원칙_입니다.
하지만 예외는 드물기 때문에 원칙을 위반하는 것은 최후의 수단으로 사용하세요.

## Options {🚀}

`autorun`, `reaction`, `when`의 동작은 위의 사용 방법과 같이 `options` 인수를 전달함으로써 더욱 미세하게 조정될 수 있습니다.

### `name`

이 문자열은 [Spy 이벤트 리스너](analyzing-reactivity.md#spy) 및 [MobX 개발자 도구](https://github.com/mobxjs/mobx-devtools)에서 reaction에 대한 디버깅 이름으로 사용됩니다.

### `fireImmediately` _(reaction)_

_data_ 함수의 첫 번째 실행 후 _effect_ 함수가 즉시 트리거 되어야 함을 나타내는 boolean입니다. 기본 값은 `false`입니다.

### `delay` _(autorun, reaction)_

effect 함수를 조정하는 데 사용할 수 있는 시간(밀리초)입니다. 0(기본값)이면 쓰로틀링(throttling)이 수행되지 않습니다.

### `timeout` _(when)_

`when`이 대기하는 제한 시간을 설정합니다. 시간 초과 시 `when`은 reject 또는 throw합니다.

### `onError`

기본적으로 reaction 내부에서 throw된 모든 예외는 로그에 찍히지만, 더 이상 throw 되지는 않습니다. 이는 한 reaction의 예외가 다른(관련되지 않은) reaction의 예정된 실행을 방해하지 않도록 하기 위한 것입니다.
이를 통해 reaction이 예외로부터 복구(recover) 될 수도 있습니다. 예외를 throw 해도 MobX의 트래킹은 중단되지 않으므로 예외의 원인이 제거되면 후속 reaction이 다시 정상적으로 실행될 수 있습니다. 이 옵션을 사용하면 해당 동작을 오버라이딩할 수 있습니다. [configure](configuration.md#disableerrorboundaries-boolean)를 사용하여 전역 오류 처리기(global error handler)를 설정하거나 오류 탐지 기능을 완전히 비활성화할 수 있습니다.

### `scheduler` _(autorun, reaction)_

사용자 지정 스케줄러를 설정하여 autorun 함수 재실행 예약 방법을 결정합니다. `{ scheduler: run => { setTimeout(run, 1000) }}`과 같이 나중에 호출되는 함수가 필요합니다.

### `equals`: (reaction)

기본적으로 `comparer.default`로 설정됩니다. 구체적으로 명시된 경우 이 비교 함수는 _data_ 함수에 의해 생성된 이전 값과 다음 값을 비교하는 데 사용됩니다. _effect_ 함수는 이 함수가 false를 반환하는 경우에만 호출됩니다.

[내장형 comparers](computeds.md#built-in-comparers) 섹션을 확인하세요.