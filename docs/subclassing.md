---
title: 서브클래싱
sidebar_label: 서브클래싱
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 서브클래싱

서브클래싱은 몇 가지 [제한](#limitations)이 있습니다. 특히 **프로토타입의 action·flow·computed**만 오버라이드할 수 있으며 _[필드 선언](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes#field_declarations)_은 오버라이드 할 수 없습니다. 서브클래스에서 오버라이드 된 메서드와 getter에 `override` 주석을 사용합니다. 아래 예를 살펴보세요. 일을 단순하게 유지하고 상속보다 구성(composition)을 선호하도록 하세요.

```javascript
import { makeObservable, observable, computed, action, override } from "mobx"

class Parent {
    // 주석이 달린 인스턴스 필드는 오버라이드 할 수 없습니다.
    observable = 0
    arrowAction = () => {}

    // 주석이 달리지 않은 인스턴스 필드는 오버라이드 할 수 있습니다.
    overridableArrowAction = action(() => {})

    // 주석이 달린 메서드와 getter는 오버라이드 할 수 있습니다.
    action() {}
    actionBound() {}
    get computed() {}

    constructor(value) {
        makeObservable(this, {
            observable: observable,
            arrowAction: action,
            action: action,
            actionBound: action.bound,
            computed: computed,
        })
    }
}

class Child extends Parent {
    /* --- 상속됨 --- */
    // THROWS - TypeError: Cannot redefine property
    // observable = 5
    // arrowAction = () = {}

    // 주석이 없기 때문에 괜찮습니다.
    overridableArrowAction = action(() => {})

    // 프로토타입이기 때문에 괜찮습니다.
    action() {}
    actionBound() {}
    get computed() {}

    /* --- 새로운 속성 --- */
    childObservable = 0;
    childArrowAction = () => {}
    childAction() {}
    childActionBound() {}
    get childComputed() {}

    constructor(value) {
        super()
        makeObservable(this, {
            // 상속됨
            action: override,
            actionBound: override,
            computed: override,
            // 새로운 속성
            childObservable: observable,
            childArrowAction: action,
            childAction: action,
            childActionBound: action.bound,
            childComputed: computed,
        })
    }
}
```

## 제한 사항

1. **프로토타입**에 정의된 `action`, `computed`, `flow`, `action.bound`만 서브클래스에서 **오버라이드**할 수 있습니다.
1. 필드는 서브클래스에서 다시 주석을 달 수 없습니다. `override`는 예외적으로 가능합니다.
1. `makeAutoObservable`은 서브클래싱을 지원하지 않습니다.
1. 내장 확장(`ObservableMap`, `ObservableArray` 등)은 지원하지 않습니다.
1. 서브클래스에서 `makeObservable`에 다른 옵션을 제공할 수 없습니다.
1. 단일 상속 체인에서 annotations·decorators를 같이 사용할 수 없습니다.
1. [다른 모든 제한 사항도 적용됩니다.](observable-state.html#limitations)

### `TypeError: Cannot redefine property`

이러한 에러를 보게 된다면, 아마도 서브클래스에서 `x = () => {}`처럼 **화살표 함수를 오버라이드**하려고 했을 것입니다. 클래스의 **모든 주석 필드**를 **구성할 수 없기** 때문에 해당 작업은 불가능합니다. [제한 사항을 참고하세요](observable-state.md#limitations). 두 가지 옵션이 있습니다.

<details><summary>1. 함수를 프로토타입으로 이동하고 `action.bound` 주석을 사용합니다.</summary>

```javascript
class Parent {
    // action = () => {};
    // =>
    action() {}

    constructor() {
        makeObservable(this, {
            action: action.bound
        })
    }
}
class Child {
    action() {}

    constructor() {
        super()
        makeObservable(this, {
            action: override
        })
    }
}
```

</details>
<details><summary>2. `action`주석을 제거하고 수동으로 action을 사용하여 함수를 래핑합니다. `x = action(() => {})`</summary>

```javascript
class Parent {
    // action = () => {};
    // =>
    action = action(() => {})

    constructor() {
        makeObservable(this, {}) // <-- 주석 제거됨
    }
}
class Child {
    action = action(() => {})

    constructor() {
        super()
        makeObservable(this, {}) // <-- 주석 제거됨
    }
}
```

</details>
