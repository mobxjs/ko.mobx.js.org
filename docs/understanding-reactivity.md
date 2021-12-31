---
title: 반응성 이해하기
sidebar_label: 반응성 이해하기
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 반응성 이해하기

MobX는 일반적으로 사용자가 기대하는 사항에 정확히 반응합니다. 즉, 사용 사례의 90% 정도는 MobX가 "바로 작동"해야 합니다.
그러나 어느 순간 예상했던 대로 되지 않는 경우가 발생할 수 있습니다. 그 시점에서 MobX가 무엇에 반응할지를 어떻게 결정하는지 이해하는 것이 중요합니다.

> MobX는 추적된 함수의 실행 과정에서 읽히는 _기존의_ **observable** _속성_에 반응합니다.

-   _"읽는다"_란 점 표기법(예 : `user.name`) 또는 괄호 표기법(예 : `user['name']`, `todos[3]`)을 통해 객체 속성을 역참조하는 것입니다.
-   _"추적된 함수"_란 `computed`, `observer`인 React 함수 컴포넌트의 _렌더링_, React 클래스 컴포넌트 기반 `observer`에서의 `render()` 메서드 및 `autorun`, `reaction`, `when`의 첫 번째 파라미터로 전달되는 함수를 말합니다.
-   _"과정에서"_란 함수가 실행되는 동안 읽히는 observable만이 추적됨을 의미합니다. 해당 값이 추적된 함수에 의해 직접적으로 사용되는지 간접적으로 사용되는지는 중요하지 않습니다. 그러나 함수에서 '스폰(spawn)'된 항목은 추적되지 않습니다.(예 : `setTimeout`, `promise.then`, `await` 등)

다르게 말하면, MobX는 다음과 같은 항목에는 반응하지 않습니다.

-   observable로부터 얻어졌지만 추적된 함수 외부에 있는 값
-   비동기적으로 호출된 코드 블록에서 읽어진 observable

## MobX는 값이 아닌 속성 액세스를 추적합니다

위의 규칙을 예제로 자세히 설명하기 위해 다음과 같은 observable 인스턴스가 있다고 가정합니다.

```javascript
class Message {
    title
    author
    likes
    constructor(title, author, likes) {
        makeAutoObservable(this)
        this.title = title
        this.author = author
        this.likes = likes
    }

    updateTitle(title) {
        this.title = title
    }
}

let message = new Message("Foo", { name: "Michel" }, ["Joe", "Sara"])
```

위 코드는 다음과 같이 표현될 수 있습니다. 녹색 상자는 _observable_ 속성을 나타냅니다. _값_ 자체는 observable이 아닙니다!

![MobX reacts to changing references](assets/observed-refs.png)

MobX가 기본적으로 하는 일은 함수에서 사용하는 _화살표_를 기록하는 것입니다. 이후에는 이러한 _화살표_ 중 하나가 변경될 때(다른 항목을 참조하기 시작할 때)마다 재실행합니다.

## 예시

위에서 정의한 `message` 변수를 기반으로 여러 가지 예제를 통해 확인해봅시다.

#### 옳은 예: 추적된 함수 내에서의 역참조

```javascript
autorun(() => {
    console.log(message.title)
})
message.updateTitle("Bar")
```

위 코드는 예상대로 반응합니다. `.title` 속성은 autorun에서 역참조된 후 변경되었으므로 해당 변경 내용이 감지됩니다.

추적된 함수 내에서 [`trace()`](analyzing-reactivity.md)를 호출하면 MobX가 무엇을 추적하는지 확인할 수 있습니다. 위 함수의 경우 다음을 출력합니다.

```javascript
import { trace } from "mobx"

const disposer = autorun(() => {
    console.log(message.title)
    trace()
})
// 출력 값:
// [mobx.trace] 'Autorun@2' tracing enabled

message.updateTitle("Hello")
// 출력 값:
// [mobx.trace] 'Autorun@2' is invalidated due to a change in: 'Message@1.title'
Hello
```

`getDependencyTree`를 사용하여 내부 종속성(또는 observer) 트리를 가져올 수도 있습니다.

```javascript
import { getDependencyTree } from "mobx"

// disposer에 복제된 reaction의 종속성 트리(dependency tree)를 출력합니다.
console.log(getDependencyTree(disposer))
// 출력 값:
// { name: 'Autorun@2', dependencies: [ { name: 'Message@1.title' } ] }
```

#### 옳지 않은 예: observable 속성이 아닌 참조의 변경

```javascript
autorun(() => {
    console.log(message.title)
})
message = new Message("Bar", { name: "Martijn" }, ["Felicia", "Marcus"])
```

위 코드는 반응하지 **않습니다**. `message`가 변경되었지만 `message`는 observable 변수가 아니라 observable을 참조하는 변수일 뿐이며 변수(참조) 자체는 observable이 아니기 때문입니다.

#### 옳지 않은 예: 추적된 함수 외부에서의 역참조

```javascript
let title = message.title
autorun(() => {
    console.log(title)
})
message.updateTitle("Bar")
```

