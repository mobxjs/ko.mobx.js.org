---
title: MobX API Reference
sidebar_label: MobX API Reference
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# MobX API 참조

{🚀}으로 표시된 함수는 고급 기능이므로 일반적인 상황에서는 필요하지 않습니다.
중요한 API를 한 페이지에 설명하는 cheat sheet를 다운로드해 보세요.

<div class="cheat"><a href="https://gum.co/fSocU"><button title="Download the MobX 6 cheat sheet and sponsor the project">MobX 6 cheat sheet 다운로드하기</button></a></div>

## 핵심 API

_가장 중요한 MobX API입니다._

> [`observable`](#observable), [`computed`](#computed), [`reaction`](#reaction) and [`action`](#action)을 이해하는 것만으로도 MobX를 마스터하고 애플리케이션에 사용할 수 있습니다!

## observable 만들기

_observable을 만드는 방법_

### `makeObservable`

[**사용 방법**](observable-state.md#makeobservable): `makeObservable(target, annotations?, options?)`

속성, 전체 객체, 배열, Map, Set 모두 observable로 만들 수 있습니다.

### `makeAutoObservable`

[**사용 방법**](observable-state.md#makeautoobservable): `makeAutoObservable(target, overrides?, options?)`

속성, 객체, 배열, Map, Set을 자동으로 observable로 만듭니다.

### `extendObservable`

{🚀} 사용 방법: `extendObservable(target, properties, overrides?, options?)`

`target` 객체에 새 속성을 도입하여 즉시 observable로 만들 수 있습니다. 기본적으로 `Object.assign(target, properties); makeAutoObservable(target, overrides, options);`의 약어입니다. 하지만 `target`의 기존 속성은 건드리지 않습니다.

구식 생성자 함수는 `extendObservable`을 적절하게 활용할 수 있습니다.

```javascript
function Person(firstName, lastName) {
    extendObservable(this, { firstName, lastName })
}

const person = new Person("Michel", "Weststrate")
```

인스턴스화 후 기존 객체에 `extendObservable`을 사용하여 observable 필드를 추가할 수 있지만, 이 방식으로 observable 속성을 추가하는 것 자체가 관찰할 수 있다는 것은 아닙니다.

### `observable`

[**사용 방법**](observable-state.md#observable): `observable(source, overrides?, options?)` or `observable` _(주석)_

객체를 복사하여 observable로 만듭니다. source는 plain 객체, 배열, Map, Set이 될 수 있습니다. 기본적으로 `observable`은 재귀적으로 적용됩니다. 발견된 값 중 하나가 객체 또는 배열인 경우 해당 값도 `observable`을 통해 전달됩니다.

### `observable.object`

{🚀} [**사용 방법**](observable-state.md#observable): `observable.object(source, overrides?, options?)`

`observable(source, overrides?, options?)`에 대한 별칭입니다. 제공된 객체의 복제본을 만들고 모든 속성을 observable로 만듭니다.

### `observable.array`

{🚀} 사용 방법: `observable.array(initialValues?, options?)`

제공된 `initialValues`를 기반으로 새 observable 배열을 만듭니다.
observable 배열을 plain 배열로 다시 변환하려면, `.slice()` 메서드를 이용하거나 [toJS](#tojs)를 통해 재귀적으로 변환합니다.
기본 제공되는 배열 함수 외에도, 다음과 같은 기능을 observable 배열에서 사용할 수 있습니다.

-   `clear()`는 배열에서 현재 엔트리를 모두 제거합니다.
-   `replace(newItems)`는 배열의 모든 기존 엔트리를 새 엔트리로 바꿉니다.
-   `remove(value)`는 배열에서 value와 일치하는 단일 항목을 제거하고 항목이 발견되어 제거된 경우 `true`를 반환합니다.

배열의 값이 자동으로 observable로 바뀌면 안 되는 경우 `{ deep: false }` 옵션을 사용하여 배열을 얕은 observable로 만듭니다.

### `observable.map`

{🚀} 사용 방법: `observable.map(initialMap?, options?)`

제공된 `initialMap`을 기반으로 새 observable [ES6 Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)을 생성합니다.
특정 엔트리의 변경뿐만 아니라 추가 및 제거에도 반응하기를 원하는 경우 매우 유용합니다.
[Proxy 활성화](configuration.md#proxy-support)를 하지 않은 경우 observable Map을 만드는 것이 동적 키 컬렉션을 만드는데 권장되는 방법입니다.

내장된 Map 함수 외에도, observable Map에서 다음과 같은 기능을 사용할 수 있습니다.

-   `toJSON()`은 Map의 얕은 plain 객체 표현을 반환합니다. (깊은 복사를 원한다면 [toJS](#tojs)를 사용하세요.)
-   `merge(values)`는 제공된 `values`(plain 객체, 엔트리의 배열, string-keyed ES6 Map)의 모든 엔트리를 Map으로 복사합니다.
-   `replace(values)`는 Map의 전체 내용을 제공된 `values`로 바꿉니다.

Map의 값이 자동으로 observable로 바뀌면 안 되는 경우 `{ deep: false }` 옵션을 사용하여 Map을 얕은 observable로 만듭니다.

### `observable.set`

{🚀} 사용 방법: `observable.set(initialSet?, options?)`

제공된 `initialSet`을 기반으로 새 observable [ES6 Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)을 만듭니다. 값의 추가 및 제거를 관찰해야 하지만, 전체 컬렉션에서 값이 한 번만 나타날 수 있는 동적 set을 만들고 싶을 때마다 사용합니다.

Set의 값이 자동으로 observable로 바뀌면 안 되는 경우 `{ deep: false }` 옵션을 사용하여 Set을 얕은 observable로 만듭니다.

### `observable.ref`

[**사용 방법**](observable-state.md#available-annotations): `observable.ref` _(주석)_

`observable` 주석과 비슷하지만, 재할당만 추적합니다. 할당된 값 자체는 자동으로 observable이 되지 않습니다. 예를 들어 observable 필드에 변경할 수 없는 데이터를 저장하려는 경우에 사용하세요.

### `observable.shallow`

[**사용 방법**](observable-state.md#available-annotations): `observable.shallow` _(주석)_

 `observable.ref` 주석과 비슷하지만, 컬렉션에 사용됩니다. 할당된 모든 컬렉션은 observable이 되지만, 컬렉션 자체의 내용은 observable이 되지 않습니다.

### `observable.struct`

{🚀} [**사용 방법**](observable-state.md#available-annotations): `observable.struct` _(주석)_

구조적으로 현재 값과 동일한 모든 할당된 값을 무시한다는 점을 제외하고 `observable` 주석과 같습니다.

### `observable.deep`

{🚀} [**사용 방법**](observable-state.md#available-annotations): `observable.deep` _(주석)_

[`observable`](#observable) 주석의 별칭입니다.

### `observable.box`

{🚀} 사용 방법: `observable.box(value, options?)`

JavaScript의 모든 primitive 값은 변경할 수 없으므로 각각의 정의는 observable이 아닙니다.
일반적으로 MobX는 값이 포함된 _속성_을 관찰할 수 있게 하기 때문에 괜찮습니다.
드문 경우지만, 객체가 소유하지 않는 observable _primitive_를 갖는 것이 편리할 수 있습니다.
이러한 경우 _primitive_를 관리하는 observable _box_를 생성하는 것이 가능합니다.

`observable.box(value)`는 모든 값을 허용하고 box안에 저장합니다. 현재 값은 `.get()`을 통해 액세스할 수 있으며, `.set(newValue)`을 사용하여 업데이트 할 수도 있습니다.

```javascript
import { observable, autorun } from "mobx"

const cityName = observable.box("Vienna")

autorun(() => {
    console.log(cityName.get())
})
// 출력: 'Vienna'

cityName.set("Amsterdam")
// 출력: 'Amsterdam'
```

box안에 있는 값이 자동으로 observable로 바뀌면 안 되는 경우 `{ deep: false }` 옵션을 사용하여 box를 얕은 observable로 만드십시오.

---

## Actions

_action은 state를 수정하는 코드입니다._

### `action`

[**사용 방법**](actions.md): `action(fn)` or `action` _(주석)_

state를 수정하는 함수에 사용합니다.

### `runInAction`

{🚀} [**사용 방법**](actions.md#runinaction): `runInAction(fn)`

즉시 호출되는 일회성 action을 만듭니다.

### `flow`

[**사용 방법**](actions.md#using-flow-instead-of-async--await-): `flow(fn)` or `flow` _(주석)_

`async`·`await`에 대해 MobX 친화적으로 바꾼 대체제이며 취소 기능도 제공합니다.

### `flowResult`

[**사용 방법**](actions.md#using-flow-instead-of-async--await-): `flowResult(flowFunctionResult)`

TypeScript에서 사용되며, 제너레이터의 결과를 프라미스로 변환해주는 유틸리티입니다.
해당 유틸리티는 `flow`에 의해 수행된 프라미스 래핑을 타입에 맞게 수정한 것에 불과합니다. 런타임에 입력된 값을 직접 반환합니다.
---

## Computeds

_computed 값을 사용하여 다른 observable에서 정보를 얻을 수 있습니다._

### `computed`

[**사용 방법**](computeds.md): `computed(fn, options?)` or `computed(options?)` _(주석)_

다른 observable에서 파생된 observable 값을 생성하지만, 하위에 포함된 observable 중 하나라도 변경이 없을 때는 다시 계산되지 않습니다.

---

## React 통합

_`mobx-react`·`mobx-react-lite` 패키지로부터 사용할 수 있습니다._

### `observer`

[**사용 방법**](react-integration.md): `observer(component)`

observable이 변경될 때 함수 기반 또는 클래스 기반 리액트 컴포넌트를 재렌더링 하는 데 사용할 수 있는 고차 컴포넌트(Hoc) 입니다.

### `Observer`

[**사용 방법**](react-integration.md#callback-components-might-require-observer): `<Observer>{() => rendering}</Observer>`

지정된 렌더 함수를 렌더링하고, 렌더 함수에 사용된 observable 중 하나가 변경되면 자동으로 재렌더링됩니다.

### `useLocalObservable`

[**사용 방법**](react-integration.md#using-local-observable-state-in-observer-components): `useLocalObservable(() => source, annotations?)`

`makeObservable`을 통해 새로운 observable 객체를 생성하며, 생성된 객체는 컴포넌트의 전체 라이프사이클 동안 observable로 유지됩니다.

---

## Reactions

_reaction의 목표는 자동으로 발생하는 부수 효과를 모델링 하는 것입니다._

### `autorun`

[**사용 방법**](reactions.md#autorun): `autorun(() => effect, options?)`

변경 사항을 관찰할 때마다 함수를 다시 실행합니다.

### `reaction`

[**사용 방법**](reactions.md#reaction): `reaction(() => data, data => effect, options?)`

선택한 data가 변경될 때 부수 효과를 다시 실행합니다.

### `when`

[**사용 방법**](reactions.md#when): `when(() => condition, () => effect, options?)` or `await when(() => condition, options?)`

observable condition이 true가 될 때 부수 효과를 한 번 실행합니다.

---

## 유틸리티

_observable 객체 또는 computed 값으로 작업하는 것을 더 편리하게 만들어주는 유틸리티입니다. 더 작은 유틸리티는 [mobx-utils](https://github.com/mobxjs/mobx-utils) 패키지에서 찾을 수 있습니다._

### `onReactionError`

{🚀} 사용 방법: `onReactionError(handler: (error: any, derivation) => void)`

_reaction_에서 발생하는 모든 오류에 대해 호출되는 전역 오류 리스너를 연결합니다. 모니터링 또는 테스트 용도로 사용할 수 있습니다.

### `intercept`

{🚀} [**사용 방법**](intercept-and-observe.md#intercept): `intercept(propertyName|array|object|Set|Map, listener)`

observable API에 적용되기 전에 변경 사항을 가로챕니다. 가로채기를 중지하는 disposer 함수를 반환합니다.

### `observe`

{🚀} [**사용 방법**](intercept-and-observe.md#observe): `observe(propertyName|array|object|Set|Map, listener)`

단일 observable 값을 관찰하는데 사용할 수 있는 로우 레벨 API입니다. 가로채기를 중지하는 disposer 함수를 반환합니다.

### `onBecomeObserved`

{🚀} [**사용 방법**](lazy-observables.md): `onBecomeObserved(observable, property?, listener: () => void)`

무언가 observed로 전환될 때를 위한 Hook입니다.

### `onBecomeUnobserved`

{🚀} [**사용 방법**](lazy-observables.md): `onBecomeUnobserved(observable, property?, listener: () => void)`

무언가를 observerd로 설정하지 않을 때를 위한 Hook입니다.

### `toJS`

[**사용 방법**](observable-state.md#converting-observables-back-to-vanilla-javascript-collections): `toJS(value)`

재귀적으로 observable 객체를 JavaScript _구조_로 변환합니다. observable 배열, 객체, Map 그리고 primitive를 지원합니다.
computed 값 및 non-enumerable 속성은 결과에 포함되지 않습니다.
더 복잡한 (역)직렬화 시나리오의 경우 클래스에 `toJSON` 메서드를 제공하거나 [serializr](https://github.com/mobxjs/serializr)와 같은 직렬화 라이브러리를 사용하는 것이 좋습니다.

```javascript
const obj = mobx.observable({
    x: 1
})

const clone = mobx.toJS(obj)

console.log(mobx.isObservableObject(obj)) // true
console.log(mobx.isObservableObject(clone)) // false
```

---

## 환경설정

_MobX 인스턴스를 미세 조정합니다._

### `configure`

[**사용 방법**](configuration.md): 활성 MobX 인스턴스에 대한 전역 동작 설정을 지정합니다.
MobX의 전체적인 동작 방식 변경에 사용합니다.

---

## Collection 유틸리티 {🚀}

_동일한 일반 API를 사용하여 observable 배열, 객체 그리고 Map을 조작할 수 있습니다. 이 기능은 일반적으로 필요하지는 않지만, [Proxy가 지원되지 않는 환경](configuration.md#limitations-without-proxy-support)에서는 유용할 수 있습니다._

### `values`

{🚀} [**사용 방법**](collection-utilities.md): `values(array|object|Set|Map)`

컬렉션의 모든 값을 배열로 반환합니다.

### `keys`

{🚀} [**사용 방법**](collection-utilities.md): `keys(array|object|Set|Map)`

컬렉션의 모든 key와 index를 배열로 반환합니다.

### `entries`

{🚀} [**사용 방법**](collection-utilities.md): `entries(array|object|Set|Map)`

컬렉션에 있는 모든 항목의 `[key, value]` 쌍을 배열로 반환합니다.

### `set`

{🚀} [**사용 방법**](collection-utilities.md): `set(array|object|Map, key, value)`

컬렉션을 업데이트합니다.

### `remove`

{🚀} [**사용 방법**](collection-utilities.md): `remove(array|object|Map, key)`

컬렉션에서 항목을 제거합니다.

### `has`

{🚀} [**사용 방법**](collection-utilities.md): `has(array|object|Map, key)`

컬렉션에 해당 멤버가 있는지 체크합니다.

### `get`

{🚀} [**사용 방법**](collection-utilities.md): `get(array|object|Map, key)`

키를 사용하여 컬렉션에서 값을 가져옵니다.

---

## Introspection 유틸리티 {🚀}

_MobX의 내부 state를 검사하거나 MobX 위에 멋진 도구를 구축하려는 경우에 유용하게 사용할 수 있는 유틸리티입니다._

### `isObservable`

{🚀} 사용 방법: `isObservable(array|object|Set|Map)`

MobX에 의해 만들어진 객체 또는 컬렉션인지 확인합니다.

### `isObservableProp`

{🚀} 사용 방법: `isObservableProp(object, propertyName)`

해당 속성이 observable인지 확인합니다.

### `isObservableArray`

{🚀} 사용 방법: `isObservableArray(array)`

값이 observable 배열인지 확인합니다.

### `isObservableObject`

{🚀} 사용 방법: `isObservableObject(object)`

값이 observable 객체인지 확인합니다.

### `isObservableSet`

{🚀} 사용 방법: `isObservableSet(set)`

값이 observable Set인지 확인합니다.

### `isObservableMap`

{🚀} 사용 방법: `isObservableMap(map)`

값이 observable Map인지 확인합니다.

### `isBoxedObservable`

{🚀} 사용 방법: `isBoxedObservable(value)`

값이 `observable.box`를 사용하여 만든 observable.box인지 확인합니다.

### `isAction`

{🚀} 사용 방법: `isAction(func)`

함수가 `action`으로 표시되어 있는지 확인합니다.

### `isComputed`

{🚀} 사용 방법: `isComputed(boxedComputed)`

`computed(() => expr)`을 사용하여 만든 box computed 값인지 확인합니다.

### `isComputedProp`

{🚀} 사용 방법: `isComputedProp(object, propertyName)`

computed 속성인지 확인합니다.

### `trace`

{🚀} [**사용 방법**](analyzing-reactivity.md): `trace()`, `trace(true)` _(디버거 입장)_ 또는 `trace(object, propertyName, enterDebugger?)`

observer, reaction 또는 computed 값 내부에서 사용해야 합니다. 값이 무효가 되었을 때 로그를 남기거나, _true_로 호출된 경우 디버거 중단점을 설정합니다.

### `spy`

{🚀} [**사용 방법**](analyzing-reactivity.md#spy): `spy(eventListener)`

MobX에서 발생하는 모든 이벤트를 수신하는 전역 스파이 리스너를 등록합니다.

### `getDebugName`

{🚀} [**사용 방법**](analyzing-reactivity.md#getdebugname): `getDebugName(reaction|array|Set|Map)` 또는 `getDebugName(object|Map, propertyName)`

observable 또는 reaction에 대해 (생성된) 친숙한 디버그 이름을 반환합니다.

### `getDependencyTree`

{🚀} [**사용 방법**](analyzing-reactivity.md#getdependencytree): `getDependencyTree(object, computedPropertyName)`

주어진 reaction·computation이 현재 의존하고 있는 모든 observable 트리 구조를 반환합니다.

### `getObserverTree`

{🚀} [**사용 방법**](analyzing-reactivity.md#getobservertree): `getObserverTree(array|Set|Map)` 또는 `getObserverTree(object|Map, propertyName)`

주어진 observable을 관찰하는 모든 reaction·computation을 포함하는 트리 구조를 반환합니다.

---

## MobX 확장 {🚀}

_드문 경우지만 MobX 자체를 확장하려고 할 때 사용할 수 있습니다._

### `createAtom`

{🚀} [**사용 방법**](custom-observables.md): `createAtom(name, onBecomeObserved?, onBecomeUnobserved?)`

자체 observable 데이터 구조를 만들어 MobX에 연결합니다. 모든 observable 데이터 타입에서 내부적으로 사용됩니다. Atom은 MobX에 알리는 두 가지 _report_ 메서드를 제공합니다.

-   `reportObserved()`: atom이 관찰되었고, 현재 derivation의 종속성 트리의 일부로 간주하여야 합니다.
-   `reportChanged()`: atom이 변경되었으며, atom에 의한 모든 derivation은 무효가 되어야 합니다.

### `getAtom`

{🚀} [**사용 방법**](analyzing-reactivity.md#getatom): `getAtom(thing, property?)`

backing atom을 반환합니다.

### `transaction`

{🚀} 사용 방법: `transaction(worker: () => any)`

_트랜잭션은 로우 레벨 API입니다. 트랜잭션 대신에 [`action`](#action) 또는 [`runInAction`](#runinaction)을 사용하는 것을 추천합니다._

트랜잭션이 끝날 때까지 observer에게 알리지 않고 업데이트 일괄 처리를 하는 데 사용됩니다. [`untracked`](#untracked)와 마찬가지로 `action`에 의해 자동으로 적용되므로, `transaction`을 직접 사용하는 것보다 action을 사용하는 것이 더 좋습니다.

매개변수가 없는 단일 `worker` 함수를 인수로 사용하고 이 함수에 의해 반환된 값을 반환합니다.
`transaction`은 완전히 동기적으로 실행되며 중첩될 수 있습니다. 가장 바깥쪽 `transaction`이 완료된 후에 보류 중인 reaction이 실행됩니다.

```javascript
import { observable, transaction, autorun } from "mobx"

const numbers = observable([])

autorun(() => console.log(numbers.length, "numbers!"))
// 출력: '0 numbers!'

transaction(() => {
    transaction(() => {
        numbers.push(1)
        numbers.push(2)
    })
    numbers.push(3)
})
// 출력: '3 numbers!'
```

### `untracked`

{🚀} 사용 방법: `untracked(worker: () => any)`

_Untracked는 로우 레벨 API입니다. untracked 대신에 [`reaction`](#reaction), [`action`](#action) 또는 [`runInAction`](#runinaction)을 사용하는 것을 추천합니다._

observer를 설정하지 않고 코드를 실행합니다. `transaction`과 마찬가지로 `untracked`는 action에 의해 자동으로 적용되므로, 일반적으로 `untracked`를 직접 사용하는 것보다는 `action`을 사용하는 것이 더 적합합니다.

```javascript
const person = observable({
    firstName: "Michel",
    lastName: "Weststrate"
})

autorun(() => {
    console.log(
        person.lastName,
        ",",
        // untracked 블록은 종속성을 설정하지 않고
        // person의 firstName을 반환합니다.
        untracked(() => person.firstName)
    )
})
// 출력: 'Weststrate, Michel'

person.firstName = "G.K."
// 츌력되지 않습니다.

person.lastName = "Chesterton"
// 출력: 'Chesterton, G.K.'
```
