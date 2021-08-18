---
title: Intercept & Observe
sidebar_label: Intercept & Observe {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# Intercept & Observe {🚀}

_⚠️ **경고**: intercept와 observe는 로우 레벨의 유틸리티이므로 실제로는 필요하지 않습니다. `observe`는 트랜잭션을 존중하지 않고 변경사항에 대한 깊은 관찰(observing)을 지원하지 않으므로 `observe` 대신 [reaction](reactions.md) 방식을 사용하세요. 이러한 유틸리티를 사용하는 것은 안티 패턴입니다. 이전 값과 새 값에 액세스하려면 `observe` 대신 [`reaction`](reactions.md#reaction)을 사용하세요.⚠️_

`observe`와 `intercept`는 단일 observable의 변경을 모니터링하는 데 사용할 수 있지만 중첩된 observable을 추적하지는 **않습니다**.

-   `intercept`는 mutation을 observable(검증, 정규화 또는 취소)에 적용하기 전에 감지하고 수정하는 데 사용할 수 있습니다.
-   `observe`는 변경사항이 만들어진 후 이를 intercept 할 수 있도록 해줍니다.

## Intercept

사용 방법: `intercept(target, propertyName?, interceptor)`

_이 API는 가능한 사용하지 마세요. 기본적으로 약간의 관점 지향 프로그래밍(aspect-oriented programming)을 제공하여 디버깅하기 어려운 흐름을 만듭니다. 대신 state를 업데이트하는 동안이 아닌 업데이트하기 **전에** 데이터 유효성 검사와 같은 작업을 수행합니다._

-   `target`: 감시하는 observable.
-   `propertyName`: intercept 할 특정 속성을 지정하는 선택적 매개 변수입니다. `intercept(user.name, interceptor)`는 `intercept(user, "name", interceptor)`와 근본적으로 다릅니다. 전자는 `user.name` 내부의 _현재_ `value`에 interceptor를 추가하려고 시도하며, 해당 값은 절대 observable이 아닙니다. 후자는 `user`의 `name` _속성_에 대한 변경 사항을 intercept 합니다.
-   `interceptor`: observable에 발생하는 _각각의_ 변경 사항에 대해 호출되는 콜백입니다. mutation을 설명하는 단일 변경 객체를 받습니다.

`intercept`는 MobX에게 현재 변경사항과 관련하여 필요한 사항을 알려야 합니다. 
따라서 다음 중 하나를 수행해야 합니다.

1. 함수에서 받은 `change` 객체를 그대로 반환합니다. 이 경우 mutation이 적용됩니다.
2. `change` 객체를 수정하고 반환합니다(예: 데이터 정규화). 일부 필드는 수정할 수 없습니다. 아래를 참고하세요.
3. `null`을 반환합니다. 이는 변경사항이 무시될 수 있으며 해당 변경 사항을 적용해서는 안 된다는 것을 나타냅니다. 이것은 예를 들면 객체를 일시적으로 불변(immutable) 하게 만드는 것과 같이 강력한 개념입니다.
4. 예외를 throw 합니다. 예를 들어 일부 무공변성(invariant)이 충족되지 않는 경우에 해당합니다.

이 함수는 호출될 때 interceptor를 취소하는 데 사용할 수 있는 `disposer` 함수를 반환합니다. 여러 개의 interceptor를 동일한 observable에 등록할 수 있습니다. 해당 interceptor들은 등록 순서대로 연결됩니다. interceptor 중 하나가 `null`을 반환하거나 예외를 throw 하면 다른 interceptor는 더 이상 평가되지 않습니다. 상위 객체와 개별 속성 모두에 interceptor를 등록할 수도 있습니다. 이 경우 상위 객체 interceptor가 속성 interceptor보다 먼저 실행됩니다.

```javascript
const theme = observable({
    backgroundColor: "#ffffff"
})

const disposer = intercept(theme, "backgroundColor", change => {
    if (!change.newValue) {
        // background color 설정을 해제하려는 시도를 무시합니다.
        return null
    }
    if (change.newValue.length === 6) {
        // 누락된 '#'를 붙입니다.
        change.newValue = "#" + change.newValue
        return change
    }
    if (change.newValue.length === 7) {
        // 올바른 형식의 색상 코드여야 합니다!
        return change
    }
    if (change.newValue.length > 10) {
        // 이후의 변경사항에 대한 intercept를 중단합니다.
        disposer()
    }
    throw new Error("This doesn't look like a color at all: " + change.newValue)
})
```

## Observe

사용 방법: `observe(target, propertyName?, listener, invokeImmediately?)`

_상단의 경고를 참고하시기 바랍니다. 이 API 대신 [`reaction`](reactions.md#reaction)을 사용하세요._

-   `target`: 관찰하려는 observable.
-   `propertyName`: 관찰할 특정 속성을 지정하는 선택적 매개 변수입니다. `observe(user.name, listener)`는 `observe(user, "name", listener)`와 근본적으로 다릅니다. 전자는 `user.name` 내의 _현재_ `value`을 관찰하며 해당 값은 절대  observable이 아닙니다. 후자는 `user`의 `name` _속성_을 관찰합니다.
-   `listener`: observable에 발생하는 _각각의_ 변경 사항에 대해 호출되는 콜백입니다. mutation을 설명하는 단일 변경 객체를 받으며, boxed observable은 제외합니다. boxed observable은 `newValue, oldValue` 두 가지 매개 변수를 받는 `listener`를 호출합니다.
-   `invokeImmediately`: 기본적으로 _false_입니다. `observe`이 첫 번째 변경사항을 기다리는 대신 observable의 state로 직접 `listener`를 호출하도록 하려면 이 값을 _true_로 설정합니다. 일부 타입의 observable에서는 아직 지원되지 않습니다.

이 함수는 observer를 취소하는 데 사용할 수 있는 `disposer` 함수를 반환합니다. `트랜잭션`은 `observe` 메서드의 작동에 영향을 주지 않습니다. 
이는 트랜잭션 안에서도 `observe`가 각 mutation에 대한 listener를 실행한다는 것을 의미합니다. 따라서 [`autorun`](reactions.md#autorun)이 일반적으로 `observe`보다 더 강력하고 선언적인 대안입니다.

_`observe`는 **mutation**이 만들어질 때 반응하는 반면 `autorun`이나 `reaction`과 같은 reaction은 **새로운 값**이 사용 가능해질 때 해당 값에 반응합니다. 대부분의 경우 후자만으로 충분합니다._

예시:

```javascript
import { observable, observe } from "mobx"

const person = observable({
    firstName: "Maarten",
    lastName: "Luther"
})

// 모든 필드를 관찰합니다.
const disposer = observe(person, change => {
    console.log(change.type, change.name, "from", change.oldValue, "to", change.object[change.name])
})

person.firstName = "Martin"
// 출력 값: 'update firstName from Maarten to Martin'

// 이후에 발생하는 모든 업데이트를 무시합니다.
disposer()

// 단일 필드를 관찰합니다.
const disposer2 = observe(person, "lastName", change => {
    console.log("LastName changed to ", change.newValue)
})
```

관련 포스트: [Object.observe is dead. Long live mobx.observe](https://medium.com/@mweststrate/object-observe-is-dead-long-live-mobservable-observe-ad96930140c5)

## 이벤트 오버뷰

`intercept`와 `observe`의 콜백은 적어도 다음 속성을 가진 이벤트 객체를 받습니다.

-   `object`: 이벤트를 트리거하는 observable
-   `debugObjectName`: (디버깅을 위해) 이벤트를 트리거하는 observable의 이름
-   `observableKind`: observable 타입(value, set, array, object, map, computed)
-   `type` (문자열): 현재 이벤트의 타입

타입별로 사용할 수 있는 추가 필드입니다.

| Observable 타입              | 이벤트 타입 | 속성     | 설명                                                                                       | intercept하는 동안 사용가능 여부 | intercept로부터 수정 가능 여부 |
| ---------------------------- | ---------- | ------------ | ------------------------------------------------------------------------------------------------- | -------------------------- | ---------------------------- |
| Object                       | add        | name         | 추가할 속성의 이름                                                                 | √                          |                              |
|                              |            | newValue     | 할당할 새로운 값                                                                     | √                          | √                            |
|                              | update\*   | name         | 업데이트 할 속성의 이름                                                               | √                          |                              |
|                              |            | newValue     | 할당할 새로운 값                                                                     | √                          | √                            |
|                              |            | oldValue     | 대체된 값                                                                       |                            |                              |
| Array                        | splice     | index        | splice의 시작 인덱스. splice는 `push`, `unshift`, `replace` 등에 의해서도 실행됩니다.        | √                          |                              |
|                              |            | removedCount | 삭제할 요소의 갯수                                                                    | √                          | √                            |
|                              |            | added        | 추가할 요소가 있는 배열                                                                     | √                          | √                            |
|                              |            | removed      | 삭제된 요소가 있는 배열                                                               |                            |                              |
|                              |            | addedCount   | 추가된 요소의 갯수                                                                  |                            |                              |
|                              | update     | index        | 업데이트할 단일 엔트리의 인덱스                                                          | √                          |                              |
|                              |            | newValue     | 할당되었거나 할당할 새로운 값                                                          | √                          | √                            |
|                              |            | oldValue     | 대체된 이전 값                                                                  |                            |                              |
| Map                          | add        | name         | 추가된 엔트리의 이름                                                             | √                          |                              |
|                              |            | newValue     | 할당할 새로운 값                                                             | √                          | √                            |
|                              | update     | name         | 업데이트할 엔트리의 이름                                                              | √                          |                              |
|                              |            | newValue     | 할당할 새로운 값                                                             | √                          | √                            |
|                              |            | oldValue     | 대체된 값                                                                 |                            |                              |
|                              | delete     | name         | 삭제된 엔트리의 이름                                                              | √                          |                              |
|                              |            | oldValue     | 삭제된 엔트리의 값                                                          |                            |                              |
| Boxed & computed observables | create     | newValue     | 생성될 때 할당된 값. boxed observable에서는 `spy` 이벤트로서만 가능합니다. |                            |                              |
|                              | update     | newValue     | 할당할 새로운 값                                                                     | √                          | √                            |
|                              |            | oldValue     | observable의 이전 값                                                             |                            |                              |

**참고:** 객체 `update` 이벤트는 업데이트된 computed 값에 대해 실행되지 않습니다(해당 computed 값은 mutation이 아니므로). 그러나 `observe(object, 'computedPropertyName', listener)`를 사용하여 특정 속성을 명시적으로 구독하면 이러한 computed 값을 관찰할 수 있습니다.