위 코드는 반응하지 **않습니다**. `message.title`은 `autorun` 밖에서 역참조되었으며, 역참조하는 순간에 `message.title`의 값(문자열 `"Foo"`)만을 담고 있기 때문입니다.
`title`은 observable이 아니므로 `autorun`에서 절대 반응하지 않습니다.

#### 옳은 예: 추적된 함수 내에서의 역참조

```javascript
autorun(() => {
    console.log(message.author.name)
})

runInAction(() => {
    message.author.name = "Sara"
})
runInAction(() => {
    message.author = { name: "Joe" }
})
```

위 코드는 두 가지 변화에 모두 반응합니다. 점 표기법으로 `author`와 `author.name`에 모두 접근했으므로 MobX가 해당 참조를 추적할 수 있습니다.

또한 `action` 외부에서의 변경을 허용하기 위해 `runInAction`을 사용했습니다.

#### 옳지 않은 예: 추적 없이 observable 객체에 로컬 참조 저장하기

```javascript
const author = message.author
autorun(() => {
    console.log(author.name)
})

runInAction(() => {
    message.author.name = "Sara"
})
runInAction(() => {
    message.author = { name: "Joe" }
})
```

`message.author`와 `author`가 동일한 객체이고 `.name` 속성은 autorun에서 역참조되었기 때문에 첫 번째 변경사항은 감지됩니다.
하지만 `autorun`에서 `message.author` 관계를 추적하지 않으므로 두 번째 변경사항은 감지되지 **않습니다**. autorun은 여전히 "이전의" `author`를 사용하고 있습니다.

#### 흔한 함정: console.log

```javascript
autorun(() => {
    console.log(message)
})

// 다시 트리거 되지 않습니다.
message.updateTitle("Hello world")
```

위의 예에서 업데이트된 message title은 autorun 내에서 사용되지 않기 때문에 출력되지 않습니다.
autorun은 observable이 아닌 변수 `message`에만 의존합니다. 다르게 말하면, MobX는 `autorun`에서 `title`이 사용되지 않았다고 인식합니다.

웹브라우저 디버깅 도구에서 위와 같이 사용하면 결국 `title`의 업데이트된 값을 발견할 수 있겠지만 이는 오해의 소지가 있습니다. 
autorun은 처음 호출될 때 한 번 실행됩니다. `title`의 업데이트 된 값을 발견할 수 있는 문제는 `console.log`가 비동기 함수이고 객체가 나중에 포맷되기 때문에 발생합니다. 즉, 디버깅 툴바에서 title을 따라가면 업데이트된 값을 찾을 수 있습니다. 그러나 `autorun`은 업데이트를 추적하지 않습니다.

위 작업을 수행하는 방법은 불변 데이터(immutable data) 또는 방어적 복사본(defensive copy)을 항상 `console.log`에 전달하는 것입니다. 따라서 다음 방법들은 모두 `message.title`의 변경사항에 반응합니다.

```javascript
autorun(() => {
    console.log(message.title) // `.title` observable이 명확히 사용되었습니다.
})

autorun(() => {
    console.log(mobx.toJS(message)) // toJS가 깊은(deep) 클론을 생성하기 때문에 message를 읽을 수 있습니다.
})

autorun(() => {
    console.log({ ...message }) // 프로세스의 `.title`을 사용하여 얕은(shallow) 클론을 생성합니다.
})

autorun(() => {
    console.log(JSON.stringify(message)) // 전체 구조를 읽습니다.
})
```

#### 옳은 예: 추적된 함수의 배열 속성에 액세스하기

```javascript
autorun(() => {
    console.log(message.likes.length)
})
message.likes.push("Jennifer")
```

위 코드는 예상대로 반응합니다. `.length`는 속성의 요소를 카운트합니다.
이 방법은 배열의 _어떠한_ 변화에도 반응합니다.
배열은 observable 객체와 map처럼 인덱스∙속성별로 추적되는 것이 아니라 전체로서 추적됩니다.

#### 옳지 않은 예: 추적된 함수의 범위를 벗어난 인덱스에 액세스하기

```javascript
autorun(() => {
    console.log(message.likes[0])
})
message.likes.push("Jennifer")
```

배열 인덱스는 속성 액세스로 계산되기 때문에 위의 샘플 데이터와 반응합니다. 그러나 **오직** 제공된 `index가 length보다 작은(index < length)` 경우에만 해당됩니다.
MobX는 아직 존재하지 않는 배열 인덱스를 추적하지 않습니다.
따라서 배열 인덱스를 기반으로 액세스하는 경우 항상 `.length`를 확인하세요.

#### 옳은 예: 추적된 함수의 배열 함수에 액세스하기

```javascript
autorun(() => {
    console.log(message.likes.join(", "))
})
message.likes.push("Jennifer")
```

위 코드는 예상대로 반응합니다. 배열을 변경하지 않는 모든 배열 함수는 자동으로 추적됩니다.

---

```javascript
autorun(() => {
    console.log(message.likes.join(", "))
})
message.likes[2] = "Jennifer"
```

위 코드는 예상대로 반응합니다. `index <= length`인 경우에만 모든 배열 인덱스 할당이 감지됩니다.

