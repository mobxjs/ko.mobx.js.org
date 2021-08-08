---
title: 데이터 스토어 정의
sidebar_label: 데이터 스토어 정의
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 데이터 스토어 정의

해당 섹션에서는 Mendix에서 MobX를 사용하면서 발견한 대규모 프로젝트를 유지할 수 있게 만들어주는 몇 가지 모범사례를 소개합니다.
해당 섹션은 저의 의견이며 이러한 관행을 적용 안 하셔도 됩니다.
MobX 및 React를 사용하는 방법에는 여러 가지가 있으며 이는 그중 하나일 뿐입니다.

해당 섹션에서는 기존 코드 베이스와 클래식 MVC 패턴에서 잘 작동하는 MobX로 작업하는 방법에 대해 중점을 뒀습니다. 스토어를 구성하는 다른 방법으로 [mobx-state-tree](https://github.com/mobxjs/mobx-state-tree) 그리고 [mobx-keystone](https://mobx-keystone.js.org/)이 있습니다. 둘 다 모두 구조적으로 공유된 스냅샷, action 미들웨어, JSON 패치 지원 등 다양한 기능을 제공합니다.

## 스토어

스토어는 모든 Flux 아키텍처에서 찾을 수 있으며 MVC 패턴의 컨트롤러와는 약간 다릅니다. 
스토어의 주요 책임은 컴포넌트의 _로직_과 _state_를 프론트엔드 및 백엔드에서 사용할 수 있고 독립으로 테스트가 가능한 단위로 만드는 것입니다.

대부분의 애플리케이션에서 적어도 두 개의 저장소(_도메인 state 저장소_와 _UI state 저장소_)가 있으면 좋습니다. 이 둘을 분리함으로써 _도메인 state_를 전체적으로 재사용하고 테스트할 수 있는 장점이 있고, 다른 애플리케이션에서 재사용할 수 있습니다.

## 도메인 스토어

애플리케이션에 _도메인_ 스토어가 하나 이상 존재할 것입니다.
스토어에는 애플리케이션의 모든 데이터(Todo items, users, books, movies, orders, name 등)가 저장됩니다.
애플리케이션에 도메인 스토어가 하나 이상 있을 가능성이 높습니다.

하나의 도메인 스토어는 애플리케이션의 하나의 개념을 담당해야 합니다. 하나의 도메인 스토어는 종종 
여러 도메인 객체가 내부에 있는 트리 구조로 구성됩니다.

예를 들어, 상품에 관한 도메인 스토어 하나와 주문 및 주문 정보에 관한 스토어가 있다고 가정합니다. 
경험상 두 항목이 포함 관계인 경우 일반적으로 동일한 저장소에 있어야 합니다.
즉, 스토어는 _도메인 객체_를 관리합니다.

스토어의 책임은 다음과 같습니다.

-   도메인 객체를 인스턴스화 합니다. 도메인 객체가 자신이 속한 저장소를 알고 있는지 확인합니다.
-   각 도메인 객체의 인스턴스가 하나만 있는지 확인합니다.
    동일한 사용자(user), 주문(order) 또는 할 일(todo)이 메모리에 두 번 저장되어서는 안 됩니다.
    이렇게 하면 참조를 안전하게 사용할 수 있으며 참조를 확인할 필요 없이 최신 인스턴스를 보고 있는지 확인할 수 있습니다.
    이러한 방식은 디버깅할 때 빠르고 간단하며 편리합니다.
-   백엔드 통합을 제공해야 합니다. 필요할 때 데이터를 저장합니다.
-   백엔드에서 업데이트 내용을 받은 경우 기존 인스턴스를 업데이트합니다.
-   독립할 수 있고, 일반적이고, 테스트할 수 있는 애플리케이션 컴포넌트를 제공하세요.
-   스토어를 테스트할 수 있고 서버 측에서 실행할 수 있는지 확인하기 위해 실제 웹 소켓·http 요청을 별도의 객체로 이동하여 통신 계층을 추상화할 수 있습니다.
-   스토어 인스턴스는 하나만 있어야 합니다.

### 도메인 객체

각 도메인 객체는 자체 클래스(또는 생성자 함수)를 사용하여 표현해야 합니다.
클라이언트 애플리케이션 state를 일종의 데이터베이스로 취급할 필요가 없습니다.
실제 참조, 순환 데이터 구조 및 인스턴스 메서드는 Javascript의 강력한 개념입니다.
도메인 객체는 다른 스토어의 도메인 객체를 직접 참조할 수 있습니다.
다음 사항을 기억하세요. 우리는 우리의 action과 view를 최대한 단순하게 유지하기를 원합니다. 참조를 관리하고 스스로 가비지 수집을 수행하는 것에 대해 신경을 덜 쓰셔도 됩니다.
Redux 같은 Flux 아키텍처와 달리 MobX를 사용하면 데이터를 정규화할 필요가 없으며 이를 통해 애플리케이션의 _본질적_으로 복잡한 부분인 
비즈니스 규칙, action 및 사용자 인터페이스를 훨씬 간단하게 구축할 수 있습니다.

도메인 객체는 애플리케이션에 적합한 경우 자신의 모든 로직을 자신이 속한 스토어에 위임할 수도 있습니다.
도메인 객체를 plain 객체로 표현할 수 있지만, 클래스는 plain 객체보다 몇 가지 중요한 장점이 있습니다.

-   메서드를 가질 수 있습니다.
    메서드를 통해 도메인 개념을 독립 실행형으로 더 쉽게 사용하고 애플리케이션에 필요한 컨텍스트 인식의 양을 줄일 수 있습니다.
    그냥 객체를 전달해주기만 하면 됩니다.
    인스턴스 메서드로만 사용하는 경우 스토어를 전달하거나 객체에 적용할 수 있는 action을 파악할 필요가 없습니다.
    이는 대규모 애플리케이션에서 특히 중요합니다.
-   속성 및 메서드의 가시성을 세밀하게 제어할 수 있습니다.
-   생성자 함수를 사용하여 생성된 객체는 observable 속성 및 메서드와 non-observable 속성 및 메서드를 자유롭게 섞어 쓸 수 있습니다.
-   쉽게 알아볼 수 있고 엄격하게 타입을 검사할 수 있습니다.

### 도메인 스토어 예시

```javascript
import { makeAutoObservable, autorun, runInAction } from "mobx"
import uuid from "node-uuid"

export class TodoStore {
    authorStore
    transportLayer
    todos = []
    isLoading = true

    constructor(transportLayer, authorStore) {
        makeAutoObservable(this)
        this.authorStore = authorStore // 작성자를 확인할 수 있는 스토어
        this.transportLayer = transportLayer // 서버 요청을 할 수 있는 것
        this.transportLayer.onReceiveTodoUpdate(updatedTodo =>
            this.updateTodoFromServer(updatedTodo)
        )
        this.loadTodos()
    }

    // 서버에서 모든 todo를 가져옵니다.
    loadTodos() {
        this.isLoading = true
        this.transportLayer.fetchTodos().then(fetchedTodos => {
            runInAction(() => {
                fetchedTodos.forEach(json => this.updateTodoFromServer(json))
                this.isLoading = false
            })
        })
    }

    // 서버의 정보로 Todo를 업데이트합니다. Todo가 한 번만 존재함을 보장합니다.
    // 새로운 Todo를 생성하거나 기존 Todo를 업데이트하거나 
    // 서버에서 삭제된 Todo를 제거할 수 있습니다.
    updateTodoFromServer(json) {
        let todo = this.todos.find(todo => todo.id === json.id)
        if (!todo) {
            todo = new Todo(this, json.id)
            this.todos.push(todo)
        }
        if (json.isDeleted) {
            this.removeTodo(todo)
        } else {
            todo.updateFromJson(json)
        }
    }

    // 클라이언트와 서버에 새로운 Todo를 생성합니다.
    createTodo() {
        const todo = new Todo(this)
        this.todos.push(todo)
        return todo
    }

    // Todo가 어떻게든 삭제되었을 때 클라이언트 메모리에서 삭제합니다.
    removeTodo(todo) {
        this.todos.splice(this.todos.indexOf(todo), 1)
        todo.dispose()
    }
}

// 도메인 객체 Todo.
export class Todo {
    id = null // Todo의 고유 id, 변경할 수 없습니다.
    completed = false
    task = ""
    author = null // authorStore에서 가져온 Author 객체에 대한 참조
    store = null
    autoSave = true // Todo의 변경사항을 서버에 제출하기 위한 표시
    saveHandler = null // todo를 자동저장하는 부수효과의 Disposer(dispose).

    constructor(store, id = uuid.v4()) {
        makeAutoObservable(this, {
            id: false,
            store: false,
            autoSave: false,
            saveHandler: false,
            dispose: false
        })
        this.store = store
        this.id = id

        this.saveHandler = reaction(
            () => this.asJson, // JSON에서 사용되는 모든 것을 관찰합니다.
            json => {
                // autoSave가 true이면 JSON을 서버로 보냅니다.
                if (this.autoSave) {
                    this.store.transportLayer.saveTodo(json)
                }
            }
        )
    }

    // 클라이언트와 서버에서 해당 Todo를 제거하세요.
    delete() {
        this.store.transportLayer.deleteTodo(this.id)
        this.store.removeTodo(this)
    }

    get asJson() {
        return {
            id: this.id,
            completed: this.completed,
            task: this.task,
            authorId: this.author ? this.author.id : null
        }
    }

    // 서버의 정보로 Todo를 업데이트하세요.
    updateFromJson(json) {
        this.autoSave = false // 변경 사항을 서버로 다시 보내는 것을 방지합니다.
        this.completed = json.completed
        this.task = json.task
        this.author = this.store.authorStore.resolveAuthor(json.authorId)
        this.autoSave = true
    }

    // observer를 청소하세요.
    dispose() {
        this.saveHandler()
    }
}
```

## UI 스토어

_ui-state-store_는 종종 애플리케이션에 대해 매우 구체적이지만 일반적으로 매우 간단합니다.
_ui-state-store_에는 일반적으로 로직이 많지 않지만, 느슨하게 결합한 UI에 대해 많은 정보를 저장합니다.
이는 대부분의 애플리케이션이 개발 프로세스 중에 UI state를 자주 변경하므로 이상적입니다.

아래 항목들은 일반적으로 UI 스토어에 저장하는 정보입니다.

-   세션 정보
-   애플리케이션이 로드된 정도에 대한 정보
-   백엔드에 저장되지 않을 정보
-   UI에 전체적으로 영향을 미치는 정보
    -   Window dimensions
    -   접근성 정보(Accessibility information)
    -   현재 사용 중인 언어(Current language)
    -   현재 활성 중인 테마(Currently active theme)
-   관련 없는 여러 컴포넌트에 영향을 미치는 유저 인터페이스 state
    -   현재 선택 항목(Current selection)
    -   툴바 가시성(Visibility of toolbars)
    -   위저드(wizard) state
    -   전역 오버레이(global overlay) state

이러한 정보는 특정 컴포넌트의 내부 state(예: 도구 모음의 가시성)로 시작되지만, 이후 애플리케이션의 다른 위치에도 이 정보가 필요하다는 것을 알게 됩니다.
일반 React 앱에서와같이 컴포넌트 트리에서 위쪽으로 state를 푸시하는 대신 state를 _ui-state-store_로 이동시키면 됩니다.

동형 애플리케이션의 경우 모든 컴포넌트가 예상대로 렌더링 되도록 기본값으로 저장소의 스텁(stub) 구현을 제공할 수도 있습니다.
애플리케이션에 React 컨텍스트로 전달하여 _ui-state-store_를 배포할 수 있습니다.

ES6 문법을 사용한 저장소 예시입니다.

```javascript
import { makeAutoObservable, observable, computed, asStructure } from "mobx"

export class UiState {
    language = "en_US"
    pendingRequestCount = 0

    // .struct는 dimension 객체가 deepEqual 방식으로 
    // 변경되지 않는 한 observer가 신호를 받지 않습니다.
    
    windowDimensions = {
        width: window.innerWidth,
        height: window.innerHeight
    }

    constructor() {
        makeAutoObservable(this, { windowDimensions: observable.struct })
        window.onresize = () => {
            this.windowDimensions = getWindowDimensions()
        }
    }

    get appIsInSync() {
        return this.pendingRequestCount === 0
    }
}
```

## 스토어 결합하기

싱글톤을 사용하지 않고 여러 스토어를 결합하는 방법에 대해 궁금할 것입니다. 어떻게 서로에 대해 알게 될까요?

효과적인 패턴은 모든 저장소를 인스턴화하고 참조를 공유하는 `RootStore`를 만드는 것입니다. 이러한 패턴의 장점은 다음과 같습니다.

1. 설정이 간단합니다.
2. 강력한 타이핑을 지원합니다.
3. root 저장소를 인스턴스화 하기만 하면 복잡한 유닛 테스트를 쉽게 할 수 있습니다.

예시

```javascript
class RootStore {
    constructor() {
        this.userStore = new UserStore(this)
        this.todoStore = new TodoStore(this)
    }
}

class UserStore {
    constructor(rootStore) {
        this.rootStore = rootStore
    }

    getTodos(user) {
        // root 저장소를 통해 todoStore에 접근합니다.
        return this.rootStore.todoStore.todos.filter(todo => todo.author === user)
    }
}

class TodoStore {
    todos = []
    rootStore

    constructor(rootStore) {
        makeAutoObservable(this, { rootStore: false })
        this.rootStore = rootStore
    }
}
```

React를 사용할 때 루트 저장소는 일반적으로 React 컨텍스트를 사용하여 컴포넌트 트리에 삽입됩니다.
