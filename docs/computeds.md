---
title: computed를 이용한 정보 파생
sidebar_label: Computeds
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# computed를 이용한 정보 파생

사용 방법:

-   `computed` _(annotation)_
-   `computed(options)` _(annotation)_
-   `computed(fn, options?)`

computed 값을 사용하여 다른 observable 정보를 얻을 수 있습니다.
computed 값은 느리게 평가하여 출력을 캐싱하고 observables 중 하나가 변경된 경우에만 다시 계산합니다.
아무것도 관찰되지 않으면 완전히 작동을 멈춥니다.

개념적으로 스프레드시트의 수식과 매우 유사하며 과소평가할 수 없습니다. 저장해야 하는 state를 줄이는 데 도움이 되며 매우 최적화되어 있습니다. 가능한 모든 곳에 사용하세요.

## 예시

computed 값은 Javascript [getters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)에 `computed` 주석을 달아 생성할 수 있습니다.
`makeObservable`을 사용하여 getter를 computed로 선언합니다. 모든 getter를 `computed`로 선언되도록 하려면 `makeAutoObservable`, `observable` 또는 `extendObservable`를 사용하시면 됩니다.

아래 예제에서는 computed 값의 요점을 설명하기 위해 [Reactions {🚀}](reactions.md) 섹션의 [`autorun`](reactions.md#autorun)을 사용합니다.

```javascript
import { makeObservable, observable, computed, autorun } from "mobx"

class OrderLine {
    price = 0
    amount = 1

    constructor(price) {
        makeObservable(this, {
            price: observable,
            amount: observable,
            total: computed
        })
        this.price = price
    }

    get total() {
        console.log("Computing...")
        return this.price * this.amount
    }
}

const order = new OrderLine(0)

const stop = autorun(() => {
    console.log("Total: " + order.total)
})
// 계산중...
// Total: 0

console.log(order.total)
// 재계산 되지 않음!
// 0

order.amount = 5
// 계산중...
// autorun 실행 안됨

order.price = 2
// 계산중...
// Total: 10

stop()

order.price = 3
// computation, autorun 둘다 다시 계산되지 않습니다.
```

위의 예는 `computed` 값의 장점을 잘 보여주고 있으며 캐싱이 되는 모습도 확인하실 수 있습니다.
`amount`를 변경하면 `total`이 다시 계산되도록 트리거가 되지만,
`total`은 출력에 영향을 미치지 않았음을 감지하므로 `autorun`을 업데이트할 필요가 없습니다.

반면에 `total`에 주석(annotation)을 달지 않으면 `autorun`은 `total`과 `amount`에 따라 달라지기 때문에 3번 실행됩니다.
[직접 확인해보세요.](https://codesandbox.io/s/computed-3cjo9?file=/src/index.tsx)

![computed graph](assets/computed-example.png)

위의 예제와 관련된 의존성 그래프입니다.

## 규칙

computed 사용 시 주의사항:

1. 부수효과(side effects)를 가지거나 다른 observable 항목을 업데이트하면 안 됩니다.
2. 새로운 observable 항목을 만들고 반환하면 안 됩니다.

## 팁

<details id="computed-suspend"><summary>**Tip:** computed 값이 관찰되지 않으면 computed 기능이 일시 중단됩니다.<a href="#computed-suspend" class="tip-anchor"></a></summary>

MobX에 대해 처음 접하는 사람 중 [Reselect](https://github.com/reduxjs/reselect)와 같은 라이브러리에 익숙한 사람들은 혼란스러울 수도 있습니다. 생성한 computed 속성이 reaction의 어느 곳에서도 사용되지 않으면 해당 속성이 기억되지도 않으며 필요한 것보다 자주 계산되는 것 처럼 보일 수 있습니다.
예를 들어 위의 예시에서  `stop()`을 호출한 후 `console.log(order.total)`를 두 번 호출했다면 값은 두 번 계산됩니다.

따라서 MobX는 접근하지 않는 computed 값에 대해 불필요한 업데이트를 방지하기 위해
사용되지 않는 계산을 자동으로 일시중단 할 수 있습니다. 그러나 computed 속성이 일부 reaction에 사용되고 있지 _않으면_ computed 표현식은 값이 요청될 때마다 평가되므로 일반 속성처럼 작동합니다.

computed 속성만 조작하는 경우 효율적이지 않을 수 있지만, `observer` 및 `autorun`등을 사용하는 프로젝트에 적용하면 매우 효율적입니다.

아래 코드는 위에서 설명한 문제를 보여줍니다.

```javascript
// OrderLine에는 computed 속성인 `total`을 가지고 있습니다.
const line = new OrderLine(2.0)

// reaction 외부에서 `line.total`에 접근하면 매번 다시 계산됩니다.
setInterval(() => {
    console.log(line.total)
}, 60)
```

`keepAlive` 옵션을 사용하여 주석(annotation)을 설정하거나([직접 시도해보세요.](https://codesandbox.io/s/computed-3cjo9?file=/src/index.tsx)) 필요에 따라 나중에 깔끔하게 정리할 수 있는 `autorun(() => { someObject.someComputed })`을 만들어 오버라이딩함으로써 이를 해결할 수 있습니다.
두 가지 해결방법 다 메모리 누수가 발생할 위험이 있습니다. 여기서 기본 동작을 변경하는 것은 안티 패턴(anti-pattern) 입니다.

또한 MobX는 [`computedRequiresReaction`](configuration.md#computedrequiresreaction-boolean) 옵션을 사용하여 computed 값이 reaction 컨텍스트 외부에서 액세스 될 때 오류를 보고할 수 있습니다.

</details>

<details id="computed-setter"><summary>**Tip:** computed 값은 setter를 가질 수 있습니다.<a href="#computed-setter" class="tip-anchor"></a></summary>

computed 값에 대해 [setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set)를 정의할 수 있습니다. setter는 computed 속성의 값을 직접적으로 바꿀 수는 없지만,
파생의 "역"으로 사용할 수 있습니다. Setter는 자동으로 action으로 표시됩니다. 

예시:

```javascript
class Dimension {
    length = 2

    constructor() {
        makeAutoObservable(this)
    }

    get squared() {
        return this.length * this.length
    }
    set squared(value) {
        this.length = Math.sqrt(value)
    }
}
```

</details>

<details id="computed-struct"><summary>{🚀} **Tip:** 출력을 구조적으로 비교하기 위한 `computed.struct`<a href="#computed-struct" class="tip-anchor"></a></summary>

computed 값이 이전 계산과 구조적으로 동일한 출력일 때 observer에게 알릴 필요가 없다면 `computed.struct`를 사용하시면 됩니다. `computed.struct`는 observer에게 알리기 전에 참조가 같은지 확인하는 것이 아닌 구조적 비교부터 수행합니다.

예시:

```javascript
class Box {
    width = 0
    height = 0

    constructor() {
        makeObservable(this, {
            x: observable,
            y: observable,
            topRight: computed.struct
        })
    }

    get topRight() {
        return {
            x: this.width,
            y: this.height
        }
    }
}
```

기본적으로 `computed`의 결과는 참조에 의해 비교됩니다. 따라서 위의 예에서 `computed.struct`를 사용하지 않는다면 `topRight`는 항상 새 객체를 생성하므로 이전 출력과 동일한 것으로 간주하지 않습니다.

하지만, 위의 예시에서는 _실제로 `computed.struct`가 필요하지 않습니다_!
computed 값은 일반적으로 백업 값이 변경되는 경우에만 재평가됩니다.
따라서 `topRight`는 `width`와 `height` 변화에만 반응합니다.
이러한 변경사항이 있으면 다른 `topRight` 좌표를 얻을 수 있습니다. `computed.struct`는 캐시 적중이 없고 노력이 낭비되므로 필요하지 않습니다.

실제로 `computed.struct`는 들리는 것처럼 유용하지는 않습니다. 기본 observable의 변경이 여전히 동일한 출력으로 이어질 수 있는 경우에만 사용하세요. 예를 들어 좌표를 반올림하는 경우 기본값이 같지 않더라도 반올림 좌표는 이전에 반올림된 좌표와 같을 수 있습니다.

출력 변경 여부를 확인하는 사용자 지정 옵션은 [`equals`](#equals)를 확인하세요.

</details>

<details id="computed-with-args"><summary>{🚀} **Tip:** 인수를 사용한 computed 값<a href="#computed-with-args" class="tip-anchor"></a></summary>

getter는 인수를 사용하지 않지만, 인수를 필요로하는 파생 값으로 작업하기 위한 몇 가지 전략은 [여기](computeds-with-args.md)에서 확인해보세요.

</details>

<details id="standalone"><summary>{🚀} **Tip:** `computed(expression)`를 사용하여 독립형 computed 값 만들기<a href="#standalone" class="tip-anchor"></a></summary>

`computed`는 [`observable.box`](api.md#observablebox)가 독립 computed 값을 생성하는 것처럼 함수로 직접 호출할 수 있습니다.
반환된 객체에 `.get()`을 사용하여 computation의 현재 값을 가져옵니다.
이러한 형식의 `computed`는 자주 사용되지는 않지만 "박스화된(boxed)" 계산을 전달해야 하는 경우 유용할 수 있습니다. 이러한 사례 중 하나를 [여기](computeds-with-args.md)에서 확인할 수 있습니다.

</details>

## Options {🚀}

일반적으로 `computed`는 원하는 대로 즉시 사용할 수 있는 방식으로 동작하지만, `options` 인수를 전달하여 동작을 사용자 정의 할 수 있습니다.

### `name`

[Spy 이벤트 리스너](analyzing-reactivity.md#spy) 및 [MobX 개발자 도구](https://github.com/mobxjs/mobx-devtools)에서 디버그 이름으로 사용됩니다.

### `equals`

기본적으로 `comparer.default`로 설정되며, 이전 값과 다음 값을 비교하는 비교 함수 역할을 합니다. 그리고 만약 해당 함수가 값이 같다고 간주하면 observer는 재평가되지 않습니다.

해당 옵션은 다른 라이브러리의 구조 데이터 및 유형으로 작업할 때 유용합니다. 예를 들어, computed [moment](https://momentjs.com/) 인스턴스는 `(a, b) => a.isSame(b)`를 사용할 수 있습니다. `comparer.structural` 및 `comparer.shallow`는 구조 비교와 얕은 비교를 사용하여 새 값이 이전 값과 다른지 여부를 확인하고 그 결과를 observer에 알리는 경우에 유용합니다.

위의 [`computed.struct`](#computed-struct) 섹션을 확인해보세요.

#### 내장형 comparers

MobX는 `computed`의 `equals` 옵션 대부분을 충족해주는 네 가지 `comparer` 메서드를 기본으로 제공합니다.

-   `comparer.identity`는 두 값이 동일한지 확인하기 위해 항등 (`===`) 연산자를 사용합니다.
-   `comparer.default`는 `comparer.identity`와 같지만, `NaN`은 `NaN`과 같은 것으로 간주합니다.
-   `comparer.structural`는 두 값이 동일한지 확인하기 위해 심층 구조 비교를 수행합니다.
-   `comparer.shallow`는 두 값이 동일한지 확인하기 위해 얕은 구조 비교를 수행합니다.

`mobx`에서 `comparer`을 import 하여 위와 같은 메서드에 접근할 수 있습니다. 또한 reaction에 대해서도 사용할 수 있습니다.

### `requiresReaction`

연산 비용이 많이 드는 computed 값에 대해 `true`로 설정하는 것이 좋습니다. 반응 컨텍스트 외부에서 값을 읽으려고 하면 캐시 되지 않을 수 있으며 연산 비용이 많이 드는 재평가를 수행하는 대신 계산된 항목이 throw 됩니다.

### `keepAlive`

아무것도 관찰되지 않을 때 computed 값을 일시 중단하는 것을 방지합니다.(위 설명을 참고하세요.) [reactions](reactions.md#always-dispose-of-reactions)에 대해 논의된 것과 유사한 메모리 누수를 잠재적으로 발생시킬 수 있습니다.
