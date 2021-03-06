---
title: Collection utilities
sidebar_label: Collection utilities {π}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# Collection utilities {π}

λμΌν μΌλ° APIλ₯Ό μ¬μ©νμ¬ observable arrays, objects, Mapsλ₯Ό μ‘°μν  μ μμ΅λλ€.
μ΄λ¬ν APIλ μμ ν λ°μμ μλλ€. μ¦, [Proxy μ§μ μμ΄λ](configuration.md#limitations-without-proxy-support) `set`μ μ¬μ©νμ¬ μλ‘μ΄ μμ±μ μΆκ°νκ³  `values`λ `keys`λ₯Ό μ¬μ©νμ¬ observable arrays, objects, Mapsλ₯Ό λ°λ³΅νλ©΄ MobXμ μν΄ κ°μ§λ  μ μμ΅λλ€.

`values`, `keys`, `entries`μ λ λ€λ₯Έ μ₯μ μ μ΄ν°λ μ΄ν°(iterator) λμ  λ°°μ΄μ λ°ννμ¬ κ²°κ³Όμ λν΄ μ¦μ `.map(fn)`μ νΈμΆν  μ μλ€λ κ²μλλ€.

μ΄λ° μ₯μ λ€μ΄ μμ§λ§, μΌλ°μ μΈ νλ‘μ νΈμμλ μ΄λ¬ν APIλ₯Ό μ¬μ©ν  μ΄μ κ° κ±°μ μμ΅λλ€.

Access:

-   `values(collection)` μ»¬λ μμ μλ λͺ¨λ  κ°μ λ°°μ΄μ λ°νν©λλ€.
-   `keys(collection)` μ»¬λ μμ μλ λͺ¨λ  ν€μ λ°°μ΄μ λ°νν©λλ€.
-   `entries(collection)` μ»¬λ μμ μλ λͺ¨λ  ν­λͺ© `[key, value]` μμ λ°°μ΄μ λ°νν©λλ€.

Mutation:

-   `set(collection, key, value)` or `set(collection, { key: value })` μ κ³΅λ ν€Β·κ° μμΌλ‘ μ§μ λ μ»¬λ μμ μλ°μ΄νΈν©λλ€.
-   `remove(collection, key)` μ»¬λ μμμ μ§μ λ μμμ μ κ±°ν©λλ€. Splicingμ λ°°μ΄μ μ¬μ©λ©λλ€.
-   `has(collection, key)` μ»¬λ μμ μ§μ λ _observable_ μμ±μ΄ μμΌλ©΄ _true_λ₯Ό λ°νν©λλ€.
-   `get(collection, key)` μ§μ λ ν€ μλμ μμμ λ°νν©λλ€.

`Proxy`κ° μ§μλμ§ μλ νκ²½μμ μ‘μΈμ€ APIλ₯Ό μ¬μ©νλ κ²½μ° λ³κ²½ μ¬ν­μ κ°μ§ν  μ μλλ‘ λ³ν(Mutation) APIλ μ¬μ©ν΄μΌ ν©λλ€.

```javascript
import { get, set, observable, values } from "mobx"

const twitterUrls = observable.object({
    Joe: "twitter.com/joey"
})

autorun(() => {
    // Getμ μμ§ μ‘΄μ¬νμ§ μλ μμ±μ μΆμ ν  μ μμ΅λλ€.
    console.log(get(twitterUrls, "Sara"))
})

autorun(() => {
    console.log("All urls: " + values(twitterUrls).join(", "))
})

set(twitterUrls, { Sara: "twitter.com/horsejs" })
```
