---
title: Lazy observables λ§λ€κΈ°
sidebar_label: Lazy observables {π}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# Lazy observables λ§λ€κΈ° {π}

μ¬μ©λ²:

-   `onBecomeObserved(observable, property?, listener: () => void): (() => void)`
-   `onBecomeUnobserved(observable, property?, listener: () => void): (() => void)`

`onBecomeObserved`μ `onBecomeUnobserved` ν¨μλ κΈ°μ‘΄μ κ΄μ°° λμλ€(observables)μ lazyν λμμ΄λ λΆμμ©(side effects)μ 
μΆκ°νλ λ° 
μ¬μ©ν  μ 
μμ΅λλ€. 
μ΄ ν¨μλ€μ MobXμ κ΄μ°° μμ€νμ μ°κ²°λμ΄ μκ³  κ΄μ°° λμλ€(observables)μ΄ κ΄μ°°λκΈ° _μμ_ νκ±°λ _μ€λ¨_ λ  λ μλ¦Όμ λ°μ΅λλ€.
λ ν¨μλ _λ¦¬μ€λ(listener)_λ₯Ό λΆλ¦¬νλ _μ²λ¦¬(disposer)_ ν¨μλ₯Ό λ¦¬ν΄ν©λλ€. 


μλμ μμμμλ κ΄μ°°λ κ°(observed value)μ΄ μ€μ λ‘ μ¬μ© μ€μΌ λλ§ λ€νΈμν¬ fetchλ₯Ό μννλ λ° λ ν¨μλ₯Ό μ¬μ©ν©λλ€.

```javascript
export class City {
    location
    temperature
    interval

    constructor(location) {
        makeAutoObservable(this, {
            resume: false,
            suspend: false
        })
        this.location = location
        // temperatureκ° μ€μ λ‘ μ¬μ© λλ κ²½μ°μλ§ λ°μ΄ν° fetchingμ μμν©λλ€!
        onBecomeObserved(this, "temperature", this.resume)
        onBecomeUnobserved(this, "temperature", this.suspend)
    }

    resume = () => {
        log(`Resuming ${this.location}`)
        this.interval = setInterval(() => this.fetchTemperature(), 5000)
    }

    suspend = () => {
        log(`Suspending ${this.location}`)
        this.temperature = undefined
        clearInterval(this.interval)
    }

    fetchTemperature = flow(function* () {
        // λ°μ΄ν° fetching λ‘μ§...
    })
}
```
