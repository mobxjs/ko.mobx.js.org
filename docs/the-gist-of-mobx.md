---
title: MobX의 요지
sidebar_label: MobX의 요지
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# MobX의 요지

## 개념

MobX는 어플리케이션에서 다음 세 가지 개념을 구분합니다.

1. 상태(state)
2. 동작(action)
3. 파생(derivation)

세 가지 개념에 대해 아래의 내용을 자세히 살펴보거나, [10분만에 알아보는 MobX와 React](https://ko.mobx.js.org/getting-started.html)에서 단계별로 심층적으로 살펴보고 간단한 Todo list 앱을 만들어보세요.

### 1. 상태(state)를 정의하고 관찰 가능하게(observable) 만들기

_State_는 애플리케이션을 구동하는 데이터입니다.
일반적으로 todo 아이템과 같은 _도메인 별 state_가 있고 현재 선택된 요소와 같은 _view state_가 있습니다.
State는 값을 보유하고 있는 스프레드시트 셀과 같습니다.

원하는 데이터 구조(일반 객체, 배열, 클래스, 순환 데이터 구조 또는 참조)에 state를 저장합니다. 어떤 데이터 구조를 사용하든 MobX의 작동에는 문제가 되지 않습니다.
단지 시간이 지남에 따라 변경하려는 모든 속성을 MobX가 추적할 수 있도록 `observable`로 표시하면 됩니다.

여기 간단한 예제가 있습니다.

```javascript
import { makeObservable, observable, action } from "mobx"

class Todo {
    id = Math.random()
    title = ""
    finished = false

    constructor(title) {
        makeObservable(this, {
            title: observable,
            finished: observable,
            toggle: action
        })
        this.title = title
    }

    toggle() {
        this.finished = !this.finished
    }
}
```

**Hint**: [`makeAutoObservable`](observable-state.md)을 사용하면 위 예제를 더 단순화 할 수 있지만, 이처럼 명시적으로 표시함으로써 다른 개념을 더 자세히 보여드리고 있습니다.

`observable`을 사용하는 것은 객체의 속성을 스프레드시트 셀로 바꾸는 것과 같습니다.
스프레드시트와 다른 점은 들어갈 수 있는 값이 원시 값(primitive values)뿐만 아니라 참조(reference), 객체 및 배열일 수도 있습니다.

`action`으로 표시된 `toggle`은 어떨까요?

### 2. action을 이용한 state 업데이트

_action_은 사용자 이벤트, 백엔드 데이터 푸시, 예약된 이벤트 등과 같이 _state_를 변경하는 코드 조각입니다.
action은 스프레드시트 셀에 새 값을 입력하는 사용자와 같습니다.

위의 `Todo` 예제에서 `finished` 값을 바꾸는 `toggle`을 확인할 수 있으며, `finished`는 `observable`로 표시되어 있습니다. 위의 예시처럼 `observable`을 변경하는 코드는 [`action`](actions.md)으로 표시하는 것이 좋습니다. 이렇게 하면 MobX가 트랜잭션을 자동으로 적용하여 성능을 쉽게 최적화할 수 있습니다.

action을 사용하면 코드를 구조화하는 데 도움을 줄 수 있으며 의도하지 않은 state 변경도 방지할 수 있습니다.
state를 변경하는 메서드는 MobX 용어로 _action_이라고 합니다. 현재의 state를 기반으로 새로운 정보를 계산하는 _view_와는 대조적입니다.
모든 메서드는 이 두 가지 목표 중 하나에 기여해야 합니다.

### 3. 상태(state) 변화에 자동으로 응답하는 파생(derivation) 만들기

state에서 더 이상의 상호작용 없이 파생될 수 있는 _모든 것_이 derivation 입니다.
derivation은 다음과 같이 다양한 형태로 존재할 수 있습니다.

-   _사용자 인터페이스_
-   남은 `todos`의 수와 같은 _파생 데이터_
-   _백엔드 통합_ (예시: 서버에 변경사항 전송)

MobX는 다음과 같이 두 종류로 derivation을 구분합니다.

-   _computed 값_ : 현재의 observable state 에서 순수 함수를 사용하여 파생될 수 있는 값
-   _reaction_ : state가 변경될 때 자동으로 발생해야 하는 부수효과 (명령형 프로그래밍과 반응형 프로그래밍 사이를 연결해주는 다리 역할)

MobX를 시작할 때 reaction을 과도하게 사용하는 경향이 있습니다.
가장 좋은 방식은 현재 state를 기반으로 값을 생성하려는 경우에 항상 `computed`를 사용하는 것입니다.

#### 3.1. computed를 사용하여 파생된 값 모델링하기

_computed_ 값을 생성하려면 JS getter 함수 `get`을 사용하여 속성을 정의하고 `makeObservable`을 사용하여 `computed`로 표시합니다.

```javascript
import { makeObservable, observable, computed } from "mobx"

class TodoList {
    todos = []
    get unfinishedTodoCount() {
        return this.todos.filter(todo => !todo.finished).length
    }
    constructor(todos) {
        makeObservable(this, {
            todos: observable,
            unfinishedTodoCount: computed
        })
        this.todos = todos
    }
}
```

MobX는 todo가 추가되거나 `finished` 속성 중 하나가 수정될 때 `unfinishedTodoCount`를 자동으로 업데이트합니다.

이러한 계산은 MS Excel과 같은 스프레드시트 프로그램의 공식과 유사합니다. 자동으로 업데이트되지만 필요할 때(무언가 결과에 영향을 미칠 수 있을 때)만 업데이트됩니다.

#### 3.2. reaction을 사용하여 부수효과(side effect) 모델링하기

사용자가 화면에서 state의 변화나 computed 값의 변화를 볼 수 있으려면 GUI의 일부를 다시 그리는 _reaction_이 필요합니다.

reaction은 computed 값과 유사하지만, 정보를 생성하는 대신 콘솔 출력, 네트워크 요청, DOM 패치 적용을 위해 React 컴포넌트 트리를 점진적으로 업데이트하는 등의 부수효과를 생성합니다.

간단히 말해, reaction은 [반응형](https://en.wikipedia.org/wiki/Reactive_programming) 프로그래밍과 [명령형](https://ko.wikipedia.org/wiki/%EB%AA%85%EB%A0%B9%ED%98%95_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D) 프로그래밍의 세계를 연결합니다.

지금까지 가장 많이 사용되는 reaction 형태는 UI 컴포넌트입니다.
action과 reaction 모두 부수효과를 일으킬 수 있습니다.
form을 제출할 때 네트워크 요청을 하는 것처럼, 트리거 될 수 있는 명확하고 명시적인 
출처가 있는 부수효과는 관련 이벤트 핸들러에서 명시적으로 트리거 되어야 합니다.

#### 3.3. 반응형 리액트 컴포넌트

React를 사용하는 경우 [설치 중에 선택한](installation.md#installation) 바인딩 패키지에서 [`observer`](react-integration.md) 함수를 이용하여 컴포넌트를 감싸서 반응형으로 만들 수 있습니다. 해당 예제에서는 더 가벼운 `mobx-react-lite` 패키지를 사용합니다.

```javascript
import * as React from "react"
import { render } from "react-dom"
import { observer } from "mobx-react-lite"

const TodoListView = observer(({ todoList }) => (
    <div>
        <ul>
            {todoList.todos.map(todo => (
                <TodoView todo={todo} key={todo.id} />
            ))}
        </ul>
        Tasks left: {todoList.unfinishedTodoCount}
    </div>
))

const TodoView = observer(({ todo }) => (
    <li>
        <input type="checkbox" checked={todo.finished} onClick={() => todo.toggle()} />
        {todo.title}
    </li>
))

const store = new TodoList([new Todo("Get Coffee"), new Todo("Write simpler code")])
render(<TodoListView todoList={store} />, document.getElementById("root"))
```

`observer`는 리액트 컴포넌트를 렌더링하는 데이터의 derivation으로 변환합니다.
MobX를 사용하면 smart 컴포넌트나 dumb 컴포넌트의 구분이 없습니다.
모든 컴포넌트는 smart하게 렌더링 되지만, dumb하게 정의됩니다. MobX에서는 필요할 때마다 컴포넌트가 다시 렌더링 되며, 그 이상은 렌더링 되지 않습니다.

따라서 위 예제의 `onClick` 핸들러는 `toggle` action을 사용할 때 적절한 `TodoView` 컴포넌트를 강제로 다시 렌더링하지만, `TodoListView` 컴포넌트는 완료되지 않은 작업의 수(unfinishedTodoCount)가 변경된 경우에만 다시 렌더링 됩니다.
`Tasks left`줄을 지우거나 별도의 컴포넌트에 넣으면 Todo를 체크할 때 `TodoListView` 컴포넌트는 더 이상 다시 렌더링 되지 않습니다.

React가 MobX와 어떻게 작동하는지 자세히 알아보려면 [React 통합](react-integration.md) 섹션을 확인해보세요.

#### 3.4. 커스텀 reaction

거의 필요하지는 않지만 [`autorun`](reactions.md#autorun),
[`reaction`](reactions.md#reaction), [`when`](reactions.md#when) 함수를 사용하여 상황에 맞게 만들 수 있습니다.
예를 들어, `autorun`은 `unfinishedTodoCount`의 수가 변경될 때마다 로그 메시지를 출력합니다.

```javascript
// state를 자동으로 관찰하는 함수입니다.
autorun(() => {
    console.log("Tasks left: " + todos.unfinishedTodoCount)
})
```

`unfinishedTodoCount`가 변경될 때마다 새 메시지를 출력하는 이유는 무엇일까요? 답은 아래의 원칙에 있습니다.

_MobX는 추적된 함수를 실행하는 동안 읽은 기존의 observable 속성에 반응합니다._

MobX가 반응해야 하는 observable 항목을 결정하는 방법에 대해 자세히 알아보려면 [반응성 이해하기](understanding-reactivity.md) 섹션을 확인해보세요.

## 원칙

MobX는 _action_이 _state_를 변경하는 단방향 데이터 흐름을 사용하여 영향을 받는 모든 _view_를 업데이트합니다.

![Action, State, View](assets/action-state-view.png)

1. _state_가 변경되면 모든 _derivation_이  **자동으로** 그리고 **원자 단위로** 업데이트됩니다. 결과적으로 중간 값을 관찰할 수는 없습니다.

2. 기본적으로 모든 _derivation_은 **동기식**으로 업데이트 됩니다. 이는 _action_이 _state_를 변경한 직후에 computed 값을 안전하게 검사할 수 있음을 의미합니다.

3. _computed 값_은 **느리게** 업데이트됩니다. 활발히 사용되지 않는 계산된 값은 부수효과(I/O)에 필요할 때까지 업데이트되지 않습니다.
   만약 view가 계산된 값을 사용하지 않으면 가비지가 자동으로 수집됩니다.

4. 모든 _computed 값_은 **순수**해야 하며 _state_를 바꾸면 안 됩니다.

이러한 배경이 생기게 된 맥락에 대해 더 알고 싶다면 [MobX의 기본 원칙](https://hackernoon.com/the-fundamental-principles-behind-mobx-7a725f71f3e8)을 확인하세요.

## 직접 시도하기

[CodeSandbox](https://codesandbox.io/s/concepts-principles-il8lt?file=/src/index.js:1161-1252)에서 위의 예제를 직접 해볼 수 있습니다.

## Linting

MobX의 멘탈 모델을 채택하기 어렵다면 매우 엄격하게 구성하고 이러한 패턴을 벗어날 때 런타임에 경고하세요. [linting MobX](configuration.md#linting-options) 섹션을 확인해보세요.
