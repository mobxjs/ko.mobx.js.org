---
title: 반응 분석하기
sidebar_label: 반응 분석하기 {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 반응 분석하기 {🚀}

# `trace`를 사용한 디버깅

`trace`는 컴퓨티드 값, 리액션, 컴포넌트 등이 왜 재계산되었는지 알려주는 조그만 유틸입니다.

사용법은 간단합니다. `import { trace } from "mobx"`로 임포트한 다음 리액션이나 컴퓨티드 값 안에 넣으면 됩니다.
이렇게 하면 해당 값이 왜 재계산되었는지 바로 출력이 됩니다.

인자로 `true`를 넘겨주면 자동으로 디버깅 모드에 진입할 수도 있습니다.
디버깅 모드에서는 해당 리액션을 재실행하게 만든 변경사항을 스택 프레임을 통해 확인할 수 있습니다. 아래 이미지를 참고해주세요.

디버깅 모드에서는 리액션과 컴퓨테이션에 영향을 준 전체 파생 트리(derivation tree)도 볼 수 있습니다.

![trace](assets/trace-tips2.png)

![trace](assets/trace.gif)

## 라이브 예시

[CodeSandbox](https://codesandbox.io/s/trace-dnhbz?file=/src/index.js:309-338) 에서 `trace` 를 사용한 간단한 예시를 확인해보세요.

[여기에는](https://csb-nr58ylyn4m-hontnuliaa.now.sh/) 스택을 직접 둘러볼 수 있게 올려두었습니다.
크롬 디버거의 블랙박스 기능도 꼭 써보세요!

## 사용 예시

`trace()` 호출에는 총 3가지 방법이 있습니다. 먼저 아래와 같이 호출할 수 있습니다.

```javascript
import { observer } from "mobx-react"
import { trace } from "mobx"

const MyComponent = observer(() => {
    trace(true) // 옵저버블 값이 이 컴포넌트를 재실행하게 할 때마다 디버깅 모드로 진입합니다.
    return <div>{this.props.user.name}</div>
})
```

둘째로, 리액션이나 오토런의 `reaction` 인자를 통해 `trace()`를 호출할 수 있습니다.

```javascript
mobx.autorun("logger", reaction => {
    reaction.trace()
    console.log(user.fullname)
})
```

마지막으로, 컴퓨티드 값 속성의 이름을 문자열로 넘겨줘도 됩니다.

```javascript
trace(user, "fullname")
```

# 내부검사 API

아래 API 들은 디버깅을 위해 MobX 의 내부 상태를 검사하거나 MobX 를 활용해 멋진 툴을 만들 때 유용하게 쓸 수 있습니다.
다양한 [`isObservable*` API](api.md#isobservable) 도 참고해보세요.

### `getDebugName`

사용방법:

-   `getDebugName(thing, property?)`

옵저버블 객체나 프로퍼티, 리액션 등에 대해 친절한 디버그 이름을 자동 생성해서 반환합니다. [MobX 개발자 도구](https://github.com/mobxjs/mobx-devtools) 에서도 사용되었습니다.

### `getDependencyTree`

사용방법:

-   `getDependencyTree(thing, property?)`.

리액션이나 컴퓨테이션이 의존하고 있는 모든 옵저버블을 트리 형태로 반환합니다.

### `getObserverTree`

사용방법:

-   `getObserverTree(thing, property?)`.

옵저버블을 사용하고 있는 모든 리액션과 컴퓨테이션을 트리 형태로 반환합니다.

### `getAtom`

사용방법:

-   `getAtom(thing, property?)`.

옵저버블 객체나 프로퍼티, 리액션 등의 _아톰_ 을 반환합니다.

# Spy

사용방법:

-   `spy(listener)`

MobX 의 모든 이벤트를 감시하는 전역 감시 리스너를 등록합니다.
이 작업은 _모든_ 옵저버블에 `observe` 리스너를 붙이는 것과 거의 동일합니다. 여기에 추가로 실행 중인 트랜잭션, 리액션, 컴퓨테이션에 대해 알려줍니다.
이것도 역시 [MobX 개발자 도구](https://github.com/mobxjs/mobx-devtools) 에서 사용되었습니다.

모든 액션에 대해 스파이하는 예시:

```javascript
spy(event => {
    if (event.type === "action") {
        console.log(`${event.name} with args: ${event.arguments}`)
    }
})
```

스파이 리스너는 항상 하나의 객체를 받습니다. 이 객체엔 항상 `type` 필드가 있고 이 타입에 따라 서로 다른 필드를 갖게 됩니다. `spy`를 하면 기본적으로 아래 이벤트들이 감시됩니다.

| 타입                            | 옵저버블 유형 | 다른 필드들                                                   | 중첩 여부 |
| ------------------------------- | -------------- | -------------------------------------------------------------- | ------ |
| action                          |                | name, object (scope), arguments[]                              | 예    |
| scheduled-reaction              |                | name                                                           | 아니오     |
| reaction                        |                | name                                                           | 예    |
| error                           |                | name, message, error                                           | 아니오     |
| add,update,remove,delete,splice |                | [가로채기 & 관찰 {🚀}](intercept-and-observe.md) 참고 | 예    |
| report-end                      |                | spyReportEnd=true, time? (총 실행 시간(ms))          | 아니오     |

`report-end` 이벤트는 `spyReportStart: true` 이벤트와 한 쌍을 이룹니다.
이 이벤트가 묶음 이벤트의 마지막이란 것을 표시해줌으로써 서브 이벤트들을 포함하는 하나의 묶음 이벤트가 생성됩니다.
이 이벤트를 통해 전체 실행 시간을 보고 받을 수도 있습니다.

옵저버블 값들에 대한 스파이 이벤트들은 `observe`에 넘겨진 이벤트와 동일하게 취급됩니다.
프로덕션 빌드에서 `spy` API 는 무시되어 실행되지 않습니다.

[가로채기 & 관찰 {🚀}](intercept-and-observe.md#event-overview) 섹션에서 더 자세한 내용을 알아보세요.
