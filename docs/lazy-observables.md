---
title: Lazy observables 만들기
sidebar_label: Lazy observables {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# Lazy observables 만들기 {🚀}

사용법:

-   `onBecomeObserved(observable, property?, listener: () => void): (() => void)`
-   `onBecomeUnobserved(observable, property?, listener: () => void): (() => void)`

`onBecomeObserved`와 `onBecomeUnobserved` 함수는 기존의 관찰 대상들(observables)에 lazy한 동작이나 부작용(side effects)을 
추가하는 데 
사용할 수 
있습니다. 
이 함수들은 MobX의 관찰 시스템에 연결되어 있고 관찰 대상들(observables)이 관찰되기 _시작_ 하거나 _중단_ 될 때 알림을 받습니다.
두 함수는 _리스너(listener)_를 분리하는 _처리(disposer)_ 함수를 리턴합니다. 


아래의 예시에서는 관찰된 값(observed value)이 실제로 사용 중일 때만 네트워크 fetch를 수행하는 데 두 함수를 사용합니다.

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
        // temperature가 실제로 사용 되는 경우에만 데이터 fetching을 시작합니다!
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
        // 데이터 fetching 로직...
    })
}
```
