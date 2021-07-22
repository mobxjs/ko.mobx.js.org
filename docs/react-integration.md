---
title: React 통합
sidebar_label: React 통합
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# React 통합

사용 방법:

```javascript
import { observer } from "mobx-react-lite" // 또는 "mobx-react".

const MyComponent = observer(props => ReactElement)
```

MobX는 React와 독립적으로 작동하지만, 일반적으로 React와 함께 사용합니다. [MobX의 요점](the-gist-of-mobx.md)에서 이미 통합에 가장 중요한 부분인 React component를 감쌀 수 있는 `observer` [HoC](https://reactjs.org/docs/higher-order-components.html)를 보았습니다.

`observer`는 [설치 중](installation.md#installation)에 선택한 별도의 React 바인딩 패키지에서 제공됩니다. 아래 예시에서는 더 가벼운 [`mobx-react-lite` 패키지](https://github.com/mobxjs/mobx/tree/main/packages/mobx-react-lite)를 사용할 것입니다.

```javascript
import React from "react"
import ReactDOM from "react-dom"
import { makeAutoObservable } from "mobx"
import { observer } from "mobx-react-lite"

class Timer {
    secondsPassed = 0

    constructor() {
        makeAutoObservable(this)
    }

    increaseTimer() {
        this.secondsPassed += 1
    }
}

const myTimer = new Timer()

// `observer`로 감싸진 함수 컴포넌트는
// 이전에 사용했던 observable의 향후 변경 사항에 반응합니다.
const TimerView = observer(({ timer }) => <span>Seconds passed: {timer.secondsPassed}</span>)

ReactDOM.render(<TimerView timer={myTimer} />, document.body)

setInterval(() => {
    myTimer.increaseTimer()
}, 1000)
```

**Hint:** 위의 예제는 [CodeSandbox](https://codesandbox.io/s/minimal-observer-p9ti4?file=/src/index.tsx)에서 직접 실행해 볼 수 있습니다.

The `observer` HoC는 _렌더링 중에_ 사용되는 _모든 observable_에 React 컴포넌트들을 자동으로 구독합니다.
결과적으로 관련 observables가 변경되면 컴포넌트들을 자동으로 다시 렌더링합니다.
또한 _관련된_ 변경사항이 없을 때는 컴포넌트가 다시 렌더링 되지 않습니다.
따라서, 컴포넌트로부터 접근할 수는 있지만 실제로 읽지 않는 observables는 다시 렌더링 되지 않습니다.

이러한 로직은 MobX 어플리케이션을 잘 최적화 시키며 과도한 렌더링을 방지하기 위해 추가 코드가 필요하지 않습니다.

`observer`가 작동하려면 observables가 _어떻게_ 도착하는지는 중요하지 않고 읽히기만 하면 됩니다.
observables를 깊게 읽는 것도 잘 작동하고, `todos[0].author.displayName`처럼 복잡한 표현도 잘 작동합니다.
이러한 로직은 데이터 의존성을 명시적으로 선언하거나 미리 계산해야 하는 다른 프레임워크(selectors)에 비해 구독 메커니즘(mechanism)을 훨씬 더 정확하고 효율적으로 만듭니다.

## 로컬 및 외부 상태

상태를 구성하는 방법에는 큰 유연성이 있습니다. 왜냐하면 어떤 observables를 읽는지 또는 observables가 어디에서 유래했는지는 중요하지 않기 때문입니다.
아래 예제는 `observer`로 감싸인 컴포넌트에서 외부 및 로컬 observable 상태를 사용하는 방법에 대해 다양한 패턴을 보여줍니다.

### `observer` 컴포넌트에서 외부 상태 사용하기

<!--DOCUSAURUS_CODE_TABS-->
<!--props 사용-->

Observables는 props로 컴포넌트에 전달할 수 있습니다. (아래 예시 처럼)

```javascript
import { observer } from "mobx-react-lite"

const myTimer = new Timer() // 위의 타이머 정의를 참고하세요.

const TimerView = observer(({ timer }) => <span>Seconds passed: {timer.secondsPassed}</span>)

// myTimer를 prop으로 전달합니다.
ReactDOM.render(<TimerView timer={myTimer} />, document.body)
```

<!--전역 변수 사용-->

observable에 대한 참조를 얻는 _방법_은 중요하지 않으므로 
외부 범위에서 직접 observables를 사용할 수 있습니다. (imports 포함)

```javascript
const myTimer = new Timer() // 위의 타이머 정의를 참고하세요.

// `myTimer`는 props를 사용하지 않고 클로저로 인해 직접 사용됩니다.
const TimerView = observer(() => <span>Seconds passed: {myTimer.secondsPassed}</span>)

ReactDOM.render(<TimerView />, document.body)
```

observables을 직접 사용하는 것은 잘 작동하지만 일반적으로 모듈 상태가 도입되기 때문에 이러한 패턴은 단위 테스트를 복잡하게 만들 수 있습니다. 그래서 전역 변수를 사용하는 대신 React Context를 사용하는 것이 좋습니다.

<!--React context 사용-->

[React Context](https://reactjs.org/docs/context.html)는 전체 하위 트리와 observables를 공유하는 훌륭한 메커니즘입니다.

```javascript
import {observer} from 'mobx-react-lite'
import {createContext, useContext} from "react"

const TimerContext = createContext<Timer>()

const TimerView = observer(() => {
    // 컨텍스트에서 타이머를 가져옵니다.
    const timer = useContext(TimerContext) // 위의 타이머 정의를 참고하세요.
    return (
        <span>Seconds passed: {timer.secondsPassed}</span>
    )
})

ReactDOM.render(
    <TimerContext.Provider value={new Timer()}>
        <TimerView />
    </TimerContext.Provider>,
    document.body
)
```

Provider의 값을 다른 값으로 바꾸지 않는 것이 좋습니다. MobX를 사용하면 공유되는 observable이 자동으로 업데이트 되므로 Provider의 값을 다른 값으로 바꿀 필요가 없습니다.

<!--END_DOCUSAURUS_CODE_TABS-->

### `observer`컴포넌트에서 로컬 observable 상태 사용하기

`observer`가 사용하는 observables은 어디에서나 올 수 있으므로 로컬 상태일 수도 있습니다. 
다시 말해, 위에서 소개한 옵션과는 다른 로컬 상태를 사용할 수 있습니다. 

<!--DOCUSAURUS_CODE_TABS-->
<!--observable 클래스와 `useState` 함께 쓰기-->

로컬 observable 상태를 사용하는 가장 간단한 방법은 useState를 사용하여 observable 클래스에 대한 참조를 저장하는 것입니다.
일반적으로 참조를 변경하고 싶지 않기 때문에 useState에서 반환된 업데이터 함수를 완전히 무시합니다.

```javascript
import { observer } from "mobx-react-lite"
import { useState } from "react"

const TimerView = observer(() => {
    const [timer] = useState(() => new Timer()) // 위의 타이머 정의를 참고하세요.
    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

이전 예제에서 했던 것 처럼 타이머를 자동으로 업데이트하려면 
`useEffect`를 일반적인 React방식으로 사용할 수 있습니다.

```javascript
useEffect(() => {
    const handle = setInterval(() => {
        timer.increaseTimer()
    }, 1000)
    return () => {
        clearInterval(handle)
    }
}, [timer])
```

<!--로컬 observable 객체와 `useState` 함께 쓰기-->

앞에서 언급했듯이 클래스를 사용하는 대신 observable 객체를 직접 생성할 수 있습니다.
이를 위해 [observable](observable-state.md#observable)을 활용할 수 있습니다.

```javascript
import { observer } from "mobx-react-lite"
import { observable } from "mobx"
import { useState } from "react"

const TimerView = observer(() => {
    const [timer] = useState(() =>
        observable({
            secondsPassed: 0,
            increaseTimer() {
                this.secondsPassed++
            }
        })
    )
    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

<!--`useLocalObservable` hook-->

`const [store] = useState(() => observable({ /* something */}))`은 일반적인 조합입니다.  
이 패턴을 더 단순하게 만들기 위해 [`useLocalObservable`](https://github.com/mobxjs/mobx-react#uselocalobservable-hook) hook이 `mobx-react-lite` 패키지에 있으며, 이를 통해 이전 예제를 다음과 같이 단순화할 수 있습니다.

```javascript
import { observer, useLocalObservable } from "mobx-react-lite"
import { useState } from "react"

const TimerView = observer(() => {
    const timer = useLocalObservable(() => ({
        secondsPassed: 0,
        increaseTimer() {
            this.secondsPassed++
        }
    }))
    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

<!--END_DOCUSAURUS_CODE_TABS-->

### observable 상태가 로컬로 필요하지 않을 수 있습니다.

이론적으로 React's Suspense 메커니즘의 일부 기능을 차단할 수 있으므로 일반적으로 로컬 컴포넌트 상태에 대해 MobX observables를 너무 빨리 의존하지 않는 것이 좋습니다.
일반적으로 상태가 컴포넌트(하위항목 포함)간 공유되는 도메인 데이터를 캡처할 때 MobX observables를 사용하세요. ex) todo items, users, bookings 등등

로딩 상태, 선택 등과 같은 UI상태만 캡처하는 상태는 추후에 React suspense 기능을 활용할 수 있기 때문에 [`useState` hook](https://reactjs.org/docs/hooks-state.html)을 사용하는 것이 더 좋습니다.

React 컴포넌트 안에서 observables를 사용하는 것은 깊거나(deep), computed 값이 있거나, 다른 `observer` 컴포넌트와 공유될 때 가치가 있습니다.

## 항상 `observer` 컴포넌트 안에서 observables를 읽습니다.

`observer`를 언제 사용해야 할지 궁금하시나요? 일반적으로 _observable 데이터를 읽는 모든 컴포넌트에 observer를 사용합니다_.

`observer`는 감싸고 있는 컴포넌트만 개선하며, 감싸고 있는 컴포넌트를 호출하는 컴포넌트는 개선하지 않습니다. 따라서 일반적으로 모든 컴포넌트는 observer에 의해 감싸져야 하며, 모든 컴포넌트를 observer로 감싸는 행동은 비효율적이지 않기 때문에 걱정하실 필요가 없습니다. `observer` 컴포넌트가 많을수록 업데이트의 세밀성이 더 높아져 렌더링 효율성이 높아집니다.
### Tip: 가능한 한 늦게 객체에서 값을 가져옵니다.

`observer`는 가능한 한 오랫동안 개체 참조를 전달하고 DOM과 low-level 컴포넌트로 렌더링 될 예정인 observer 기반 컴포넌트 내부의 속성만 읽을 때 가장 잘 동작합니다.
즉, `observer`는 객체에서 값을 '역참조'한다는 사실에 반응합니다.

아래의 예시에서 `TimerView` 컴포넌트는 `.secondsPassed`가 `observer`컴포넌트 내부에서 읽는 것이 아니라 외부에서 읽혀 추적되지 _않기_ 때문에 향후 변경사항에 반응하지 **않습니다**.

```javascript
const TimerView = observer(({ secondsPassed }) => <span>Seconds passed: {secondsPassed}</span>)

React.render(<TimerView secondsPassed={myTimer.secondsPassed} />, document.body)
```

이러한 방법은 memoization을 더 잘 활용하기 위해 초기에 역참조하고 원시적인 값(primitives)을 전달하는 것이 좋은 관행인 react-redux와는 다른 사고 방식입니다. 
자세한 사항은 [Understanding reactivity](understanding-reactivity.md)를 확인해주세요.

### `observer`가 아닌 컴포넌트에 observables를 전달하지 마세요.

`observer`로 감싸진 컴포넌트는 컴포넌트 _자체_ 렌더링 중에 사용되는 observable만 구독합니다.
따라서 observable objects / arrays / maps이 자식 컴포넌트에 전달되면 자식 컴포넌트들도 `observer`로 감싸줘야 합니다. 
위의 내용은 모든 콜백 요소 기반 컴포넌트들도 해당합니다.

`observer`가 아닌 컴포넌트에 observables를 전달하려는 경우엔 전달하기 전에 observable을 [일반 Javascript 값 또는 구조로 변환](observable-state.md#converting-observables-back-to-vanilla-javascript-collections) 해야합니다.

위의 내용을 자세히 설명하기 위해
observable `todo` 객체, `TodoView` 컴포넌트 (observer), 열(column)과 값(value) 매핑을 사용하지만, 관찰자는 아닌 가상의 `GridRow` 컴포넌트를 예로 들어보겠습니다.

```javascript
class Todo {
    title = "test"
    done = true

    constructor() {
        makeAutoObservable(this)
    }
}

const TodoView = observer(({ todo }: { todo: Todo }) =>
   // 잘못된 예시: GridRow는 observer가 아니기 때문에 todo.title 과 todo.done에 대한 변경 사항을 선택하지 않습니다.
   return <GridRow data={todo} />

   // 올바른 예시: `TodoView`가 `todo` 관련 변경사항을 감지하여
   //            일반 데이터를 전달하도록 합니다. 
   return <GridRow data={{
       title: todo.title,
       done: todo.done
   }} />

   // 올바른 예시: `toJS`를 사용하는 것도 좋지만, 일반적으로 명시적으로 사용하는 것이 더 좋습니다.
   return <GridRow data={toJS(todo)} />
)
```

### 콜백 컴포넌트는 `<Observer>`가 필요할 수 있습니다.

`GridRow`가 `onRender` 콜백을 사용하는 동일한 예를 상상해보세요.
`onRender`는 `TodoView`의 렌더가 아니라 `GridRow`의 렌더링 주기의 일부이기 때문에 (구문상 표시가 되는 위치에 있음에도 불구하고) 콜백 컴포넌트가 observer 컴포넌트를 사용하는지 확인해야 합니다.
또는 [`<Observer />`](https://github.com/mobxjs/mobx-react#observer)을 사용하여 인라인 익명 observer를 만들 수 있습니다.

```javascript
const TodoView = observer(({ todo }: { todo: Todo }) => {
    // 잘못된 예시: GridRow.onRender는 observer가 아니기 때문에 todo.title / todo.done의 변경사항을 선택하지 않습니다.
    return <GridRow onRender={() => <td>{todo.title}</td>} />

    // 올바른 예시: 변경사항을 감지 할 수 있도록 Observer에 콜백 렌더링을 감쌉니다.
    return <GridRow onRender={() => <Observer>{() => <td>{todo.title}</td>}</Observer>} />
})
```

## Tips

<details id="static-rendering"><summary>Server Side Rendering (SSR)<a href="#static-rendering" class="tip-anchor"></a></summary>
서버 사이드 렌더링 컨텍스트에서 `observer`가 사용되는 경우: `observer`가 사용된 observables를 구독하는 것을 막고 GC문제가 발생하지 않도록 하기위해 `enableStaticRendering(true)`를 호출해야 합니다.
</details>

<details id="react-vs-lite"><summary>**Note:** mobx-react vs. mobx-react-lite<a href="#react-vs-lite" class="tip-anchor"></a></summary>
해당 문서에서는 기본적으로 `mobx-react-lite`를 사용했습니다.
[mobx-react](https://github.com/mobxjs/mobx-react/)는 더 거대하며 `mobx-react-lite`를 안에서 사용합니다.
mobx-react는 기존 프로그램으로 구축하지 않고 처음부터 개발하는 소프트웨어에서 필요하지 않은 몇가지 기능을 더 제공합니다.
mobx-react가 제공하는 추가 제공사항은 다음과 같습니다.

1. React 클래스 컴포넌트를 지원합니다.
1. `Provider` 그리고 `inject`를 제공합니다. React.createContext가 더 이상 필요하지 않습니다.
1. 명확한 observable `propTypes`.

`mobx-react`는 함수형 컴포넌트를 지원하며 `mobx-react-lite`를 완전히 다시 패키징하고 내보냅니다.
`mobx-react`를 사용하면 `mobx-react-lite`를 의존성으로 추가하거나 다른곳에서 가져올 필요가 없습니다.

</details>

<details id="observer-vs-memo"><summary>**Note:** `observer` 또는 `React.memo`?<a href="#observer-vs-memo" class="tip-anchor"></a></summary>
`observer`는 자동적으로 `memo`를 적용하므로 `observer` 컴포넌트는 `memo`로 감쌀 필요가 없습니다.
 props 안에 아무리 깊게 있어도 `observer`가 찾을 수 있기 때문에 `memo`는 observer 컴포넌트에 안전하게 적용될 수 있습니다.
</details>

<details id="class-comp"><summary>**Tip:** 클래스 기반 리액트 컴포넌트를 위한 `observer` 사용방법<a href="#class-comp" class="tip-anchor"></a>
</summary>
위에서 언급했듯 클래스 기반 컴포넌트는 `mobx-react-lite`가 아닌 `mobx-react`를 통해서만 지원됩니다.
간단히 말해서 다음과 같이 `observer`에서 함수 컴포넌트를 감싼것 처럼 클래스 기반 컴포넌트를 감쌀 수 있습니다.

```javascript
import React from "React"

const TimerView = observer(
    class TimerView extends React.Component {
        render() {
            const { timer } = this.props
            return <span>Seconds passed: {timer.secondsPassed} </span>
        }
    }
)
```

자세한 내용은 [mobx-react docs](https://github.com/mobxjs/mobx-react#api-documentation)을 확인해주세요.

</details>

<details id="displayname"><summary>**Tip:** React DevTools에서 멋진 컴포넌트 이름 사용하기<a href="#displayname" class="tip-anchor"></a>
</summary>
[React DevTools](https://reactjs.org/blog/2019/08/15/new-react-devtools.html)는 컴포넌트 계층 구조를 적절하게 표시하기 위해 컴포넌트의 display name을 사용합니다.

아래와 같이 사용하는 경우

```javascript
export const MyComponent = observer(props => <div>hi</div>)
```

DevTools에 no display name이 보일 것입니다.

![devtools-noname](assets/devtools-noDisplayName.png)

위의 문제를 해결하기 위해 다음과 같이 접근할 수 있습니다.

-   화살표 함수 대신에 이름이 있는 `function`을 사용하세요. `mobx-react`는 함수 이름에서 컴포넌트 이름을 유추합니다.

    ```javascript
    export const MyComponent = observer(function MyComponent(props) {
        return <div>hi</div>
    })
    ```

-   TypeScript와 Babel같은 변환기는 변수 이름에서 컴포넌트 이름을 유추합니다.

    ```javascript
    const _MyComponent = props => <div>hi</div>
    export const MyComponent = observer(_MyComponent)
    ```

-   default export를 사용하여 변수 이름에서 추론합니다.

    ```javascript
    const MyComponent = props => <div>hi</div>
    export default observer(MyComponent)
    ```

-   [**Broken**] `displayName`을 명시적으로 설정합니다.

    ```javascript
    export const MyComponent = observer(props => <div>hi</div>)
    MyComponent.displayName = "MyComponent"
    ```

    이것은 React 16에서 작동되지 않습니다. mobx-react `observer`는 React.memo를 사용하고 다음과 같은 버그(https://github.com/facebook/react/issues/18026)를 발생 시키지만, React 17에서 수정될 것입니다.

이제 컴포넌트 이름을 볼 수 있습니다.

![devtools-withname](assets/devtools-withDisplayName.png)

</details>

<details id="wrap-order"><summary>{🚀} **Tip:** `observer`를 다른 고차 컴포넌트와 결합할 때 `observer`를 먼저 적용하세요.<a href="#wrap-order" class="tip-anchor"></a></summary>

`observer`가 다른 데코레이터나 고차 컴포넌트와 결합하는 경우, `observer`가 가장 안쪽에 있는 (처음 적용된) 데코레이터 인지 확인하세요. 
그렇지 않으면 정상적으로 작동하지 않습니다.

</details>

<details id="computed-props"><summary>{🚀} **Tip:** props로부터 computed 파생<a href="#computed-props" class="tip-anchor"></a></summary>
로컬 observables의 computed 값이 컴포넌트가 받는 props에 따라 달라질 수 있습니다.
하지만 리액트 컴포넌트가 받는 props는 observable이 아니므로 props에 대한 변경 사항은 computed 값에 반영되지 않습니다. 최신 데이터에서 computed 값을 적절하게 추출하기 위해서는 로컬 observable 상태를 수동으로 업데이트 해야합니다.

```javascript
import { observer, useLocalObservable } from "mobx-react-lite"
import { useEffect } from "react"

const TimerView = observer(({ offset }) => {
    const timer = useLocalObservable(() => ({
        offset, // The initial offset value
        secondsPassed: 0,
        increaseTimer() {
            this.secondsPassed++
        },
        get offsetTime() {
            return this.secondsPassed - this.offset // 'props'로 넘어온 'offset'이 아닙니다!
        }
    }))

    useEffect(() => {
        // `props`로 넘어온 offset을 observable `timer`와 동기화합니다.
        timer.offset = offset
    }, [offset])

    // 데모를 위해 Effect를 활용하여 타이머를 설정 합니다.
    useEffect(() => {
        const handle = setInterval(timer.increaseTimer, 1000)
        return () => {
            clearInterval(handle)
        }
    }, [])

    return <span>Seconds passed: {timer.offsetTime}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

실제로 이러한 패턴은 거의 필요하지 않으며,
`return <span>Seconds passed: {timer.secondsPassed - offset}</span>`
이 약간 덜 효율적이지만 훨씬 간단합니다.

</details>

<details id="useeffect"><summary>{🚀} **Tip:** useEffect 와 observables<a href="#useeffect" class="tip-anchor"></a></summary>

`useEffect`는 리액트 컴포넌트의 라이프 사이클에서 발생해야하는 부수효과(side effects)를 설정하는데 사용할 수 있습니다.
`useEffect`를 사용하려면 의존성을 지정해야합니다.
MobX는 이미 effect의 의존성을 자동으로 결정하는 방법인 `autorun`을 가지고 있기 때문에 `useEffect`에 의존성을 지정하는 작업이 필요하지 않습니다.
`useEffect`를 사용하여 `autorun`과 컴포넌트의 수명주기를 합치는 것은 다행히 간단합니다.

```javascript
import { observer, useLocalObservable, useAsObservableSource } from "mobx-react-lite"
import { useState } from "react"

const TimerView = observer(() => {
    const timer = useLocalObservable(() => ({
        secondsPassed: 0,
        increaseTimer() {
            this.secondsPassed++
        }
    }))

    // observable의 변경사항에 따라 트리거 되는 Effect입니다.
    useEffect(
        () =>
            autorun(() => {
                if (timer.secondsPassed > 60) alert("Still there. It's a minute already?!!")
            }),
        []
    )

    // 데모를 위해 Effect를 활용하여 타이머를 설정 합니다.
    useEffect(() => {
        const handle = setInterval(timer.increaseTimer, 1000)
        return () => {
            clearInterval(handle)
        }
    }, [])

    return <span>Seconds passed: {timer.secondsPassed}</span>
})

ReactDOM.render(<TimerView />, document.body)
```

effect함수에서 `autorun`에 의해 생성된 disposer를 반환한다는 점에 유의하세요.
해당 내용은 컴포넌트가 사라질 때 `autorun`이 정리되기 때문에 중요합니다!

observable의 아닌 값이 autorun을 다시 실행해야 하는 경우를 제외하고 의존성 배열은 비워 둘 수 있고, 다시 실행 해야하는 경우에는 의존성 배열을 추가해야합니다.
linter를 만족스럽게 만들기 위해 타이머(위의 예시처럼)를 의존성으로 정의할 수 있습니다.
참조가 실제로 변경되지 않기 때문에 안전하고 더 이상 영향을 미치지 않습니다.

어떤 observables가 효과(effect)를 트리거 해야하는지 명시적으로 정의하려면 다른 패턴들은 유지하고 `autorun` 대신 `reaction`을 사용하세요.

</details>

### React 컴포넌트를 어떻게 최적화할 수 있나요?

[React 최적화 {🚀}](react-optimizations.md)를 확인해주세요.

## 문제해결 방법

도와주세요. 컴포넌트가 리렌더링 되지 않아요...

1. `observer`를 잊지 않았는지 확인하세요.
1. 반응하려는 대상이 실제로 observable인지 확인해보세요. 런타임에 확인할 경우 [`isObservable`](api.md#isobservable), [`isObservableProp`](api.md#isobservableprop)와 같은 유틸리티를 사용해보세요.
1. 경고 또는 에러가 있는지 브라우저의 콘솔 로그를 확인해보세요.
1. 일반적으로 추적이 어떻게 작동하는지 확인해보세요. [Understanding reactivity](understanding-reactivity.md)를 참고하세요.
1. 위에서 설명하고 있는 잘못된 예시를 확인해보세요.
1. MobX를 [구성](configuration.md#linting-options)하여 잘못된 메커니즘 사용에 대해 경고하고 콘솔 로그를 확인해보세요.
1. [trace](analyzing-reactivity.md)를 사용하여 올바른 구독을 하고 있는지 확인하거나 [spy](analyzing-reactivity.md#spy), [mobx-logger](https://github.com/winterbe/mobx-logger) 패키지를 사용하여 MobX가 일반적으로 무엇을 하는지 확인해보세요.
