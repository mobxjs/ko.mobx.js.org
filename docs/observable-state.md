---
title: observable state 만들기
sidebar_label: Observable state
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# observable state 만들기

속성(property), 모든 객체, 배열, 맵(map)과 셋(set)은 observable로 설정될 수 있습니다.
객체를 observable로 만드는 가장 기본적인 방법은 속성마다 `makeObservable` 주석을 지정하는 것입니다.
가장 중요한 주석들은 다음과 같습니다.

-   `observable` 은 state를 저장하는 추적 가능한 필드를 정의합니다.
-   `action`은 메서드를 state를 변경할 action으로 표시합니다.
-   `computed`는 state로부터 새로운 사실을 도출하고 그 결괏값을 캐시 하는 getter를 나타냅니다.

배열, map 및 set과 같은 컬렉션은 자동으로 observable 설정됩니다.

## `makeObservable`

사용법:

-   `makeObservable(target, annotations?, options?)`

`makeObservable`은 _존재하는_ 객체 속성을 트랩(trap) 하여 observable로 만듭니다. 클래스 인스턴스를 포함해서 어떠한 JavaScript 객체든 `target`이 될 수 있습니다.
일반적으로 `makeObservable`은 클래스 구조에서 사용되며, 첫 번째 인수는 `this`입니다.
`annotations` 인수는 [주석](#available-annotations)을 각 구성원(member)에 매핑합니다. [데코레이터](enabling-decorators.md)를 사용하는 경우 `annotations` 인수는 생략될 수 있습니다.

정보를 파생시키고 인수를 취하는 메서드(예 : `findUsersOlderThan(age: number): User[]`)에는 주석이 필요하지 않습니다.
이러한 읽기 작업은 reaction에서 호출될 때 계속 추적되지만 메모리 누수를 방지하기 위해 결괏값은 메모리에 저장되지 않습니다. [MobX-utils computedFn {🚀}](https://github.com/mobxjs/mobx-utils#computedfn)에서도 확인해보세요.

[하위 클래스(Subclassing)는 몇 가지 제한점과 함께](subclassing.md) `override` 주석을 통해 지원됩니다.

<!--DOCUSAURUS_CODE_TABS-->
<!--class + makeObservable-->

```javascript
import { makeObservable, observable, computed, action, flow } from "mobx"

class Doubler {
    value

    constructor(value) {
        makeObservable(this, {
            value: observable,
            double: computed,
            increment: action,
            fetch: flow
        })
        this.value = value
    }

    get double() {
        return this.value * 2
    }

    increment() {
        this.value++
    }

    *fetch() {
        const response = yield fetch("/api/value")
        this.value = response.json()
    }
}
```

**주석 처리된 모든** 필드는 **설정 불가(non-configurable)** 합니다.<br>
**observable이 적용되지 않은**(state가 없는) 모든 필드(`action`, `flow`)는 **작성 불가(non-writable)** 합니다.

<!--factory function + makeAutoObservable-->

```javascript
import { makeAutoObservable } from "mobx"

function createDoubler(value) {
    return makeAutoObservable({
        value,
        get double() {
            return this.value * 2
        },
        increment() {
            this.value++
        }
    })
}
```
클래스는 `makeAutoObservable` 또한 레버리지(leverage) 할 수 있습니다.
예제에서의 차이는 MobX가 어떻게 다양한 프로그래밍 스타일에 적용될 수 있는지를 보여줍니다.

<!--observable-->

```javascript
import { observable } from "mobx"

const todosById = observable({
    "TODO-123": {
        title: "find a decent task management system",
        done: false
    }
})

todosById["TODO-456"] = {
    title: "close all tickets older than two weeks",
    done: true
}

const tags = observable(["high prio", "medium prio", "low prio"])
tags.push("prio: for fun")
```
`makeObservable`을 활용한 첫 번째 예제와 대조적으로, `observable`은 객체에 _필드_를 추가하거나 제거합니다.
`observable`은 동적 키(key)를 가진 객체나 배열, map, set과 같은 항목에 유리합니다.

<!--END_DOCUSAURUS_CODE_TABS-->

## `makeAutoObservable`

사용법:

-   `makeAutoObservable(target, overrides?, options?)`

`makeAutoObservable`은 모든 속성(property)을 기본적으로 추론한다는 점에서 `makeObservable`보다 한층 더 업그레이드된 형태입니다.
`overrides`을 사용하여 기본 동작을 특정 주석으로 재정의할 수 있습니다.
특히 `overrides`를 `false`로 설정하면 속성이나 메서드를 처리에서 완전히 제외할 수 있습니다.
예시를 위해 상단의 코드 탭을 확인해보세요.
새로운 구성원(member)은 명시적으로 언급될 필요가 없기 때문에 `makeAutoObservable` 함수가 `makeObservable` 보다 상태 유지에 있어 더 간편하고 쉽습니다.
하지만 super 클래스나 [하위 클래스](subclassing.md)에는 `makeAutoObservable`을 사용할 수 없습니다.

추론 규칙:

-   _가지고 있는_ 모든 속성은 `observable`로 지정됩니다.
-   모든 `get`ter는 `computed`로 지정됩니다.
-   모든 `set`ter는 `action`으로 지정됩니다.
-   모든 _프로토타입 내 함수_는 `autoAction`으로 지정됩니다.
-   모든 _프로토타입 내 생성 함수(generator function)_는 `flow`로 지정됩니다. (생성 함수는 특정 트랜스파일러(transpiler) 환경에서 감지 불가하며 flow가 예상대로 동작하지 않는 경우 `flow`를 분명하게 명시하세요.)
-   `overrides` 인수에서 `false`로 표시된 구성원은 주석을 달지 않습니다. 예를 들어 식별자와 같은 읽기 전용 필드에 사용됩니다.

## `observable`

사용법:

-   `observable(source, overrides?, options?)`

`observable` 주석은 모든 객체를 한 번에 observable로 지정하는 함수로써 호출될 수도 있습니다.
`source` 객체를 복제하여 모든 구성원을 observable로 지정합니다. 이는 `makeAutoObservable`과 유사합니다.
비슷하게 `overrides` 맵은 특정 구성원의 주석을 명시하기 위해 제공됩니다.
예시를 위해 상단의 코드 블록을 확인하세요.

`observable`로 반환된 객체는 프록시가 됩니다. 즉, 객체에 나중에 추가된 속성도 observable로 지정됩니다.([프록시 사용](configuration.md#proxy-support)이 disabled로 설정된 경우는 제외합니다.)

`observable` 메서드는 [array](api.md#observablearray), [map](api.md#observablemap), [set](api.md#observableset)과 같은 컬렉션 유형으로도 호출할 수 있습니다. 해당 컬렉션 유형들(⭐️주어가 이게 맞나?) 또한 복제되어 'observable'로 전환됩니다.

<details id="observable-array"><summary>**예시:** observable 배열<a href="#observable-array" class="tip-anchor"></a></summary>

하단의 예제에서는 observable 항목들을 생성하고 [`autorun`](reactions.md#autorun)을 통해 관측합니다.
map, set 컬렉션으로 작업하면 이와 유사하게 작동합니다.

```javascript
import { observable, autorun } from "mobx"

const todos = observable([
    { title: "Spoil tea", completed: true },
    { title: "Make coffee", completed: false }
])

autorun(() => {
    console.log(
        "Remaining:",
        todos
            .filter(todo => !todo.completed)
            .map(todo => todo.title)
            .join(", ")
    )
})
// 출력값: 'Remaining: Make coffee'

todos[0].completed = false
// 출력값: 'Remaining: Spoil tea, Make coffee'

todos[2] = { title: "Take a nap", completed: false }
// 출력값: 'Remaining: Spoil tea, Make coffee, Take a nap'

todos.shift()
// 출력값: 'Remaining: Make coffee, Take a nap'
```

Observable 배열은 다음과 같이 유용한 함수들을 추가적으로 제공합니다.

-   `clear()` : 배열에서 현재 엔트리(entry)를 모두 제거합니다.
-   `replace(newItems)` : 배열 내에 존재하는 모든 엔트리를 새로운 것으로 대체합니다.
-   `remove(value)` : 배열에서 value가 일치하는 하나의 항목을 제거합니다. 해당 항목을 발견하고 제거했다면 `true`를 반환합니다.

</details>

<details id="non-convertibles"><summary>**주의:** 원시 값(primitive)과 클래스 인스턴스는 절대 observable로 전환되지 않습니다.<a href="#non-convertibles" class="tip-anchor"></a></summary>

원시 값은 JavaScript에서 변경할 수 없는 값이기 때문에 MobX에 의해 observable로 지정될 수 없습니다.([box](api.md#observablebox) 처리는 가능합니다.)
그러나 일반적으로 라이브러리 외부에서는 이 메커니즘을 사용할 수 없습니다.

클래스 인스턴스는 `observable` 속성을 지정하거나 `observable`에 상속되게 하더라도 절대 자동으로 observable이 될 수 없습니다.
클래스 생성자에서 적절히 설정해야 클래스 구성원을 observable로 지정할 수 있습니다.(⭐️의역했는데 의미 맞는지 확인해줭!!)

</details>

<details id="avoid-proxies"><summary>{🚀} **팁:** observable (프록시) vs makeObservable (비 프록시)<a href="#avoid-proxies" class="tip-anchor"></a></summary>

`make(Auto)Observable`과 `observable`의 근본적인 차이점은 `make(Auto)Observable`이 첫 번째 인수로 할당한 객체를 수정하는 반면 `observable`은 observable로 지정된 것을 _복제_한다는 점입니다.

두 번째 차이점으로 `observable`은 [`프록시`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 객체를 생성합니다. 때문에 객체를 동적 탐색 맵(dynamic lookup map)으로 활용하는 경우 이후에 추가되는 속성을 트랩 할 수 있습니다.
observable로 지정하고자 하는 객체가 모든 구성원을 미리 알고 있는 정규 표현식 형태라면, 비 프록시 객체가 프록시 객체보다 좀 더 빠르고 디버거와 `console.log`에서 검사되기 쉬우므로 `makeObservable`을 사용할 것을 권장합니다.

이 때문에 `make(Auto)Observable`는 팩토리 함수(factory function)에 사용하는 API로 권장됩니다.
비 프록시 클론을 생성하려면 `observable`에 `{ proxy: false }` 를 설정하세요.

</details>

## 활용 가능한 주석

| 주석                         | 설명                                                                                                                                                                                                                                                                                                                        |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `observable`<br/>`observable.deep` | state를 저장하는 추적 가능한 필드를 정의합니다. 가능한 경우 `observable`로 할당된 값은 해당 유형에 따라 자동으로 `observable`, `autoAction` 또는 `flow`로 전환됩니다. `순수 객체`, `배열`, `Map`, `Set`, `함수`, `생성 함수(generator function)`만이 전환 가능하며 클래스 인스턴스 및 그 외의 것들은 전환하지 않습니다. |
| `observable.ref`                   | `observable`과 유사하지만 재할당 값만을 추적합니다. 할당된 값들은 완전히 무시되며 절대 `observable`∙`autoAction`∙`flow`로 자동 전환되지 않습니다. 예를 들면 observable 필드에 변경 불가한 데이터를 저장하는 경우 사용하세요.|
| `observable.shallow`               | `observable.ref`과 유사하지만 컬렉션의 경우에만 사용됩니다. 할당된 컬렉션은 observable로 지정되나 컬렉션의 내용 자체는 observable로 지정되지 않습니다.|
| `observable.struct`                | 현재 값과 구조상 똑같은 할당 값은 제외됩니다. 이 점을 제외하고는 `observable`과 유사합니다.|
| `action`                           | 메서드를 state를 변경할 action으로 지정합니다. 더 자세한 내용은 [actions](actions.md)를 확인하세요. 작성 불가(Non-writable) 합니다.|
| `action.bound`                     | action과 유사하지만 추가적으로 `this`가 항상 설정되어 있을 수 있도록 action을 인스턴스에 바인딩 합니다. 작성 불가(Non-writable) 합니다.|
| `computed`                         | 캐시 할 수 있는 파생 값으로 [getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)를 선언하는 데 사용할 수 있습니다. 더 자세한 내용은 [computeds](computeds.md)를 확인하세요.|
| `computed.struct`                  | 재계산된 결괏값이 이전 결괏값과 구조상 똑같은 경우 어떠한 observer에도 알리지 않습니다. 이 점을 제외하고는 `computed`와 유사합니다.|
| `true`                             | 가장 나은 주석을 추론합니다. 더 자세한 내용은 [makeAutoObservable](#makeautoobservable)을 확인하세요.|
| `false`                            | 명시적으로 이 속성은 주석을 달지 마십시오.(⭐️ 이 속성은 명시적으로 활용하지 마세요?)|
| `flow`                             | 비동기 작업 관리를 위해 `flow`를 생성합니다. 더 자세한 내용은 [flow](actions.md#using-flow-instead-of-async--await-)를 확인하세요. TypeScript의 추론된 반환 유형(inferred return type)이 꺼져있을 수 있습니다. 작성 불가(Non-writable) 합니다.|
| `flow.bound`                       | flow와 유사하지만 추가적으로 `this`가 항상 설정되어 있을 수 있도록 flow를 인스턴스에 바인딩 합니다. 작성 불가(Non-writable) 합니다.|
| `override`                         | [상속된 `action`, `flow`, `computed`와 하위 클래스에서 override된 `action.bound`에 적합합니다.](subclassing.md) (⭐️ '하위 클래스에서 override된' 이 action.bound만 꾸며주는건지 전체 다 꾸며주는건지 헷갈려,, 확인부탁!!)|
| `autoAction`                       | 명시적으로 사용되어서는 안되며, `makeAutoObservable` 환경에서 호출 컨텍스트(context)에 따라 action이나 derivation로 활용 가능한 메서드를 표시하는 데 사용됩니다.|

## 제한점

1. `make(Auto)Observable`은 이미 정의되어 있는 속성만 지원합니다. [**컴파일러 구성**이 맞게 되었는지 확인](installation.md#use-spec-compliant-transpilation-for-class-properties)하거나, 차선책으로 `make(Auto)Observable` 사용 이전에 해당 값이 모든 속성에 할당되었는지 확인하세요. 환경 설정이 정확히 되지 않으면, 선언은 되었으나 초기화되지 않은 필드(예 : `class X { y; }`)는 제대로 픽업되지 않습니다.
1. `makeObservable`는 해당 클래스 정의에 의해 선언된 속성만 처리할 수 있습니다. 하위 클래스 혹은 super 클래스에서 observable 필드를 도입하는 경우 해당 속성 자체에서 `makeObservable`을 호출해야 합니다.
1. `options` 인수는 한 번만 규정됩니다. 통과된 `options`는 _"sticky(⭐️뭐라고 해야되지)"_ 하며 이후에 변경될 수 없습니다. ([하위 클래스](subclassing.md) 예시)
1. **모든 필드는 한 번만 주석 설정되어야 합니다**(`override` 제외). [하위 클래스](subclassing.md)에서는 필드 주석이나 구성을 바꿀 수 없습니다.
1. 비순수 객체(**클래스**)에서 **주석 설정된 모든** 필드는 **설정 불가(non-configurable)** 합니다.<br>
   [`configure({ safeDescriptors: false })` {🚀☣️} 로 비활성화 할 수 있습니다.](configuration.md#safedescriptors-boolean)
1. **observable로 지정되지 않은**(state가 없는) 모든 필드 (`action`, `flow`) 는 **작성 불가(non-writable)** 합니다.<br>
   [`configure({ safeDescriptors: false })` {🚀☣️} 로 비활성화 할 수 있습니다.](configuration.md#safedescriptors-boolean)
1. [**프로토타입에서** 정의된 **`action`, `computed`, `flow`, `action.bound`**만이 하위 클래스에서 **override** 될 수 있습니다.](subclassing.md)
1. _TypeScript_에서는 기본적으로 **비공개(private)** 필드를 주석 설정할 수 없습니다. 이는 다음과 같이 관련 비공개 필드를 제네릭(generic) 인수로 통과시키면 해결할 수 있습니다. `makeObservable<MyStore, "privateField" | "privateField2">(this, { privateField: observable, privateField2: observable })`
1. **`make(Auto)Observable`을 호출**하고 주석을 설정하는 작업은 추론 결과를 캐시 하기 위해서 **반드시** 수행되어야 합니다.
1. **`make(Auto)Observable`** 호출 이후에는 **프로토타입을 수정할 수 없습니다**.
1. _EcmaScript_ **비공개** 필드(**`#field`**)는 **지원되지 않습니다**. _TypeScript_를 사용하는 경우 `private` 모디파이어를 사용하도록 권장합니다.
1. 단일 상속성 체인(inheritance chain) 내에서 **주석과 데코레이터를 혼용**하는 것은 **지원되지 않습니다**. 예를 들면 super 클래스에서는 데코레이터를, 하위 클래스에서는 주석을 사용할 수 없습니다.
1. `makeObservable`,`extendObservable`는 또 다른 빌트인 observable 유형(`ObservableMap`, `ObservableSet`, `ObservableArray` 등)에서 사용될 수 없습니다.
1. `makeObservable(Object.create(prototype))`은 `prototype`에서 생성 객체로 속성을 복사하고 해당 속성을 `observable`로 지정합니다. 이 동작은 잘못되었으므로 **사라질 기능**이며 이후 버전에서 수정될 것입니다. 해당 기능에 의지하지 마세요.

## Options {🚀}

상단의 API는 선택적 `options` 인수를 취하며 해당 인수는 하단의 옵션을 지원하는 객체입니다.

-   **`autoBind: true`**는 `action`∙`flow`가 아닌 `action.bound`∙`flow.bound`를 기본으로 사용합니다. 또한 명시적으로 주석 설정된 구성원에 영향을 미치지 않습니다.
-   **`deep: false`**는 `observable`이 아닌 `observable.ref`를 기본으로 사용합니다. 또한 명시적으로 주석 설정된 구성원에 영향을 미치지 않습니다.
-   **`name: <string>`**는 오류 메시지 및 리플렉션 API로 인쇄된 디버그 이름을 개체에 지정합니다.
-   **`proxy: false`**는 `observable(thing)`가 비 [**프록시**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) 구현을 사용하도록 강제합니다. 비 프록시 객체는 디버깅하기 쉽고 속도도 빠르기 때문에 시간이 지나도 객체 모양이 변경되지 않는 경우 이 옵션을 사용하는 것이 좋습니다. [프록시 방지하기](#avoid-proxies) 문서를 확인하세요.

<details id="one-options-per-target"><summary>**유의:** options는 *sticky*하며 한 번만 제공할 수 있습니다.<a href="#one-options-per-target" class="tip-anchor"></a></summary>
`options` 인수는 observable 하지 않은 `target`에만 제공됩니다.<br>
observable 객체가 초기화되면 옵션을 변경할 수 없습니다.<br>
options는 타겟에 저장되며 이후의 `makeObservable`∙`extendObservable` 호출에 의해 반영(respect, 존중?)됩니다.<br>
[하위 클래스](subclassing.md)에서는 다른 옵션을 적용할 수 없습니다.
</details>

## observable 요소를 vanilla JavaScript 컬렉션으로 전환하기

예를 들면 observable 객체를 트래킹 할 수 없는 React 컴포넌트에 observable 객체를 적용해야 할 때, 혹은 더 이상 변형되지 않아야 하는 클론을 얻고 싶을 때와 같이 observable 한 데이터 구조를 vanilla 데이터 구조로 다시 변환해야 하는 경우가 있습니다.

컬렉션을 얕게(shallowly) 변환하려면 일반적인 JavaScript 메커니즘을 활용하세요.

```javascript
const plainObject = { ...observableObject }
const plainArray = observableArray.slice()
const plainMap = new Map(observableMap)
```

재귀적으로 데이터 트리를 순수 객체로 전환하기 위해 [`toJS`](api.md#tojs)를 사용할 수 있습니다.
클래스의 경우 `JSON.stringify`에서 선택되므로 `toJSON()` 메서드를 실행하는 것이 좋습니다.

## 클래스에 대한 짧은 참고자료

지금까지 상단의 예제들은 대부분 클래스 구문에 치우쳐 있었습니다.
MobX는 원칙적으로 이에 대해 아무런 의견이 없으며, 일반 객체를 사용하는 MobX 사용자도 아마 그만큼 많을 것입니다.
그러나 클래스의 이점은 TypeScript와 같이 훨씬 더 쉽게 찾을 수 있는 API가 있다는 것입니다.
또한 `instanceof` 체크는 유형 추론에 매우 강력하며 클래스 인스턴스는 `Proxy` 객체에 감싸지지 않기 때문에 디버거에서 더 나은 경험을 제공합니다.
마지막으로, 클래스는 모양이 예측 가능하고 프로토타입에서 메서드를 공유하기 때문에 많은 엔진 최적화의 이점을 누릴 수 있습니다.
그러나 무거운 상속 패턴은 쉽게 풋건(foot-gun)이 될 수 있으므로 클래스를 사용할 경우 단순하게 유지하십시오.
따라서, 비록 클래스를 사용하는 것에 대해 약간의 호불호가 있지만, 그것이 당신에게 더 잘 맞는다면 이 스타일에서 벗어날 것을 확실히 권하고 싶습니다.(⭐️ '그것', '이 스타일'을 뭐라고 번역해야 더 확실할까..)