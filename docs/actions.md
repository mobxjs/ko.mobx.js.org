---
title: action을 이용한 state 업데이트
sidebar_label: Actions
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# action을 이용한 state 업데이트

사용 방법:

-   `action` _주석(annotation)_
-   `action(fn)`
-   `action(name, fn)`

모든 애플리케이션에는 action이 있습니다. action은 state를 수정하는 코드 조각입니다. 원칙적으로 action은 항상 이벤트에 대한 응답으로 발생합니다. 예를 들어 버튼 클릭, 일부 입력 변경, 웹 소켓 메시지 도착 등이 있습니다.

[`makeAutoObservable`](observable-state.md#makeautoobservable)를 통해 대부분의 action을 자동화 할 수 있지만, action을 따로 선언해야 합니다. 왜냐하면 이를 통해 코드 구조를 개선할 수 있고 아래와 같은 성능 이점을 제공하기 때문입니다.

1. [트랜잭션(transaction)](api.md#transaction) 내부에서 실행됩니다. 가장 바깥쪽 action이 완료될 때까지 reaction은 실행되지 않기 때문에, action 실행 중에 생성된 중간값 또는 불완전한 값은 action이 완료될 때까지 애플리케이션에서 볼 수 없습니다.

2. 기본적으로 action 외부에서 state를 변경할 수 없습니다. 이를 통해 코드에서 state 업데이트가 발생하는 위치를 명확히 확인할 수 있습니다.

`action` 주석은 state를 _수정하려는_ 함수에서만 사용해야 합니다. 정보(조회 또는 데이터 필터링)를 유도하는 함수는 MobX가 추적할 수 있도록 action으로 표시하면 안 됩니다. `action` 주석이 달린 멤버는 non-enumerable이 됩니다.

## 예시

<!--DOCUSAURUS_CODE_TABS-->
<!--makeObservable-->

```javascript
import { makeObservable, observable, action } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeObservable(this, {
            value: observable,
            increment: action
        })
    }

    increment() {
        // 중간 state는 observer에게 표시되지 않습니다.
        this.value++
        this.value++
    }
}
```

<!--makeAutoObservable-->

```javascript
import { makeAutoObservable } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeAutoObservable(this)
    }

    increment() {
        this.value++
        this.value++
    }
}
```

<!--action.bound-->

```javascript
import { makeObservable, observable, action } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeObservable(this, {
            value: observable,
            increment: action.bound
        })
    }

    increment() {
        this.value++
        this.value++
    }
}

const doubler = new Doubler()

// 해당 방법으로 increment를 호출하는 것은 이미 바인딩되어 있으므로 안전합니다.
setInterval(doubler.increment, 1000)
```

<!--action(fn)-->

```javascript
import { observable, action } from "mobx"

const state = observable({ value: 0 })

const increment = action(state => {
    state.value++
    state.value++
})

increment(state)
```

<!--runInAction(fn)-->

```javascript
import { observable, runInAction } from "mobx"

const state = observable({ value: 0 })

runInAction(() => {
    state.value++
    state.value++
})
```

<!--END_DOCUSAURUS_CODE_TABS-->

## `action` 함수를 이용한 함수 래핑

MobX의 트랜잭션 특성을 최대한 활용하려면 action을 최대한 외부로 전달해야 합니다. state를 수정하려는 경우 클래스 메서드를 action으로 표시하는 것이 좋습니다. 이벤트 핸들러는 가장 바깥쪽 트랜잭션이기 때문에 action으로 표시하는 것이 더 좋습니다. 그리고 두 개의 action을 호출하는 action 주석이 표시되지 않은 단일 이벤트 핸들러는 두 개의 트랜잭션을 생성합니다.

action 기반 이벤트 핸들러를 쉽게 만드는 데 도움이 되는 `action`은 주석 기능뿐만 아니라 고차 함수도 될 수 있습니다. 함수를 인수로 사용하여 호출할 수 있으며, 이러한 경우 동일한 특징을 가진 action 래핑 함수를 반환합니다.

예를 들어 React에서 `onClick` 핸들러는 아래와 같이 래핑 될 수 있습니다.

```javascript
const ResetButton = ({ formState }) => (
    <button
        onClick={action(e => {
            formState.resetPendingUploads()
            formState.resetValues()
            e.stopPropagation()
        })}
    >
        Reset form
    </button>
)
```

디버깅 목적으로 래핑 된 함수의 이름을 지정하거나 `action`에 대한 첫 번째 인수로 이름을 전달하는 것이 좋습니다.

<details id="actions-are-untracked"><summary>**메모:** 추적되지 않는 action<a href="#actions-are-untracked" class="tip-anchor"></a></summary>

action의 또 다른 특징은 [추적되지 않는다](api.md#untracked)는 것입니다. action이 부수효과 내부 또는 computed 값 내부에서 호출되면 action에서 읽은 observable 항목은 derivation의 종속성으로 계산되지 않습니다.

`makeAutoObservable`, `extendObservable` 및 `observable`은 `autoAction`이라는 특별한 `action`을 사용합니다. 
이 동작은 함수가 derivation인지 action인지를 런타임에 결정합니다.

</details>

## `action.bound`

사용 방법:

-   `action.bound` _주석(annotation)_

`action.bound` 주석을 사용하여 메서드를 적절한 인스턴스에 바인딩할 수 있음으로 `this`는 항상 함수 내에서 적절하게 바인딩 됩니다.

<details id="auto-bind"><summary>**Tip:** 모든 action과 flow를 자동으로 바인딩하려면 `makeAutoObservable(o, {}, { autoBind: true })`을 사용하세요.<a href="#avoid-bound" class="tip-anchor"></a></summary>

```javascript
import { makeAutoObservable } from "mobx"

class Doubler {
    value = 0

    constructor(value) {
        makeAutoObservable(this, {}, { autoBind: true })
    }

    increment() {
        this.value++
        this.value++
    }

    *flow() {
        const response = yield fetch("http://example.com/value")
        this.value = yield response.json()
    }
}
```

</details>

## `runInAction`

사용 방법:

-   `runInAction(fn)`

해당 유틸리티를 사용하여 즉시 호출되는 임시 action을 생성할 수 있습니다. 비동기 프로세스에서 유용하게 사용하실 수 있습니다. 
예를 들어 [위의 코드 블록](#examples)을 확인하세요.

## action 및 상속

**프로토타입에서** 정의된 action만 하위 클래스에서 **재정의(override)**할 수 있습니다.

```javascript
class Parent {
    // on instance
    arrowAction = () => {}

    // on prototype
    action() {}
    boundAction() {}

    constructor() {
        makeObservable(this, {
            arrowAction: action,
            action: action,
            boundAction: action.bound,
        })
    }
}
class Child extends Parent {
    // THROWS: TypeError: Cannot redefine property: arrowAction
    arrowAction = () => {}

    // OK
    action() {}
    boundAction() {}

    constructor() {
        super()
        makeObservable(this, {
            arrowAction: override,
            action: override,
            boundAction: override,
        })
    }
}
```

단일 _action_에 **바인딩**하기 위해 _화살표 함수_ 대신에 `this`와 `action.bound`를 사용할 수 있습니다.<br>
자세한 내용은 [**subclassing**](subclassing.md)을 확인하세요.

## 비동기 action

기본적으로 비동기 프로세스는 발생 시점과 상관없이 모든 reaction이 업데이트되기 때문에 MobX에서 특별한 처리가 필요하지 않습니다.
그리고 observable 객체는 mutable 하므로 action이 실행되는 동안 observable 객체에 대한 참조를 유지하는 것이 안전합니다.
하지만 비동기 프로세스에서 observable을 업데이트하는 모든 단계는 `action`으로 표시되어야 합니다.
아래에서 소개해 드리는 여러 가지 방법을 사용하여 `action`을 표시해보세요.

예를 들어 promise를 처리할 때 state를 업데이트하는 핸들러는 아래와 같이 `action`을 사용하여 래핑하거나 action이 되어야 합니다.

<!--DOCUSAURUS_CODE_TABS-->
<!--핸들러 `action`으로 감싸기-->

promise 해결 핸들러는 인라인으로 처리되지만 원래 작업이 완료된 후에 실행되므로 `action`으로 래핑해야 합니다.

```javascript
import { action, makeAutoObservable } from "mobx"

class Store {
    githubProjects = []
    state = "pending" // "pending", "done" or "error"

    constructor() {
        makeAutoObservable(this)
    }

    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(
            action("fetchSuccess", projects => {
                const filteredProjects = somePreprocessing(projects)
                this.githubProjects = filteredProjects
                this.state = "done"
            }),
            action("fetchError", error => {
                this.state = "error"
            })
        )
    }
}
```

<!--별도의 action을 통한 업데이트 처리-->

promise 핸들러가 클래스 필드라면 `makeAutoObservable`에 의해 action이 자동으로 래핑 됩니다.

```javascript
import { makeAutoObservable } from "mobx"

class Store {
    githubProjects = []
    state = "pending" // "pending", "done" or "error"

    constructor() {
        makeAutoObservable(this)
    }

    fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        fetchGithubProjectsSomehow().then(this.projectsFetchSuccess, this.projectsFetchFailure)
    }

    projectsFetchSuccess = projects => {
        const filteredProjects = somePreprocessing(projects)
        this.githubProjects = filteredProjects
        this.state = "done"
    }

    projectsFetchFailure = error => {
        this.state = "error"
    }
}
```

<!--async/await + runInAction-->

`await` 이후 단계가 동일하지 않기 때문에 action 래핑이 필요합니다. 
여기에서 `runInAction`을 활용할 수 있습니다.

```javascript
import { runInAction, makeAutoObservable } from "mobx"

class Store {
    githubProjects = []
    state = "pending" // "pending", "done" or "error"

    constructor() {
        makeAutoObservable(this)
    }

    async fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        try {
            const projects = await fetchGithubProjectsSomehow()
            const filteredProjects = somePreprocessing(projects)
            runInAction(() => {
                this.githubProjects = filteredProjects
                this.state = "done"
            })
        } catch (e) {
            runInAction(() => {
                this.state = "error"
            })
        }
    }
}
```

<!--`flow` + generator function -->

```javascript
import { flow, makeAutoObservable, flowResult } from "mobx"

class Store {
    githubProjects = []
    state = "pending"

    constructor() {
        makeAutoObservable(this, {
            fetchProjects: flow
        })
    }

    // 별 모양은 제너레이터(generator) 함수입니다!
    *fetchProjects() {
        this.githubProjects = []
        this.state = "pending"
        try {
            // await 대신 yield를 사용합니다.
            const projects = yield fetchGithubProjectsSomehow()
            const filteredProjects = somePreprocessing(projects)
            this.state = "done"
            this.githubProjects = filteredProjects
        } catch (error) {
            this.state = "error"
        }
    }
}

const store = new Store()
const projects = await flowResult(store.fetchProjects())
```

<!--END_DOCUSAURUS_CODE_TABS-->

## async·await 대신에 flow 사용하기 {🚀}

사용 방법:

-   `flow` _주석(annotation)_
-   `flow(function* (args) { })`

`flow` 래퍼는 MobX action을 더 쉽게 작업할 수 있게 `async`·`await` 대신 사용할 수 있는 옵션입니다.
`flow`는 [generator function](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Generator)을 유일한 입력으로 사용합니다.
generator 안에서 yield를 사용하여(`await somePromise` 대신 `yield somePromise`) promise를 연결할 수 있습니다.
flow 매커니즘은 promise가 해결될 때 generator가 계속 진행되거나 throw되는지 확인합니다.

따라서 `flow`는 `async`·`await`과 달리 `action` 래핑이 필요하지 않습니다. 이는 다음과 같이 적용될 수 있습니다.

1. 비동기 함수를 `flow`로 감쌉니다.
2. `async` 대신  `function *`을 사용합니다.
3. `await` 대신 `yield`를 사용합니다.

[`flow` + generator function](#asynchronous-actions) 예제에서 실제로 어떻게 사용하는지 확인해보세요.

`flowResult` 함수는 TypeScript를 사용할 때만 필요합니다.
메서드를 `flow`로 데코레이팅하기 때문에 반환된 generator는 promise로 래핑 됩니다.
그러나 TypeScript는 이러한 변환을 인식하지 못하기 때문에 `flowResult`를 사용하여 해당 변경을 인식할 수 있도록 해야 합니다.

`makeAutoObservable`은 자동으로 generator를 flow로 유추합니다. `flow` 주석이 달린 멤버는 non-enumerable이 됩니다.

<details id="flow-wrap"><summary>{🚀} **Note:** 객체 필드에 flow 사용하기<a href="#flow-wrap" class="tip-anchor"></a></summary>
`flow`는 `action`과 마찬가지로 함수를 직접 래핑할 수 있습니다. 위의 예시는 아래와 같이 작성될 수 있습니다.

```typescript
import { flow } from "mobx"

class Store {
    githubProjects = []
    state = "pending"

    fetchProjects = flow(function* (this: Store) {
        this.githubProjects = []
        this.state = "pending"
        try {
            // await 대신에 yield를 사용합니다.
            const projects = yield fetchGithubProjectsSomehow()
            const filteredProjects = somePreprocessing(projects)
            this.state = "done"
            this.githubProjects = filteredProjects
        } catch (error) {
            this.state = "error"
        }
    })
}

const store = new Store()
const projects = await store.fetchProjects()
```

`flowResult`가 더 이상 필요하지 않다는 장점이 있고, 타입을 올바르게 추론하기 위해 `this`가 필요하다는 단점이 있습니다.

</details>

## `flow.bound`

사용 방법:

-   `flow.bound` _주석(annotation)_

`flow.bound` 주석을 사용하여 메서드를 적절한 인스턴스에 자동으로 바인딩할 수 있음으로 `this`는 항상 함수 내에서 적절하게 바인딩 됩니다.
action과 유사하게 flow는 [`autoBind` 옵션](#auto-bind)을 사용하여 기본적으로 바인딩 할 수 있습니다.

## flow 취소 {🚀}

flow의 또 다른 장점은 취소할 수 있다는 것입니다.
`flow`의 반환 값은 generator 함수에서 반환된 값을 해결(resolve)하는 promise입니다.
반환된 promise는 실행 중인 generator를 취소할 수 있는 `cancel()` 메서드가 있습니다.
모든 `try`·`finally` 절은 계속 실행됩니다.

## 필수 action 비활성화 {🚀}

기본적으로 MobX 6 버전 이상에서는 action을 사용하여 state를 변경해야 합니다.
이러한 동작을 비활성화시키고 싶다면, [`enforceActions`](configuration.md#enforceactions) 섹션을 확인해주세요.
이러한 옵션은 경고의 가치가 덜 중요할 수도 있는 단위 테스트에서 유용할 수 있습니다.
