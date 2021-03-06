---
title: Intercept & Observe
sidebar_label: Intercept & Observe {π}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# Intercept & Observe {π}

_β οΈ **κ²½κ³ **: interceptμ observeλ λ‘μ° λ λ²¨μ μ νΈλ¦¬ν°μ΄λ―λ‘ μ€μ λ‘λ νμνμ§ μμ΅λλ€. `observe`λ νΈλμ­μμ μ‘΄μ€νμ§ μκ³  λ³κ²½μ¬ν­μ λν κΉμ κ΄μ°°(observing)μ μ§μνμ§ μμΌλ―λ‘ `observe` λμ  [reaction](reactions.md) λ°©μμ μ¬μ©νμΈμ. μ΄λ¬ν μ νΈλ¦¬ν°λ₯Ό μ¬μ©νλ κ²μ μν° ν¨ν΄μλλ€. μ΄μ  κ°κ³Ό μ κ°μ μ‘μΈμ€νλ €λ©΄ `observe` λμ  [`reaction`](reactions.md#reaction)μ μ¬μ©νμΈμ.β οΈ_

`observe`μ `intercept`λ λ¨μΌ observableμ λ³κ²½μ λͺ¨λν°λ§νλ λ° μ¬μ©ν  μ μμ§λ§ μ€μ²©λ observableμ μΆμ νμ§λ **μμ΅λλ€**.

-   `intercept`λ mutationμ observable(κ²μ¦, μ κ·ν λλ μ·¨μ)μ μ μ©νκΈ° μ μ κ°μ§νκ³  μμ νλ λ° μ¬μ©ν  μ μμ΅λλ€.
-   `observe`λ λ³κ²½μ¬ν­μ΄ λ§λ€μ΄μ§ ν μ΄λ₯Ό intercept ν  μ μλλ‘ ν΄μ€λλ€.

## Intercept

μ¬μ© λ°©λ²: `intercept(target, propertyName?, interceptor)`

_μ΄ APIλ κ°λ₯ν μ¬μ©νμ§ λ§μΈμ. κΈ°λ³Έμ μΌλ‘ μ½κ°μ κ΄μ  μ§ν₯ νλ‘κ·Έλλ°(aspect-oriented programming)μ μ κ³΅νμ¬ λλ²κΉνκΈ° μ΄λ €μ΄ νλ¦μ λ§λ­λλ€. λμ  stateλ₯Ό μλ°μ΄νΈνλ λμμ΄ μλ μλ°μ΄νΈνκΈ° **μ μ** λ°μ΄ν° μ ν¨μ± κ²μ¬μ κ°μ μμμ μνν©λλ€._

-   `target`: κ°μνλ observable.
-   `propertyName`: intercept ν  νΉμ  μμ±μ μ§μ νλ μ νμ  λ§€κ° λ³μμλλ€. `intercept(user.name, interceptor)`λ `intercept(user, "name", interceptor)`μ κ·Όλ³Έμ μΌλ‘ λ€λ¦λλ€. μ μλ `user.name` λ΄λΆμ _νμ¬_ `value`μ interceptorλ₯Ό μΆκ°νλ €κ³  μλνλ©°, ν΄λΉ κ°μ μ λ observableμ΄ μλλλ€. νμλ `user`μ `name` _μμ±_μ λν λ³κ²½ μ¬ν­μ intercept ν©λλ€.
-   `interceptor`: observableμ λ°μνλ _κ°κ°μ_ λ³κ²½ μ¬ν­μ λν΄ νΈμΆλλ μ½λ°±μλλ€. mutationμ μ€λͺνλ λ¨μΌ λ³κ²½ κ°μ²΄λ₯Ό λ°μ΅λλ€.

`intercept`λ MobXμκ² νμ¬ λ³κ²½μ¬ν­κ³Ό κ΄λ ¨νμ¬ νμν μ¬ν­μ μλ €μΌ ν©λλ€. 
λ°λΌμ λ€μ μ€ νλλ₯Ό μνν΄μΌ ν©λλ€.

1. ν¨μμμ λ°μ `change` κ°μ²΄λ₯Ό κ·Έλλ‘ λ°νν©λλ€. μ΄ κ²½μ° mutationμ΄ μ μ©λ©λλ€.
2. `change` κ°μ²΄λ₯Ό μμ νκ³  λ°νν©λλ€(μ: λ°μ΄ν° μ κ·ν). μΌλΆ νλλ μμ ν  μ μμ΅λλ€. μλλ₯Ό μ°Έκ³ νμΈμ.
3. `null`μ λ°νν©λλ€. μ΄λ λ³κ²½μ¬ν­μ΄ λ¬΄μλ  μ μμΌλ©° ν΄λΉ λ³κ²½ μ¬ν­μ μ μ©ν΄μλ μ λλ€λ κ²μ λνλλλ€. μ΄κ²μ μλ₯Ό λ€λ©΄ κ°μ²΄λ₯Ό μΌμμ μΌλ‘ λΆλ³(immutable) νκ² λ§λλ κ²κ³Ό κ°μ΄ κ°λ ₯ν κ°λμλλ€.
4. μμΈλ₯Ό throw ν©λλ€. μλ₯Ό λ€μ΄ μΌλΆ λ¬΄κ³΅λ³μ±(invariant)μ΄ μΆ©μ‘±λμ§ μλ κ²½μ°μ ν΄λΉν©λλ€.

μ΄ ν¨μλ νΈμΆλ  λ interceptorλ₯Ό μ·¨μνλ λ° μ¬μ©ν  μ μλ `disposer` ν¨μλ₯Ό λ°νν©λλ€. μ¬λ¬ κ°μ interceptorλ₯Ό λμΌν observableμ λ±λ‘ν  μ μμ΅λλ€. ν΄λΉ interceptorλ€μ λ±λ‘ μμλλ‘ μ°κ²°λ©λλ€. interceptor μ€ νλκ° `null`μ λ°ννκ±°λ μμΈλ₯Ό throw νλ©΄ λ€λ₯Έ interceptorλ λ μ΄μ νκ°λμ§ μμ΅λλ€. μμ κ°μ²΄μ κ°λ³ μμ± λͺ¨λμ interceptorλ₯Ό λ±λ‘ν  μλ μμ΅λλ€. μ΄ κ²½μ° μμ κ°μ²΄ interceptorκ° μμ± interceptorλ³΄λ€ λ¨Όμ  μ€νλ©λλ€.

```javascript
const theme = observable({
    backgroundColor: "#ffffff"
})

const disposer = intercept(theme, "backgroundColor", change => {
    if (!change.newValue) {
        // background color μ€μ μ ν΄μ νλ €λ μλλ₯Ό λ¬΄μν©λλ€.
        return null
    }
    if (change.newValue.length === 6) {
        // λλ½λ '#'λ₯Ό λΆμλλ€.
        change.newValue = "#" + change.newValue
        return change
    }
    if (change.newValue.length === 7) {
        // μ¬λ°λ₯Έ νμμ μμ μ½λμ¬μΌ ν©λλ€!
        return change
    }
    if (change.newValue.length > 10) {
        // μ΄νμ λ³κ²½μ¬ν­μ λν interceptλ₯Ό μ€λ¨ν©λλ€.
        disposer()
    }
    throw new Error("This doesn't look like a color at all: " + change.newValue)
})
```

## Observe

μ¬μ© λ°©λ²: `observe(target, propertyName?, listener, invokeImmediately?)`

_μλ¨μ κ²½κ³ λ₯Ό μ°Έκ³ νμκΈ° λ°λλλ€. μ΄ API λμ  [`reaction`](reactions.md#reaction)μ μ¬μ©νμΈμ._

-   `target`: κ΄μ°°νλ €λ observable.
-   `propertyName`: κ΄μ°°ν  νΉμ  μμ±μ μ§μ νλ μ νμ  λ§€κ° λ³μμλλ€. `observe(user.name, listener)`λ `observe(user, "name", listener)`μ κ·Όλ³Έμ μΌλ‘ λ€λ¦λλ€. μ μλ `user.name` λ΄μ _νμ¬_ `value`μ κ΄μ°°νλ©° ν΄λΉ κ°μ μ λ  observableμ΄ μλλλ€. νμλ `user`μ `name` _μμ±_μ κ΄μ°°ν©λλ€.
-   `listener`: observableμ λ°μνλ _κ°κ°μ_ λ³κ²½ μ¬ν­μ λν΄ νΈμΆλλ μ½λ°±μλλ€. mutationμ μ€λͺνλ λ¨μΌ λ³κ²½ κ°μ²΄λ₯Ό λ°μΌλ©°, boxed observableμ μ μΈν©λλ€. boxed observableμ `newValue, oldValue` λ κ°μ§ λ§€κ° λ³μλ₯Ό λ°λ `listener`λ₯Ό νΈμΆν©λλ€.
-   `invokeImmediately`: κΈ°λ³Έμ μΌλ‘ _false_μλλ€. `observe`μ΄ μ²« λ²μ§Έ λ³κ²½μ¬ν­μ κΈ°λ€λ¦¬λ λμ  observableμ stateλ‘ μ§μ  `listener`λ₯Ό νΈμΆνλλ‘ νλ €λ©΄ μ΄ κ°μ _true_λ‘ μ€μ ν©λλ€. μΌλΆ νμμ observableμμλ μμ§ μ§μλμ§ μμ΅λλ€.

μ΄ ν¨μλ observerλ₯Ό μ·¨μνλ λ° μ¬μ©ν  μ μλ `disposer` ν¨μλ₯Ό λ°νν©λλ€. `νΈλμ­μ`μ `observe` λ©μλμ μλμ μν₯μ μ£Όμ§ μμ΅λλ€. 
μ΄λ νΈλμ­μ μμμλ `observe`κ° κ° mutationμ λν listenerλ₯Ό μ€ννλ€λ κ²μ μλ―Έν©λλ€. λ°λΌμ [`autorun`](reactions.md#autorun)μ΄ μΌλ°μ μΌλ‘ `observe`λ³΄λ€ λ κ°λ ₯νκ³  μ μΈμ μΈ λμμλλ€.

_`observe`λ **mutation**μ΄ λ§λ€μ΄μ§ λ λ°μνλ λ°λ©΄ `autorun`μ΄λ `reaction`κ³Ό κ°μ reactionμ **μλ‘μ΄ κ°**μ΄ μ¬μ© κ°λ₯ν΄μ§ λ ν΄λΉ κ°μ λ°μν©λλ€. λλΆλΆμ κ²½μ° νμλ§μΌλ‘ μΆ©λΆν©λλ€._

μμ:

```javascript
import { observable, observe } from "mobx"

const person = observable({
    firstName: "Maarten",
    lastName: "Luther"
})

// λͺ¨λ  νλλ₯Ό κ΄μ°°ν©λλ€.
const disposer = observe(person, change => {
    console.log(change.type, change.name, "from", change.oldValue, "to", change.object[change.name])
})

person.firstName = "Martin"
// μΆλ ₯ κ°: 'update firstName from Maarten to Martin'

// μ΄νμ λ°μνλ λͺ¨λ  μλ°μ΄νΈλ₯Ό λ¬΄μν©λλ€.
disposer()

// λ¨μΌ νλλ₯Ό κ΄μ°°ν©λλ€.
const disposer2 = observe(person, "lastName", change => {
    console.log("LastName changed to ", change.newValue)
})
```

κ΄λ ¨ ν¬μ€νΈ: [Object.observe is dead. Long live mobx.observe](https://medium.com/@mweststrate/object-observe-is-dead-long-live-mobservable-observe-ad96930140c5)

## μ΄λ²€νΈ μ€λ²λ·°

`intercept`μ `observe`μ μ½λ°±μ μ μ΄λ λ€μ μμ±μ κ°μ§ μ΄λ²€νΈ κ°μ²΄λ₯Ό λ°μ΅λλ€.

-   `object`: μ΄λ²€νΈλ₯Ό νΈλ¦¬κ±°νλ observable
-   `debugObjectName`: (λλ²κΉμ μν΄) μ΄λ²€νΈλ₯Ό νΈλ¦¬κ±°νλ observableμ μ΄λ¦
-   `observableKind`: observable νμ(value, set, array, object, map, computed)
-   `type` (λ¬Έμμ΄): νμ¬ μ΄λ²€νΈμ νμ

νμλ³λ‘ μ¬μ©ν  μ μλ μΆκ° νλμλλ€.

| Observable νμ              | μ΄λ²€νΈ νμ | μμ±     | μ€λͺ                                                                                       | interceptνλ λμ μ¬μ©κ°λ₯ μ¬λΆ | interceptλ‘λΆν° μμ  κ°λ₯ μ¬λΆ |
| ---------------------------- | ---------- | ------------ | ------------------------------------------------------------------------------------------------- | -------------------------- | ---------------------------- |
| Object                       | add        | name         | μΆκ°ν  μμ±μ μ΄λ¦                                                                 | β                          |                              |
|                              |            | newValue     | ν λΉν  μλ‘μ΄ κ°                                                                     | β                          | β                            |
|                              | update\*   | name         | μλ°μ΄νΈ ν  μμ±μ μ΄λ¦                                                               | β                          |                              |
|                              |            | newValue     | ν λΉν  μλ‘μ΄ κ°                                                                     | β                          | β                            |
|                              |            | oldValue     | λμ²΄λ κ°                                                                       |                            |                              |
| Array                        | splice     | index        | spliceμ μμ μΈλ±μ€. spliceλ `push`, `unshift`, `replace` λ±μ μν΄μλ μ€νλ©λλ€.        | β                          |                              |
|                              |            | removedCount | μ­μ ν  μμμ κ°―μ                                                                    | β                          | β                            |
|                              |            | added        | μΆκ°ν  μμκ° μλ λ°°μ΄                                                                     | β                          | β                            |
|                              |            | removed      | μ­μ λ μμκ° μλ λ°°μ΄                                                               |                            |                              |
|                              |            | addedCount   | μΆκ°λ μμμ κ°―μ                                                                  |                            |                              |
|                              | update     | index        | μλ°μ΄νΈν  λ¨μΌ μνΈλ¦¬μ μΈλ±μ€                                                          | β                          |                              |
|                              |            | newValue     | ν λΉλμκ±°λ ν λΉν  μλ‘μ΄ κ°                                                          | β                          | β                            |
|                              |            | oldValue     | λμ²΄λ μ΄μ  κ°                                                                  |                            |                              |
| Map                          | add        | name         | μΆκ°λ μνΈλ¦¬μ μ΄λ¦                                                             | β                          |                              |
|                              |            | newValue     | ν λΉν  μλ‘μ΄ κ°                                                             | β                          | β                            |
|                              | update     | name         | μλ°μ΄νΈν  μνΈλ¦¬μ μ΄λ¦                                                              | β                          |                              |
|                              |            | newValue     | ν λΉν  μλ‘μ΄ κ°                                                             | β                          | β                            |
|                              |            | oldValue     | λμ²΄λ κ°                                                                 |                            |                              |
|                              | delete     | name         | μ­μ λ μνΈλ¦¬μ μ΄λ¦                                                              | β                          |                              |
|                              |            | oldValue     | μ­μ λ μνΈλ¦¬μ κ°                                                          |                            |                              |
| Boxed & computed observables | create     | newValue     | μμ±λ  λ ν λΉλ κ°. boxed observableμμλ `spy` μ΄λ²€νΈλ‘μλ§ κ°λ₯ν©λλ€. |                            |                              |
|                              | update     | newValue     | ν λΉν  μλ‘μ΄ κ°                                                                     | β                          | β                            |
|                              |            | oldValue     | observableμ μ΄μ  κ°                                                             |                            |                              |

**μ°Έκ³ :** κ°μ²΄ `update` μ΄λ²€νΈλ μλ°μ΄νΈλ computed κ°μ λν΄ μ€νλμ§ μμ΅λλ€(ν΄λΉ computed κ°μ mutationμ΄ μλλ―λ‘). κ·Έλ¬λ `observe(object, 'computedPropertyName', listener)`λ₯Ό μ¬μ©νμ¬ νΉμ  μμ±μ λͺμμ μΌλ‘ κ΅¬λνλ©΄ μ΄λ¬ν computed κ°μ κ΄μ°°ν  μ μμ΅λλ€.
