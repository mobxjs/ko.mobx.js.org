---
title: custom observables 만들기
sidebar_label: Custom observables {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# custom observables 만들기 {🚀}

## 원자(Atoms)

특정 시점에서 반응 계산에 사용할 수 있는 더 많은 데이터 구조 또는 기타 항목(streams)을 원할 때는 원자(atoms)의 개념을 사용하여 간단하게 해결할 수 있습니다.
원자는 observable 데이터 소스가 관찰 또는 변경되었음을 MobX에 알리는 데 사용할 수 있으며 MobX는 원자가 사용되거나 더 이상 사용되지 않을 때마다 원자에 신호를 보냅니다.

_**Tip**: 대부분의 경우에서, 일반 observable을 생성하고 [`onBecomeObserved`](lazy-observables.md) 유틸리티를 사용하여 MobX가 추적을 시작할 때 알림을 받으면 자신만의 원자를 생성할 필요가 없습니다.

다음 예제는 현재 날짜와 시간을 반환하는 observable `Clock`을 만드는 방법을 보여줍니다. Clock은 반응형 함수에서 사용할 수 있으며, 누군가가 관찰하고 있는 경우에만 실제로 똑딱거리게 됩니다.

`Atom` 클래스의 전체 API는 아래 예에서 확인할 수 있습니다.

```javascript
import { createAtom, autorun } from "mobx"

class Clock {
    atom
    intervalHandler = null
    currentDateTime

    constructor() {
        // MobX 핵심 알고리즘과 상호 작용할 원자를 만듭니다.
        this.atom = createAtom(
            // 첫번째 파라미터: 디버깅을 위한 Atom의 이름입니다.
            "Clock",
            // 두번째 파라미터(선택): 원자가 관찰되지 않은 상태에서 관찰된 상태로 전환될 때의 콜백입니다.
            () => this.startTicking(),
            // 세번째 파라미터(선택): 원자가 관찰된 원자에서 관찰되지 않은 원자로 전환될 때의 콜백입니다.
            () => this.stopTicking()
            // 이 두 상태 사이에서 동일한 원자가 여러 번 전환됩니다.
        )
    }

    getTime() {
        // observable 데이터 소스의 사용에 대해 MobX에 알립니다.
        //
        // reportObserved는 원자가 현재 일부 reaction에 의해 관찰되고 있는 경우 true를 반환합니다.
        // 필요한 경우 startTicking onBecomeObserved 이벤트 핸들러도 트리거 합니다.
        if (this.atom.reportObserved()) {
            return this.currentDateTime
        } else {
            // getTime이 호출되었지만 reaction이 실행되고 있지 않습니다.
            // 따라서, 아무도 이 값에 의존하지 않고 startTicking onBecomeObserved
            // 핸들러도 실행되지 않습니다.
            //
            // 원자의 특성에 따라 오류 발생, 기본값 반환 등과 같은 상황에서 다르게 동작할 수 있습니다.
            return new Date()
        }
    }

    tick() {
        this.currentDateTime = new Date()
        this.atom.reportChanged() // 해당 데이터 소스가 변경되었음을 MobX에 알립니다.
    }

    startTicking() {
        this.tick() // 초기의 tick
        this.intervalHandler = setInterval(() => this.tick(), 1000)
    }

    stopTicking() {
        clearInterval(this.intervalHandler)
        this.intervalHandler = null
    }
}

const clock = new Clock()

const disposer = autorun(() => console.log(clock.getTime()))
// 1초마다 시간을 출력합니다.

// 출력을 중지합니다. 다른 곳에서 같은 `clock`을 사용하는 부분이 없으면 Ticking도 멈춥니다.
disposer()
```