#### 옳지 않은 예: observable의 어떠한 속성에도 액세스하지 않고 "사용"하기

```javascript
autorun(() => {
    message.likes
})
message.likes.push("Jennifer")
```

위 코드는 예상대로 반응하지 **않습니다**. 단순히 `likes` 배열 자체가 `autorun`에서 사용되지 않고 배열에 대한 참조만 사용되고 있기 때문입니다.
반대로 `message.likes = ["Jennifer"]`는 잘 반응할 것입니다. 해당 문은 배열을 수정하는 것이 아니라 `likes` 속성 자체를 수정하기 때문입니다.

#### 옳은 예: 아직 존재하지 않는 map 엔트리 사용하기

```javascript
const twitterUrls = observable.map({
    Joe: "twitter.com/joey"
})

autorun(() => {
    console.log(twitterUrls.get("Sara"))
})

runInAction(() => {
    twitterUrls.set("Sara", "twitter.com/horsejs")
})
```

위 코드는 반응할 **것입니다**. observable map은 존재하지 않을 수 있는 엔트리를 관찰하도록 도와줍니다.
처음에는 `undefined`가 출력됩니다.
`twitterUrls.has("Sara")`를 사용하면 엔트리의 존재 여부를 먼저 확인할 수 있습니다.
따라서 동적 키 수집에 대한 프록시 지원이 없는 환경에서는 항상 observable map을 사용하세요. 프록시 지원이 있는 경우 observable map도 사용할 수 있지만 plain 객체를 사용할 수도 있습니다.

#### MobX는 비동기적으로 액세스된 데이터를 추적하지 않습니다

```javascript
function upperCaseAuthorName(author) {
    const baseName = author.name
    return baseName.toUpperCase()
}
autorun(() => {
    console.log(upperCaseAuthorName(message.author))
})

runInAction(() => {
    message.author.name = "Chesterton"
})
```

위 코드는 반응합니다. `author.name`이 `autorun`에 전달된 함수에서 자체적으로 역참조되지는 않았지만, MobX는 `upperCaseAuthorName`에서 발생하는 역참조를 추적할 것입니다. 해당 역참조는 autorun이 실행되는 _동안_ 발생하기 때문입니다.

---

```javascript
autorun(() => {
    setTimeout(() => console.log(message.likes.join(", ")), 10)
})

runInAction(() => {
    message.likes.push("Jennifer")
})
```

위 코드는 반응하지 **않습니다**. `autorun` 실행 중에는 어떠한 observable도 액세스 되지 않으며, 비동기 함수인 `setTimeout`을 실행하는 동안에만 액세스 되기 때문입니다.

[비동기 action](actions.md#asynchronous-actions) 섹션을 함께 확인해보세요.

#### observable이 아닌 객체 속성 사용하기

```javascript
autorun(() => {
    console.log(message.author.age)
})

runInAction(() => {
    message.author.age = 10
})
```

위 코드는 프록시를 지원하는 환경에서 React를 실행하는 경우 **반응합니다**.
이 작업은 `observable` 또는 `observable.object`로 생성된 객체에 대해서만 수행됩니다. 클래스 인스턴스의 새 속성은 자동으로 observable이 되지 않습니다.

_프록시를 지원하지 않는 환경_

위 코드는 반응하지 **않습니다**. MobX는 observable 속성만 추적할 수 있으며 'age'는 위에서 observable 속성으로 정의되지 않았습니다.

그러나 MobX에서 지원하는 `get` 및 `set` 메서드를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

```javascript
import { get, set } from "mobx"

autorun(() => {
    console.log(get(message.author, "age"))
})
set(message.author, "age", 10)
```

#### [프록시 지원이 없을 때] 옳지 않은 예: 아직 존재하지 않는 observable 객체 속성 사용하기

```javascript
autorun(() => {
    console.log(message.author.age)
})
extendObservable(message.author, {
    age: 10
})
```

위 코드는 반응하지 **않습니다**. MobX는 추적이 시작될 때 존재하지 않았던 observable 속성에 반응하지 않습니다.
두 문장이 바뀌거나 다른 observable로 인해 `autorun`이 재실행되면 `autorun`에서 `age`를 추적하기 시작합니다.

#### [프록시 지원이 없을 때] 옳은 예: MobX 유틸리티를 사용해서 객체를 읽거나(read) 쓰기(write)

프록시 지원이 없는 환경에서 observable 객체를 동적 컬렉션(dynamic collection)으로 사용하려면 MobX의 `get` 및 `set` API를 사용하세요.

다음 코드도 반응합니다.

```javascript
import { get, set, observable } from "mobx"

const twitterUrls = observable.object({
    Joe: "twitter.com/joey"
})

autorun(() => {
    console.log(get(twitterUrls, "Sara")) // `get` can track not yet existing properties.
})

runInAction(() => {
    set(twitterUrls, { Sara: "twitter.com/horsejs" })
})
```

더 자세한 내용은 [Collection utilities API](api.md#collection-utilities-)에서 확인하세요.

#### 요약

> MobX는 추적된 함수의 실행 과정에서 읽히는 _기존의_ **observable** _속성_에 반응합니다.
