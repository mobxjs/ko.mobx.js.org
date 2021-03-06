---
title: custom observables λ§λ€κΈ°
sidebar_label: Custom observables {π}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# custom observables λ§λ€κΈ° {π}

## μμ(Atoms)

νΉμ  μμ μμ λ°μ κ³μ°μ μ¬μ©ν  μ μλ λ λ§μ λ°μ΄ν° κ΅¬μ‘° λλ κΈ°ν ν­λͺ©(streams)μ μν  λλ μμ(atoms)μ κ°λμ μ¬μ©νμ¬ κ°λ¨νκ² ν΄κ²°ν  μ μμ΅λλ€.
μμλ observable λ°μ΄ν° μμ€κ° κ΄μ°° λλ λ³κ²½λμμμ MobXμ μλ¦¬λ λ° μ¬μ©ν  μ μμΌλ©° MobXλ μμκ° μ¬μ©λκ±°λ λ μ΄μ μ¬μ©λμ§ μμ λλ§λ€ μμμ μ νΈλ₯Ό λ³΄λλλ€.

_**Tip**: λλΆλΆμ κ²½μ°μμ, μΌλ° observableμ μμ±νκ³  [`onBecomeObserved`](lazy-observables.md) μ νΈλ¦¬ν°λ₯Ό μ¬μ©νμ¬ MobXκ° μΆμ μ μμν  λ μλ¦Όμ λ°μΌλ©΄ μμ λ§μ μμλ₯Ό μμ±ν  νμκ° μμ΅λλ€.

λ€μ μμ λ νμ¬ λ μ§μ μκ°μ λ°ννλ observable `Clock`μ λ§λλ λ°©λ²μ λ³΄μ¬μ€λλ€. Clockμ λ°μν ν¨μμμ μ¬μ©ν  μ μμΌλ©°, λκ΅°κ°κ° κ΄μ°°νκ³  μλ κ²½μ°μλ§ μ€μ λ‘ λλ±κ±°λ¦¬κ² λ©λλ€.

`Atom` ν΄λμ€μ μ μ²΄ APIλ μλ μμμ νμΈν  μ μμ΅λλ€.

```javascript
import { createAtom, autorun } from "mobx"

class Clock {
    atom
    intervalHandler = null
    currentDateTime

    constructor() {
        // MobX ν΅μ¬ μκ³ λ¦¬μ¦κ³Ό μνΈ μμ©ν  μμλ₯Ό λ§λ­λλ€.
        this.atom = createAtom(
            // μ²«λ²μ§Έ νλΌλ―Έν°: λλ²κΉμ μν Atomμ μ΄λ¦μλλ€.
            "Clock",
            // λλ²μ§Έ νλΌλ―Έν°(μ ν): μμκ° κ΄μ°°λμ§ μμ μνμμ κ΄μ°°λ μνλ‘ μ νλ  λμ μ½λ°±μλλ€.
            () => this.startTicking(),
            // μΈλ²μ§Έ νλΌλ―Έν°(μ ν): μμκ° κ΄μ°°λ μμμμ κ΄μ°°λμ§ μμ μμλ‘ μ νλ  λμ μ½λ°±μλλ€.
            () => this.stopTicking()
            // μ΄ λ μν μ¬μ΄μμ λμΌν μμκ° μ¬λ¬ λ² μ νλ©λλ€.
        )
    }

    getTime() {
        // observable λ°μ΄ν° μμ€μ μ¬μ©μ λν΄ MobXμ μλ¦½λλ€.
        //
        // reportObservedλ μμκ° νμ¬ μΌλΆ reactionμ μν΄ κ΄μ°°λκ³  μλ κ²½μ° trueλ₯Ό λ°νν©λλ€.
        // νμν κ²½μ° startTicking onBecomeObserved μ΄λ²€νΈ νΈλ€λ¬λ νΈλ¦¬κ±° ν©λλ€.
        if (this.atom.reportObserved()) {
            return this.currentDateTime
        } else {
            // getTimeμ΄ νΈμΆλμμ§λ§ reactionμ΄ μ€νλκ³  μμ§ μμ΅λλ€.
            // λ°λΌμ, μλ¬΄λ μ΄ κ°μ μμ‘΄νμ§ μκ³  startTicking onBecomeObserved
            // νΈλ€λ¬λ μ€νλμ§ μμ΅λλ€.
            //
            // μμμ νΉμ±μ λ°λΌ μ€λ₯ λ°μ, κΈ°λ³Έκ° λ°ν λ±κ³Ό κ°μ μν©μμ λ€λ₯΄κ² λμν  μ μμ΅λλ€.
            return new Date()
        }
    }

    tick() {
        this.currentDateTime = new Date()
        this.atom.reportChanged() // ν΄λΉ λ°μ΄ν° μμ€κ° λ³κ²½λμμμ MobXμ μλ¦½λλ€.
    }

    startTicking() {
        this.tick() // μ΄κΈ°μ tick
        this.intervalHandler = setInterval(() => this.tick(), 1000)
    }

    stopTicking() {
        clearInterval(this.intervalHandler)
        this.intervalHandler = null
    }
}

const clock = new Clock()

const disposer = autorun(() => console.log(clock.getTime()))
// 1μ΄λ§λ€ μκ°μ μΆλ ₯ν©λλ€.

// μΆλ ₯μ μ€μ§ν©λλ€. λ€λ₯Έ κ³³μμ κ°μ `clock`μ μ¬μ©νλ λΆλΆμ΄ μμΌλ©΄ Tickingλ λ©μΆ₯λλ€.
disposer()
```
