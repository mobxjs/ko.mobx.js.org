---
title: 데코레이터 사용하기
sidebar_label: 데코레이터 사용하기 {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 데코레이터 사용하기 {🚀}

MobX 6 이전에는 `observable`, `computed`, `action`을 표시하기 위해 ES.next 데코레이터를 사용하도록 권장했습니다. 그러나 데코레이터는 현재 ES 표준이 아니며 표준화 과정에도 오랜 시간이 소요되고 있습니다. 또한 표준화되는 데코레이터는 기존의 시행되었던 방식과 다를 것으로 보입니다. MobX 6에서는 호환성을 위해 데코레이터에서 벗어나 [`makeObservable` / `makeAutoObservable`](observable-state.md)을 사용할 것을 권장합니다.

그러나 기존의 많은 코드베이스와 온라인 문서 및 튜토리얼 자료에서 데코레이터를 사용하고 있습니다. `observable`, `action`, `computed`와 같이 `makeObservable`의 주석으로 사용할 수 있는 것은 무엇이든 데코레이터로 사용할 수 있다는 것이 규칙입니다. 예시로 구체적인 형태를 살펴봅시다.

```javascript
import { makeObservable, observable, computed, action } from "mobx"

class Todo {
    id = Math.random()
    @observable title = ""
    @observable finished = false

    constructor() {
        makeObservable(this)
    }

    @action
    toggle() {
        this.finished = !finished
    }
}

class TodoList {
    @observable todos = []

    @computed
    get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length
    }

    constructor() {
        makeObservable(this)
    }
}
```

MobX 6 이전에는 생성자에서 `makeObservable(this)` 호출이 요구되지 않았지만 6버전 이후로는 다릅니다. 해당 호출을 통해 데코레이터 구현이 더 간단해지고 호환성이 높아졌기 때문입니다.
이는 MobX가 데코레이터 정보에 따라 인스턴스를 observable로 설정하도록 지시합니다. 데코레이터는 `makeObservable`의 두 번째 인수를 대신합니다.

우리는 이러한 형태로 계속해서 데코레이터를 지원할 계획입니다.
기존의 MobX 4/5 코드베이스는 [code-mod](https://www.npmjs.com/package/mobx-undecorate))를 통해 `makeObservable` 호출을 사용하도록 마이그레이션 할 수 있습니다.
MobX 4/5에서 6으로 마이그레이션할 때 필요한 `makeObservable` 호출이 생성되도록 항상 code-mod를 실행하는 것이 좋습니다.

[MobX 4/5에서 마이그레이션 하기 {🚀}](migrating-from-4-or-5.md) 섹션을 확인해보세요.

## `observer`를 데코레이터로 사용하기

`mobx-react`의 `observer` 함수는 클래스 컴포넌트에서 사용할 수 있는 함수이자 데코레이터입니다.

```javascript
@observer
class Timer extends React.Component {
    /* ... */
}
```

## 데코레이터 지원 활성화하기

MobX를 사용하는 새로운 코드베이스는 언어의 공식 파트가 될 때까지 데코레이터를 사용하는 것을 권장하지 않지만, 사용할 수는 있습니다. 변환을 위한 설정이 필요하므로 Babel 또는 TypeScript를 사용해야 합니다.

### TypeScript

`tsconfig.json`에서 `"experimentalDecorators": true`와 `"useDefineForClassFields": true` 컴파일러 옵션을 활성화하세요.

### Babel 7

`npm i --save-dev @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators`로 데코레이터 지원 패키지를 설치한 후 `.babelrc` 파일에서 활성화하세요.(반드시 순서를 지켜주세요.)

```javascript
{
    "plugins": [
        ["@babel/plugin-proposal-decorators", { "legacy": true }],
        ["@babel/plugin-proposal-class-properties", { "loose": false }]
        // MobX 4/5에서와 반대로, "loose"가 false여야 합니다!       ^
    ]
}
```

### 데코레이터 구문과 Create React App (v2)

`create-react-app@^2.1.1` 이상에서 TypeScript를 사용하는 경우에만 데코레이터가 지원됩니다. 이전 버전이나 vanilla JavaScript를 사용할 때는 eject 또는 [customize-cra](https://github.com/arackaf/customize-cra) 패키지를 사용합니다.

## 경고: 데코레이터 구문의 한계

_데코레이터 구문의 현재 트랜스파일러 구현은 상당히 제한적이며 정확히 동일하게 동작하지 않습니다. 또한 2단계 프로포절(proposal)이 모든 트랜스파일러에 의해 시행되기 전까지는 많은 구성 패턴들이 데코레이터와 함께 사용 불가합니다. 이러한 이유로 MobX의 현재 데코레이터 구문 지원은 지원되는 기능이 모든 환경에서 일관되게 동작하는 범위로 설정되어 있습니다._

다음 패턴은 MobX에서 공식적으로 지원되지 않습니다.

-   상속 트리에서 decorate 된 클래스 멤버를 다시 정의하기
-   static 클래스 멤버 decorate 하기
-   MobX에서 제공하는 데코레이터와 다른 데코레이터 결합하기
-   HMR(Hot Module Reloading)∙React-hot-loader는 예상대로 작동하지 않을 수 있습니다.
