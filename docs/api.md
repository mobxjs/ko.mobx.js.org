---
title: MobX API Reference
sidebar_label: MobX API Reference
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# MobX API ์ฐธ์กฐ

{๐}์ผ๋ก ํ์๋ ํจ์๋ ๊ณ ๊ธ ๊ธฐ๋ฅ์ด๋ฏ๋ก ์ผ๋ฐ์ ์ธ ์ํฉ์์๋ ํ์ํ์ง ์์ต๋๋ค.
์ค์ํ API๋ฅผ ํ ํ์ด์ง์ ์ค๋ชํ๋ cheat sheet๋ฅผ ๋ค์ด๋ก๋ํด ๋ณด์ธ์.

<div class="cheat"><a href="https://gum.co/fSocU"><button title="Download the MobX 6 cheat sheet and sponsor the project">MobX 6 cheat sheet ๋ค์ด๋ก๋ํ๊ธฐ</button></a></div>

## ํต์ฌ API

_๊ฐ์ฅ ์ค์ํ MobX API์๋๋ค._

> [`observable`](#observable), [`computed`](#computed), [`reaction`](#reaction) and [`action`](#action)์ ์ดํดํ๋ ๊ฒ๋ง์ผ๋ก๋ MobX๋ฅผ ๋ง์คํฐํ๊ณ  ์ ํ๋ฆฌ์ผ์ด์์ ์ฌ์ฉํ  ์ ์์ต๋๋ค!

## observable ๋ง๋ค๊ธฐ

_observable์ ๋ง๋๋ ๋ฐฉ๋ฒ_

### `makeObservable`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#makeobservable): `makeObservable(target, annotations?, options?)`

์์ฑ, ์ ์ฒด ๊ฐ์ฒด, ๋ฐฐ์ด, Map, Set ๋ชจ๋ observable๋ก ๋ง๋ค ์ ์์ต๋๋ค.

### `makeAutoObservable`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#makeautoobservable): `makeAutoObservable(target, overrides?, options?)`

์์ฑ, ๊ฐ์ฒด, ๋ฐฐ์ด, Map, Set์ ์๋์ผ๋ก observable๋ก ๋ง๋ญ๋๋ค.

### `extendObservable`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `extendObservable(target, properties, overrides?, options?)`

`target` ๊ฐ์ฒด์ ์ ์์ฑ์ ๋์ํ์ฌ ์ฆ์ observable๋ก ๋ง๋ค ์ ์์ต๋๋ค. ๊ธฐ๋ณธ์ ์ผ๋ก `Object.assign(target, properties); makeAutoObservable(target, overrides, options);`์ ์ฝ์ด์๋๋ค. ํ์ง๋ง `target`์ ๊ธฐ์กด ์์ฑ์ ๊ฑด๋๋ฆฌ์ง ์์ต๋๋ค.

๊ตฌ์ ์์ฑ์ ํจ์๋ `extendObservable`์ ์ ์ ํ๊ฒ ํ์ฉํ  ์ ์์ต๋๋ค.

```javascript
function Person(firstName, lastName) {
    extendObservable(this, { firstName, lastName })
}

const person = new Person("Michel", "Weststrate")
```

์ธ์คํด์คํ ํ ๊ธฐ์กด ๊ฐ์ฒด์ `extendObservable`์ ์ฌ์ฉํ์ฌ observable ํ๋๋ฅผ ์ถ๊ฐํ  ์ ์์ง๋ง, ์ด ๋ฐฉ์์ผ๋ก observable ์์ฑ์ ์ถ๊ฐํ๋ ๊ฒ ์์ฒด๊ฐ ๊ด์ฐฐํ  ์ ์๋ค๋ ๊ฒ์ ์๋๋๋ค.

### `observable`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#observable): `observable(source, overrides?, options?)` or `observable` _(์ฃผ์)_

๊ฐ์ฒด๋ฅผ ๋ณต์ฌํ์ฌ observable๋ก ๋ง๋ญ๋๋ค. source๋ plain ๊ฐ์ฒด, ๋ฐฐ์ด, Map, Set์ด ๋  ์ ์์ต๋๋ค. ๊ธฐ๋ณธ์ ์ผ๋ก `observable`์ ์ฌ๊ท์ ์ผ๋ก ์ ์ฉ๋ฉ๋๋ค. ๋ฐ๊ฒฌ๋ ๊ฐ ์ค ํ๋๊ฐ ๊ฐ์ฒด ๋๋ ๋ฐฐ์ด์ธ ๊ฒฝ์ฐ ํด๋น ๊ฐ๋ `observable`์ ํตํด ์ ๋ฌ๋ฉ๋๋ค.

### `observable.object`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#observable): `observable.object(source, overrides?, options?)`

`observable(source, overrides?, options?)`์ ๋ํ ๋ณ์นญ์๋๋ค. ์ ๊ณต๋ ๊ฐ์ฒด์ ๋ณต์ ๋ณธ์ ๋ง๋ค๊ณ  ๋ชจ๋  ์์ฑ์ observable๋ก ๋ง๋ญ๋๋ค.

### `observable.array`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `observable.array(initialValues?, options?)`

์ ๊ณต๋ `initialValues`๋ฅผ ๊ธฐ๋ฐ์ผ๋ก ์ observable ๋ฐฐ์ด์ ๋ง๋ญ๋๋ค.
observable ๋ฐฐ์ด์ plain ๋ฐฐ์ด๋ก ๋ค์ ๋ณํํ๋ ค๋ฉด, `.slice()` ๋ฉ์๋๋ฅผ ์ด์ฉํ๊ฑฐ๋ [toJS](#tojs)๋ฅผ ํตํด ์ฌ๊ท์ ์ผ๋ก ๋ณํํฉ๋๋ค.
๊ธฐ๋ณธ ์ ๊ณต๋๋ ๋ฐฐ์ด ํจ์ ์ธ์๋, ๋ค์๊ณผ ๊ฐ์ ๊ธฐ๋ฅ์ observable ๋ฐฐ์ด์์ ์ฌ์ฉํ  ์ ์์ต๋๋ค.

-   `clear()`๋ ๋ฐฐ์ด์์ ํ์ฌ ์ํธ๋ฆฌ๋ฅผ ๋ชจ๋ ์ ๊ฑฐํฉ๋๋ค.
-   `replace(newItems)`๋ ๋ฐฐ์ด์ ๋ชจ๋  ๊ธฐ์กด ์ํธ๋ฆฌ๋ฅผ ์ ์ํธ๋ฆฌ๋ก ๋ฐ๊ฟ๋๋ค.
-   `remove(value)`๋ ๋ฐฐ์ด์์ value์ ์ผ์นํ๋ ๋จ์ผ ํญ๋ชฉ์ ์ ๊ฑฐํ๊ณ  ํญ๋ชฉ์ด ๋ฐ๊ฒฌ๋์ด ์ ๊ฑฐ๋ ๊ฒฝ์ฐ `true`๋ฅผ ๋ฐํํฉ๋๋ค.

๋ฐฐ์ด์ ๊ฐ์ด ์๋์ผ๋ก observable๋ก ๋ฐ๋๋ฉด ์ ๋๋ ๊ฒฝ์ฐ `{ deep: false }` ์ต์์ ์ฌ์ฉํ์ฌ ๋ฐฐ์ด์ ์์ observable๋ก ๋ง๋ญ๋๋ค.

### `observable.map`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `observable.map(initialMap?, options?)`

์ ๊ณต๋ `initialMap`์ ๊ธฐ๋ฐ์ผ๋ก ์ observable [ES6 Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)์ ์์ฑํฉ๋๋ค.
ํน์  ์ํธ๋ฆฌ์ ๋ณ๊ฒฝ๋ฟ๋ง ์๋๋ผ ์ถ๊ฐ ๋ฐ ์ ๊ฑฐ์๋ ๋ฐ์ํ๊ธฐ๋ฅผ ์ํ๋ ๊ฒฝ์ฐ ๋งค์ฐ ์ ์ฉํฉ๋๋ค.
[Proxy ํ์ฑํ](configuration.md#proxy-support)๋ฅผ ํ์ง ์์ ๊ฒฝ์ฐ observable Map์ ๋ง๋๋ ๊ฒ์ด ๋์  ํค ์ปฌ๋ ์์ ๋ง๋๋๋ฐ ๊ถ์ฅ๋๋ ๋ฐฉ๋ฒ์๋๋ค.

๋ด์ฅ๋ Map ํจ์ ์ธ์๋, observable Map์์ ๋ค์๊ณผ ๊ฐ์ ๊ธฐ๋ฅ์ ์ฌ์ฉํ  ์ ์์ต๋๋ค.

-   `toJSON()`์ Map์ ์์ plain ๊ฐ์ฒด ํํ์ ๋ฐํํฉ๋๋ค. (๊น์ ๋ณต์ฌ๋ฅผ ์ํ๋ค๋ฉด [toJS](#tojs)๋ฅผ ์ฌ์ฉํ์ธ์.)
-   `merge(values)`๋ ์ ๊ณต๋ `values`(plain ๊ฐ์ฒด, ์ํธ๋ฆฌ์ ๋ฐฐ์ด, string-keyed ES6 Map)์ ๋ชจ๋  ์ํธ๋ฆฌ๋ฅผ Map์ผ๋ก ๋ณต์ฌํฉ๋๋ค.
-   `replace(values)`๋ Map์ ์ ์ฒด ๋ด์ฉ์ ์ ๊ณต๋ `values`๋ก ๋ฐ๊ฟ๋๋ค.

Map์ ๊ฐ์ด ์๋์ผ๋ก observable๋ก ๋ฐ๋๋ฉด ์ ๋๋ ๊ฒฝ์ฐ `{ deep: false }` ์ต์์ ์ฌ์ฉํ์ฌ Map์ ์์ observable๋ก ๋ง๋ญ๋๋ค.

### `observable.set`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `observable.set(initialSet?, options?)`

์ ๊ณต๋ `initialSet`์ ๊ธฐ๋ฐ์ผ๋ก ์ observable [ES6 Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)์ ๋ง๋ญ๋๋ค. ๊ฐ์ ์ถ๊ฐ ๋ฐ ์ ๊ฑฐ๋ฅผ ๊ด์ฐฐํด์ผ ํ์ง๋ง, ์ ์ฒด ์ปฌ๋ ์์์ ๊ฐ์ด ํ ๋ฒ๋ง ๋ํ๋  ์ ์๋ ๋์  set์ ๋ง๋ค๊ณ  ์ถ์ ๋๋ง๋ค ์ฌ์ฉํฉ๋๋ค.

Set์ ๊ฐ์ด ์๋์ผ๋ก observable๋ก ๋ฐ๋๋ฉด ์ ๋๋ ๊ฒฝ์ฐ `{ deep: false }` ์ต์์ ์ฌ์ฉํ์ฌ Set์ ์์ observable๋ก ๋ง๋ญ๋๋ค.

### `observable.ref`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#available-annotations): `observable.ref` _(์ฃผ์)_

`observable` ์ฃผ์๊ณผ ๋น์ทํ์ง๋ง, ์ฌํ ๋น๋ง ์ถ์ ํฉ๋๋ค. ํ ๋น๋ ๊ฐ ์์ฒด๋ ์๋์ผ๋ก observable์ด ๋์ง ์์ต๋๋ค. ์๋ฅผ ๋ค์ด observable ํ๋์ ๋ณ๊ฒฝํ  ์ ์๋ ๋ฐ์ดํฐ๋ฅผ ์ ์ฅํ๋ ค๋ ๊ฒฝ์ฐ์ ์ฌ์ฉํ์ธ์.

### `observable.shallow`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#available-annotations): `observable.shallow` _(์ฃผ์)_

 `observable.ref` ์ฃผ์๊ณผ ๋น์ทํ์ง๋ง, ์ปฌ๋ ์์ ์ฌ์ฉ๋ฉ๋๋ค. ํ ๋น๋ ๋ชจ๋  ์ปฌ๋ ์์ observable์ด ๋์ง๋ง, ์ปฌ๋ ์ ์์ฒด์ ๋ด์ฉ์ observable์ด ๋์ง ์์ต๋๋ค.

### `observable.struct`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#available-annotations): `observable.struct` _(์ฃผ์)_

๊ตฌ์กฐ์ ์ผ๋ก ํ์ฌ ๊ฐ๊ณผ ๋์ผํ ๋ชจ๋  ํ ๋น๋ ๊ฐ์ ๋ฌด์ํ๋ค๋ ์ ์ ์ ์ธํ๊ณ  `observable` ์ฃผ์๊ณผ ๊ฐ์ต๋๋ค.

### `observable.deep`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#available-annotations): `observable.deep` _(์ฃผ์)_

[`observable`](#observable) ์ฃผ์์ ๋ณ์นญ์๋๋ค.

### `observable.box`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `observable.box(value, options?)`

JavaScript์ ๋ชจ๋  primitive ๊ฐ์ ๋ณ๊ฒฝํ  ์ ์์ผ๋ฏ๋ก ๊ฐ๊ฐ์ ์ ์๋ observable์ด ์๋๋๋ค.
์ผ๋ฐ์ ์ผ๋ก MobX๋ ๊ฐ์ด ํฌํจ๋ _์์ฑ_์ ๊ด์ฐฐํ  ์ ์๊ฒ ํ๊ธฐ ๋๋ฌธ์ ๊ด์ฐฎ์ต๋๋ค.
๋๋ฌธ ๊ฒฝ์ฐ์ง๋ง, ๊ฐ์ฒด๊ฐ ์์ ํ์ง ์๋ observable _primitive_๋ฅผ ๊ฐ๋ ๊ฒ์ด ํธ๋ฆฌํ  ์ ์์ต๋๋ค.
์ด๋ฌํ ๊ฒฝ์ฐ _primitive_๋ฅผ ๊ด๋ฆฌํ๋ observable _box_๋ฅผ ์์ฑํ๋ ๊ฒ์ด ๊ฐ๋ฅํฉ๋๋ค.

`observable.box(value)`๋ ๋ชจ๋  ๊ฐ์ ํ์ฉํ๊ณ  box์์ ์ ์ฅํฉ๋๋ค. ํ์ฌ ๊ฐ์ `.get()`์ ํตํด ์ก์ธ์คํ  ์ ์์ผ๋ฉฐ, `.set(newValue)`์ ์ฌ์ฉํ์ฌ ์๋ฐ์ดํธ ํ  ์๋ ์์ต๋๋ค.

```javascript
import { observable, autorun } from "mobx"

const cityName = observable.box("Vienna")

autorun(() => {
    console.log(cityName.get())
})
// ์ถ๋ ฅ: 'Vienna'

cityName.set("Amsterdam")
// ์ถ๋ ฅ: 'Amsterdam'
```

box์์ ์๋ ๊ฐ์ด ์๋์ผ๋ก observable๋ก ๋ฐ๋๋ฉด ์ ๋๋ ๊ฒฝ์ฐ `{ deep: false }` ์ต์์ ์ฌ์ฉํ์ฌ box๋ฅผ ์์ observable๋ก ๋ง๋์ญ์์ค.

---

## Actions

_action์ state๋ฅผ ์์ ํ๋ ์ฝ๋์๋๋ค._

### `action`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](actions.md): `action(fn)` or `action` _(์ฃผ์)_

state๋ฅผ ์์ ํ๋ ํจ์์ ์ฌ์ฉํฉ๋๋ค.

### `runInAction`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](actions.md#runinaction): `runInAction(fn)`

์ฆ์ ํธ์ถ๋๋ ์ผํ์ฑ action์ ๋ง๋ญ๋๋ค.

### `flow`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](actions.md#using-flow-instead-of-async--await-): `flow(fn)` or `flow` _(์ฃผ์)_

`async`ยท`await`์ ๋ํด MobX ์นํ์ ์ผ๋ก ๋ฐ๊พผ ๋์ฒด์ ์ด๋ฉฐ ์ทจ์ ๊ธฐ๋ฅ๋ ์ ๊ณตํฉ๋๋ค.

### `flowResult`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](actions.md#using-flow-instead-of-async--await-): `flowResult(flowFunctionResult)`

TypeScript์์ ์ฌ์ฉ๋๋ฉฐ, ์ ๋๋ ์ดํฐ์ ๊ฒฐ๊ณผ๋ฅผ ํ๋ผ๋ฏธ์ค๋ก ๋ณํํด์ฃผ๋ ์ ํธ๋ฆฌํฐ์๋๋ค.
ํด๋น ์ ํธ๋ฆฌํฐ๋ `flow`์ ์ํด ์ํ๋ ํ๋ผ๋ฏธ์ค ๋ํ์ ํ์์ ๋ง๊ฒ ์์ ํ ๊ฒ์ ๋ถ๊ณผํฉ๋๋ค. ๋ฐํ์์ ์๋ ฅ๋ ๊ฐ์ ์ง์  ๋ฐํํฉ๋๋ค.
---

## Computeds

_computed ๊ฐ์ ์ฌ์ฉํ์ฌ ๋ค๋ฅธ observable์์ ์ ๋ณด๋ฅผ ์ป์ ์ ์์ต๋๋ค._

### `computed`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](computeds.md): `computed(fn, options?)` or `computed(options?)` _(์ฃผ์)_

๋ค๋ฅธ observable์์ ํ์๋ observable ๊ฐ์ ์์ฑํ์ง๋ง, ํ์์ ํฌํจ๋ observable ์ค ํ๋๋ผ๋ ๋ณ๊ฒฝ์ด ์์ ๋๋ ๋ค์ ๊ณ์ฐ๋์ง ์์ต๋๋ค.

---

## React ํตํฉ

_`mobx-react`ยท`mobx-react-lite` ํจํค์ง๋ก๋ถํฐ ์ฌ์ฉํ  ์ ์์ต๋๋ค._

### `observer`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](react-integration.md): `observer(component)`

observable์ด ๋ณ๊ฒฝ๋  ๋ ํจ์ ๊ธฐ๋ฐ ๋๋ ํด๋์ค ๊ธฐ๋ฐ ๋ฆฌ์กํธ ์ปดํฌ๋ํธ๋ฅผ ์ฌ๋ ๋๋ง ํ๋ ๋ฐ ์ฌ์ฉํ  ์ ์๋ ๊ณ ์ฐจ ์ปดํฌ๋ํธ(Hoc) ์๋๋ค.

### `Observer`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](react-integration.md#callback-components-might-require-observer): `<Observer>{() => rendering}</Observer>`

์ง์ ๋ ๋ ๋ ํจ์๋ฅผ ๋ ๋๋งํ๊ณ , ๋ ๋ ํจ์์ ์ฌ์ฉ๋ observable ์ค ํ๋๊ฐ ๋ณ๊ฒฝ๋๋ฉด ์๋์ผ๋ก ์ฌ๋ ๋๋ง๋ฉ๋๋ค.

### `useLocalObservable`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](react-integration.md#using-local-observable-state-in-observer-components): `useLocalObservable(() => source, annotations?)`

`makeObservable`์ ํตํด ์๋ก์ด observable ๊ฐ์ฒด๋ฅผ ์์ฑํ๋ฉฐ, ์์ฑ๋ ๊ฐ์ฒด๋ ์ปดํฌ๋ํธ์ ์ ์ฒด ๋ผ์ดํ์ฌ์ดํด ๋์ observable๋ก ์ ์ง๋ฉ๋๋ค.

---

## Reactions

_reaction์ ๋ชฉํ๋ ์๋์ผ๋ก ๋ฐ์ํ๋ ๋ถ์ ํจ๊ณผ๋ฅผ ๋ชจ๋ธ๋ง ํ๋ ๊ฒ์๋๋ค._

### `autorun`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](reactions.md#autorun): `autorun(() => effect, options?)`

๋ณ๊ฒฝ ์ฌํญ์ ๊ด์ฐฐํ  ๋๋ง๋ค ํจ์๋ฅผ ๋ค์ ์คํํฉ๋๋ค.

### `reaction`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](reactions.md#reaction): `reaction(() => data, data => effect, options?)`

์ ํํ data๊ฐ ๋ณ๊ฒฝ๋  ๋ ๋ถ์ ํจ๊ณผ๋ฅผ ๋ค์ ์คํํฉ๋๋ค.

### `when`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](reactions.md#when): `when(() => condition, () => effect, options?)` or `await when(() => condition, options?)`

observable condition์ด true๊ฐ ๋  ๋ ๋ถ์ ํจ๊ณผ๋ฅผ ํ ๋ฒ ์คํํฉ๋๋ค.

---

## ์ ํธ๋ฆฌํฐ

_observable ๊ฐ์ฒด ๋๋ computed ๊ฐ์ผ๋ก ์์ํ๋ ๊ฒ์ ๋ ํธ๋ฆฌํ๊ฒ ๋ง๋ค์ด์ฃผ๋ ์ ํธ๋ฆฌํฐ์๋๋ค. ๋ ์์ ์ ํธ๋ฆฌํฐ๋ [mobx-utils](https://github.com/mobxjs/mobx-utils) ํจํค์ง์์ ์ฐพ์ ์ ์์ต๋๋ค._

### `onReactionError`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `onReactionError(handler: (error: any, derivation) => void)`

_reaction_์์ ๋ฐ์ํ๋ ๋ชจ๋  ์ค๋ฅ์ ๋ํด ํธ์ถ๋๋ ์ ์ญ ์ค๋ฅ ๋ฆฌ์ค๋๋ฅผ ์ฐ๊ฒฐํฉ๋๋ค. ๋ชจ๋ํฐ๋ง ๋๋ ํ์คํธ ์ฉ๋๋ก ์ฌ์ฉํ  ์ ์์ต๋๋ค.

### `intercept`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](intercept-and-observe.md#intercept): `intercept(propertyName|array|object|Set|Map, listener)`

observable API์ ์ ์ฉ๋๊ธฐ ์ ์ ๋ณ๊ฒฝ ์ฌํญ์ ๊ฐ๋ก์ฑ๋๋ค. ๊ฐ๋ก์ฑ๊ธฐ๋ฅผ ์ค์งํ๋ disposer ํจ์๋ฅผ ๋ฐํํฉ๋๋ค.

### `observe`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](intercept-and-observe.md#observe): `observe(propertyName|array|object|Set|Map, listener)`

๋จ์ผ observable ๊ฐ์ ๊ด์ฐฐํ๋๋ฐ ์ฌ์ฉํ  ์ ์๋ ๋ก์ฐ ๋ ๋ฒจ API์๋๋ค. ๊ฐ๋ก์ฑ๊ธฐ๋ฅผ ์ค์งํ๋ disposer ํจ์๋ฅผ ๋ฐํํฉ๋๋ค.

### `onBecomeObserved`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](lazy-observables.md): `onBecomeObserved(observable, property?, listener: () => void)`

๋ฌด์ธ๊ฐ observed๋ก ์ ํ๋  ๋๋ฅผ ์ํ Hook์๋๋ค.

### `onBecomeUnobserved`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](lazy-observables.md): `onBecomeUnobserved(observable, property?, listener: () => void)`

๋ฌด์ธ๊ฐ๋ฅผ observerd๋ก ์ค์ ํ์ง ์์ ๋๋ฅผ ์ํ Hook์๋๋ค.

### `toJS`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](observable-state.md#converting-observables-back-to-vanilla-javascript-collections): `toJS(value)`

์ฌ๊ท์ ์ผ๋ก observable ๊ฐ์ฒด๋ฅผ JavaScript _๊ตฌ์กฐ_๋ก ๋ณํํฉ๋๋ค. observable ๋ฐฐ์ด, ๊ฐ์ฒด, Map ๊ทธ๋ฆฌ๊ณ  primitive๋ฅผ ์ง์ํฉ๋๋ค.
computed ๊ฐ ๋ฐ non-enumerable ์์ฑ์ ๊ฒฐ๊ณผ์ ํฌํจ๋์ง ์์ต๋๋ค.
๋ ๋ณต์กํ (์ญ)์ง๋ ฌํ ์๋๋ฆฌ์ค์ ๊ฒฝ์ฐ ํด๋์ค์ `toJSON` ๋ฉ์๋๋ฅผ ์ ๊ณตํ๊ฑฐ๋ [serializr](https://github.com/mobxjs/serializr)์ ๊ฐ์ ์ง๋ ฌํ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ์ฌ์ฉํ๋ ๊ฒ์ด ์ข์ต๋๋ค.

```javascript
const obj = mobx.observable({
    x: 1
})

const clone = mobx.toJS(obj)

console.log(mobx.isObservableObject(obj)) // true
console.log(mobx.isObservableObject(clone)) // false
```

---

## ํ๊ฒฝ์ค์ 

_MobX ์ธ์คํด์ค๋ฅผ ๋ฏธ์ธ ์กฐ์ ํฉ๋๋ค._

### `configure`

[**์ฌ์ฉ ๋ฐฉ๋ฒ**](configuration.md): ํ์ฑ MobX ์ธ์คํด์ค์ ๋ํ ์ ์ญ ๋์ ์ค์ ์ ์ง์ ํฉ๋๋ค.
MobX์ ์ ์ฒด์ ์ธ ๋์ ๋ฐฉ์ ๋ณ๊ฒฝ์ ์ฌ์ฉํฉ๋๋ค.

---

## Collection ์ ํธ๋ฆฌํฐ {๐}

_๋์ผํ ์ผ๋ฐ API๋ฅผ ์ฌ์ฉํ์ฌ observable ๋ฐฐ์ด, ๊ฐ์ฒด ๊ทธ๋ฆฌ๊ณ  Map์ ์กฐ์ํ  ์ ์์ต๋๋ค. ์ด ๊ธฐ๋ฅ์ ์ผ๋ฐ์ ์ผ๋ก ํ์ํ์ง๋ ์์ง๋ง, [Proxy๊ฐ ์ง์๋์ง ์๋ ํ๊ฒฝ](configuration.md#limitations-without-proxy-support)์์๋ ์ ์ฉํ  ์ ์์ต๋๋ค._

### `values`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](collection-utilities.md): `values(array|object|Set|Map)`

์ปฌ๋ ์์ ๋ชจ๋  ๊ฐ์ ๋ฐฐ์ด๋ก ๋ฐํํฉ๋๋ค.

### `keys`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](collection-utilities.md): `keys(array|object|Set|Map)`

์ปฌ๋ ์์ ๋ชจ๋  key์ index๋ฅผ ๋ฐฐ์ด๋ก ๋ฐํํฉ๋๋ค.

### `entries`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](collection-utilities.md): `entries(array|object|Set|Map)`

์ปฌ๋ ์์ ์๋ ๋ชจ๋  ํญ๋ชฉ์ `[key, value]` ์์ ๋ฐฐ์ด๋ก ๋ฐํํฉ๋๋ค.

### `set`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](collection-utilities.md): `set(array|object|Map, key, value)`

์ปฌ๋ ์์ ์๋ฐ์ดํธํฉ๋๋ค.

### `remove`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](collection-utilities.md): `remove(array|object|Map, key)`

์ปฌ๋ ์์์ ํญ๋ชฉ์ ์ ๊ฑฐํฉ๋๋ค.

### `has`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](collection-utilities.md): `has(array|object|Map, key)`

์ปฌ๋ ์์ ํด๋น ๋ฉค๋ฒ๊ฐ ์๋์ง ์ฒดํฌํฉ๋๋ค.

### `get`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](collection-utilities.md): `get(array|object|Map, key)`

ํค๋ฅผ ์ฌ์ฉํ์ฌ ์ปฌ๋ ์์์ ๊ฐ์ ๊ฐ์ ธ์ต๋๋ค.

---

## Introspection ์ ํธ๋ฆฌํฐ {๐}

_MobX์ ๋ด๋ถ state๋ฅผ ๊ฒ์ฌํ๊ฑฐ๋ MobX ์์ ๋ฉ์ง ๋๊ตฌ๋ฅผ ๊ตฌ์ถํ๋ ค๋ ๊ฒฝ์ฐ์ ์ ์ฉํ๊ฒ ์ฌ์ฉํ  ์ ์๋ ์ ํธ๋ฆฌํฐ์๋๋ค._

### `isObservable`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isObservable(array|object|Set|Map)`

MobX์ ์ํด ๋ง๋ค์ด์ง ๊ฐ์ฒด ๋๋ ์ปฌ๋ ์์ธ์ง ํ์ธํฉ๋๋ค.

### `isObservableProp`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isObservableProp(object, propertyName)`

ํด๋น ์์ฑ์ด observable์ธ์ง ํ์ธํฉ๋๋ค.

### `isObservableArray`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isObservableArray(array)`

๊ฐ์ด observable ๋ฐฐ์ด์ธ์ง ํ์ธํฉ๋๋ค.

### `isObservableObject`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isObservableObject(object)`

๊ฐ์ด observable ๊ฐ์ฒด์ธ์ง ํ์ธํฉ๋๋ค.

### `isObservableSet`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isObservableSet(set)`

๊ฐ์ด observable Set์ธ์ง ํ์ธํฉ๋๋ค.

### `isObservableMap`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isObservableMap(map)`

๊ฐ์ด observable Map์ธ์ง ํ์ธํฉ๋๋ค.

### `isBoxedObservable`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isBoxedObservable(value)`

๊ฐ์ด `observable.box`๋ฅผ ์ฌ์ฉํ์ฌ ๋ง๋  observable.box์ธ์ง ํ์ธํฉ๋๋ค.

### `isAction`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isAction(func)`

ํจ์๊ฐ `action`์ผ๋ก ํ์๋์ด ์๋์ง ํ์ธํฉ๋๋ค.

### `isComputed`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isComputed(boxedComputed)`

`computed(() => expr)`์ ์ฌ์ฉํ์ฌ ๋ง๋  box computed ๊ฐ์ธ์ง ํ์ธํฉ๋๋ค.

### `isComputedProp`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `isComputedProp(object, propertyName)`

computed ์์ฑ์ธ์ง ํ์ธํฉ๋๋ค.

### `trace`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](analyzing-reactivity.md): `trace()`, `trace(true)` _(๋๋ฒ๊ฑฐ ์์ฅ)_ ๋๋ `trace(object, propertyName, enterDebugger?)`

observer, reaction ๋๋ computed ๊ฐ ๋ด๋ถ์์ ์ฌ์ฉํด์ผ ํฉ๋๋ค. ๊ฐ์ด ๋ฌดํจ๊ฐ ๋์์ ๋ ๋ก๊ทธ๋ฅผ ๋จ๊ธฐ๊ฑฐ๋, _true_๋ก ํธ์ถ๋ ๊ฒฝ์ฐ ๋๋ฒ๊ฑฐ ์ค๋จ์ ์ ์ค์ ํฉ๋๋ค.

### `spy`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](analyzing-reactivity.md#spy): `spy(eventListener)`

MobX์์ ๋ฐ์ํ๋ ๋ชจ๋  ์ด๋ฒคํธ๋ฅผ ์์ ํ๋ ์ ์ญ ์คํ์ด ๋ฆฌ์ค๋๋ฅผ ๋ฑ๋กํฉ๋๋ค.

### `getDebugName`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](analyzing-reactivity.md#getdebugname): `getDebugName(reaction|array|Set|Map)` ๋๋ `getDebugName(object|Map, propertyName)`

observable ๋๋ reaction์ ๋ํด (์์ฑ๋) ์น์ํ ๋๋ฒ๊ทธ ์ด๋ฆ์ ๋ฐํํฉ๋๋ค.

### `getDependencyTree`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](analyzing-reactivity.md#getdependencytree): `getDependencyTree(object, computedPropertyName)`

์ฃผ์ด์ง reactionยทcomputation์ด ํ์ฌ ์์กดํ๊ณ  ์๋ ๋ชจ๋  observable ํธ๋ฆฌ ๊ตฌ์กฐ๋ฅผ ๋ฐํํฉ๋๋ค.

### `getObserverTree`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](analyzing-reactivity.md#getobservertree): `getObserverTree(array|Set|Map)` ๋๋ `getObserverTree(object|Map, propertyName)`

์ฃผ์ด์ง observable์ ๊ด์ฐฐํ๋ ๋ชจ๋  reactionยทcomputation์ ํฌํจํ๋ ํธ๋ฆฌ ๊ตฌ์กฐ๋ฅผ ๋ฐํํฉ๋๋ค.

---

## MobX ํ์ฅ {๐}

_๋๋ฌธ ๊ฒฝ์ฐ์ง๋ง MobX ์์ฒด๋ฅผ ํ์ฅํ๋ ค๊ณ  ํ  ๋ ์ฌ์ฉํ  ์ ์์ต๋๋ค._

### `createAtom`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](custom-observables.md): `createAtom(name, onBecomeObserved?, onBecomeUnobserved?)`

์์ฒด observable ๋ฐ์ดํฐ ๊ตฌ์กฐ๋ฅผ ๋ง๋ค์ด MobX์ ์ฐ๊ฒฐํฉ๋๋ค. ๋ชจ๋  observable ๋ฐ์ดํฐ ํ์์์ ๋ด๋ถ์ ์ผ๋ก ์ฌ์ฉ๋ฉ๋๋ค. Atom์ MobX์ ์๋ฆฌ๋ ๋ ๊ฐ์ง _report_ ๋ฉ์๋๋ฅผ ์ ๊ณตํฉ๋๋ค.

-   `reportObserved()`: atom์ด ๊ด์ฐฐ๋์๊ณ , ํ์ฌ derivation์ ์ข์์ฑ ํธ๋ฆฌ์ ์ผ๋ถ๋ก ๊ฐ์ฃผํ์ฌ์ผ ํฉ๋๋ค.
-   `reportChanged()`: atom์ด ๋ณ๊ฒฝ๋์์ผ๋ฉฐ, atom์ ์ํ ๋ชจ๋  derivation์ ๋ฌดํจ๊ฐ ๋์ด์ผ ํฉ๋๋ค.

### `getAtom`

{๐} [**์ฌ์ฉ ๋ฐฉ๋ฒ**](analyzing-reactivity.md#getatom): `getAtom(thing, property?)`

backing atom์ ๋ฐํํฉ๋๋ค.

### `transaction`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `transaction(worker: () => any)`

_ํธ๋์ญ์์ ๋ก์ฐ ๋ ๋ฒจ API์๋๋ค. ํธ๋์ญ์ ๋์ ์ [`action`](#action) ๋๋ [`runInAction`](#runinaction)์ ์ฌ์ฉํ๋ ๊ฒ์ ์ถ์ฒํฉ๋๋ค._

ํธ๋์ญ์์ด ๋๋  ๋๊น์ง observer์๊ฒ ์๋ฆฌ์ง ์๊ณ  ์๋ฐ์ดํธ ์ผ๊ด ์ฒ๋ฆฌ๋ฅผ ํ๋ ๋ฐ ์ฌ์ฉ๋ฉ๋๋ค. [`untracked`](#untracked)์ ๋ง์ฐฌ๊ฐ์ง๋ก `action`์ ์ํด ์๋์ผ๋ก ์ ์ฉ๋๋ฏ๋ก, `transaction`์ ์ง์  ์ฌ์ฉํ๋ ๊ฒ๋ณด๋ค action์ ์ฌ์ฉํ๋ ๊ฒ์ด ๋ ์ข์ต๋๋ค.

๋งค๊ฐ๋ณ์๊ฐ ์๋ ๋จ์ผ `worker` ํจ์๋ฅผ ์ธ์๋ก ์ฌ์ฉํ๊ณ  ์ด ํจ์์ ์ํด ๋ฐํ๋ ๊ฐ์ ๋ฐํํฉ๋๋ค.
`transaction`์ ์์ ํ ๋๊ธฐ์ ์ผ๋ก ์คํ๋๋ฉฐ ์ค์ฒฉ๋  ์ ์์ต๋๋ค. ๊ฐ์ฅ ๋ฐ๊นฅ์ชฝ `transaction`์ด ์๋ฃ๋ ํ์ ๋ณด๋ฅ ์ค์ธ reaction์ด ์คํ๋ฉ๋๋ค.

```javascript
import { observable, transaction, autorun } from "mobx"

const numbers = observable([])

autorun(() => console.log(numbers.length, "numbers!"))
// ์ถ๋ ฅ: '0 numbers!'

transaction(() => {
    transaction(() => {
        numbers.push(1)
        numbers.push(2)
    })
    numbers.push(3)
})
// ์ถ๋ ฅ: '3 numbers!'
```

### `untracked`

{๐} ์ฌ์ฉ ๋ฐฉ๋ฒ: `untracked(worker: () => any)`

_Untracked๋ ๋ก์ฐ ๋ ๋ฒจ API์๋๋ค. untracked ๋์ ์ [`reaction`](#reaction), [`action`](#action) ๋๋ [`runInAction`](#runinaction)์ ์ฌ์ฉํ๋ ๊ฒ์ ์ถ์ฒํฉ๋๋ค._

observer๋ฅผ ์ค์ ํ์ง ์๊ณ  ์ฝ๋๋ฅผ ์คํํฉ๋๋ค. `transaction`๊ณผ ๋ง์ฐฌ๊ฐ์ง๋ก `untracked`๋ action์ ์ํด ์๋์ผ๋ก ์ ์ฉ๋๋ฏ๋ก, ์ผ๋ฐ์ ์ผ๋ก `untracked`๋ฅผ ์ง์  ์ฌ์ฉํ๋ ๊ฒ๋ณด๋ค๋ `action`์ ์ฌ์ฉํ๋ ๊ฒ์ด ๋ ์ ํฉํฉ๋๋ค.

```javascript
const person = observable({
    firstName: "Michel",
    lastName: "Weststrate"
})

autorun(() => {
    console.log(
        person.lastName,
        ",",
        // untracked ๋ธ๋ก์ ์ข์์ฑ์ ์ค์ ํ์ง ์๊ณ 
        // person์ firstName์ ๋ฐํํฉ๋๋ค.
        untracked(() => person.firstName)
    )
})
// ์ถ๋ ฅ: 'Weststrate, Michel'

person.firstName = "G.K."
// ์ธ๋ ฅ๋์ง ์์ต๋๋ค.

person.lastName = "Chesterton"
// ์ถ๋ ฅ: 'Chesterton, G.K.'
```
