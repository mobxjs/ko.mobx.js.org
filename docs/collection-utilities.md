---
title: Collection utilities
sidebar_label: Collection utilities {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# Collection utilities {🚀}

동일한 일반 API를 사용하여 observable arrays, objects, Maps를 조작할 수 있습니다.
이러한 API는 완전히 반응적입니다. 즉, [Proxy 지원 없이도](configuration.md#limitations-without-proxy-support) `set`을 사용하여 새로운 속성을 추가하고 `values`나 `keys`를 사용하여 observable arrays, objects, Maps를 반복하면 MobX에 의해 감지될 수 있습니다.

`values`, `keys`, `entries`의 또 다른 장점은 이터레이터(iterator) 대신 배열을 반환하여 결과에 대해 즉시 `.map(fn)`을 호출할 수 있습니다.

이런 장점들이 있지만, 일반적인 프로젝트에서는 이러한 API를 사용할 이유가 거의 없습니다.

Access:

-   `values(collection)` 컬렉션에 있는 모든 값의 배열을 반환합니다.
-   `keys(collection)` 컬렉션에 있는 모든 키의 배열을 반환합니다.
-   `entries(collection)` 컬렉션에 있는 모든 항목 `[key, value]` 쌍의 배열을 반환합니다.

Mutation:

-   `set(collection, key, value)` or `set(collection, { key: value })` 제공된 키·값 쌍으로 지정된 컬렉션을 업데이트합니다.
-   `remove(collection, key)` 컬렉션에서 지정된 자식을 제거합니다. Splicing은 배열에 사용됩니다.
-   `has(collection, key)` 컬렉션에 지정된 _observable_ 속성이 있으면 _true_를 반환합니다.
-   `get(collection, key)` 지정된 키 아래의 자식을 반환합니다.

`Proxy`가 지원되지 않는 환경에서 액세스 API를 사용하는 경우 변경 사항을 감지할 수 있도록 변형(Mutation) API도 사용해야 합니다.

```javascript
import { get, set, observable, values } from "mobx"

const twitterUrls = observable.object({
    Joe: "twitter.com/joey"
})

autorun(() => {
    // Get은 아직 존재하지 않는 속성을 추적할 수 있습니다.
    console.log(get(twitterUrls, "Sara"))
})

autorun(() => {
    console.log("All urls: " + values(twitterUrls).join(", "))
})

set(twitterUrls, { Sara: "twitter.com/horsejs" })
```
