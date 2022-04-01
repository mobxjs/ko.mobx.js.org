---
title: 환경설정
sidebar_label: 환경설정 {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 환경설정 {🚀}

MobX는 사용 방법, 대상으로 지정할 Javascript 엔진 및 Mobx 모범 사례에 대한 암시의 필요성에 따라 몇 가지 환경설정을 제공합니다.
대부분의 환경설정 옵션은 `configure` 메서드를 사용하여 설정할 수 있습니다.

## 프록시(Proxy) 지원

기본적으로 MobX는 프록시를 사용하여 배열과 plain 객체를 observable로 만듭니다. 프록시는 환경 전반에 걸쳐 최고의 성능과 일관된 동작을 제공합니다.
하지만 프록시를 지원하지 않는 환경을 대상으로 하는 경우엔 프록시 지원을 사용하지 않도록 설정해야 합니다.
특히 Hermes 엔진을 사용하지 않고 Internet Explorer 또는 React Native를 대상으로 하는 경우엔, 프록시 지원을 사용하지 않도록 설정해야 합니다.

`configure`을 사용하여 프록시 지원을 사용하지 않도록 설정할 수 있습니다.

```typescript
import { configure } from "mobx"

configure({
    useProxies: "never"
})
```

`useProxies` 환경설정에 설정할 수 있는 값은 다음과 같습니다.

-   `"always"` (**디폴트**): MobX는 [`프록시` 지원](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Proxy)이 되는 환경에서만 실행될 것이며 프록시 환경을 사용할 수 없는 경우 오류가 발생합니다.
-   `"never"`: 프록시는 사용되지 않으며 MobX는 프록시가 아닌 대안으로 대체됩니다. 이는 모든 ES5 환경과 호환되지만 다양한 [제한](#limitations-without-proxy-support)이 있습니다.
-   `"ifavailable"` (실험): 프록시를 사용할 수 있는 경우 프록시가 사용되고 그렇지 않은 경우 MobX는 프록시가 아닌 대안으로 대체됩니다. 해당 모드의 장점은 MobX가 ES5 환경에서 작동하지 않는 API 또는 언어 기능을 사용할 경우 경고를 시도하여 최신환경에서 실행되는 ES5 제한에 도달할 때 오류를 트리거한다는 것입니다.

**참고:** MobX 6 이전에는 구형 엔진의 경우 MobX 4를 선택하고, 신형 엔진의 경우 MobX 5를 선택해야 했습니다. 하지만 MobX 6는 이전 Javascript 엔진을 대상으로 할 때 Map과 같은 특정 API에 대해 폴리필이 필요하지만 둘 다 지원합니다.
프록시는 폴리필할 수 없습니다. 폴리필은 존재하더라도 전체 사양을 지원하지 않으며, MobX에 적합하지 않습니다. 폴리필을 사용하지 마세요.

### 프록시 지원이 없을 때 생기는 제한 사항

1.  observable 배열은 실제 배열이 아니므로 `Array.isArray()` 검사를 통과하지 않습니다. 실질적인 결과는 배열을 다른 라이브러리에 전달하기 전에 먼저 배열을 `.slice()`(실제 배열의 얕은 복사본을 얻기 위해) 해야 하는 경우가 많습니다. 예를 들어, observable 배열을 연결하는(concatenating) 것은 작동하지 않으므로 먼저 `.slice()`를 하세요.
2.  observable plain 객체 생성 후에 속성을 추가하거나 삭제하는 작업은 자동으로 감지되지 않습니다. 객체를 인덱스 기반 룩업 맵, 즉 동적 사물 컬렉션으로 사용하려면 observable map을 대신 사용하세요.

프록시가 활성화되지 않은 경우에도 객체에 속성을 동적으로 추가하고 추가 속성을 감지할 수 있습니다.
이러한 기능은 [컬렉션 유틸리티 {🚀}](collection-utilities.md)를 사용하여 수행할 수 있습니다. (새)속성이 `set` 유틸리티를 사용하여 설정되어야 하고, 객체가 내장 Javascript 메커니즘이 아닌 `values`·`keys` 또는 `entries` 유틸리티 중 하나를 사용하여 반복되어야 합니다.
하지만, 이러한 점은 정말 잊기 쉽기 때문에 observable map을 사용하는 것이 좋습니다.

## 데코레이터 지원

실험적인 데코레이터 기능을 활성화하려면 [데코레이터 활성화 {🚀}](enabling-decorators.md) 섹션을 확인하세요.

## Linting 옵션

MobX가 주장하는 패턴인 action, state, derivation 방식을 엄격하게 구분하여 채택할 수 있도록, MobX는 조사하여 런타임에 "_lint_"할 수 있습니다. MobX가 최대한 엄격해지도록 다음 설정을 적용하고 해당 설명을 계속 읽어 보세요.

```typescript
import { configure } from "mobx"

configure({
    enforceActions: "always",
    computedRequiresReaction: true,
    reactionRequiresObservable: true,
    observableRequiresReaction: true,
    disableErrorBoundaries: true
})
```

어느 순간엔가 이 정도의 엄격함이 꽤 성가실 수 있다는 것을 알게 될수도 있습니다.
여러분과 여러분의 동료들이 MobX의 멘탈 모델을 이해했다고 확신하면 생산성을 높이기 위해 이러한 규칙을 비활성화해도 좋습니다.

또한 이러한 규칙에 의해 트리거된 경고를 억제해야 하는 경우(예: `runInAction`으로 래핑)도 있습니다.
괜찮습니다. 이러한 권장 사항에는 좋은 예외가 있습니다.
트리거된 경고에 대해 근본주의적으로 굴지 않아도 됩니다.

#### `enforceActions`

_enforceActions_의 목표는 이벤트 핸들러를 [`action`](actions.md)으로 래핑하는 것을 잊지 않도록 하는 것입니다.

가능한 옵션:

-   `"observed"` (**디폴트**): 관찰되는 모든 state는 action을 통해 변경되어야 합니다. 해당 사항은 디폴트로 설정되어 있으며, 중요한 애플리케이션에서 권장되는 엄격 모드입니다.
-   `"never"`: state는 어디에서나 변경할 수 있습니다.
-   `"always"`: state의 변경과 생성은 항상 action을 통해 변경해야 합니다.

`"observed"`의 이점은 아직 아무 곳에서도 사용되지 않는 한 action 바깥에서 observable을 만들 수 있고, 자유롭게 수정 할 수 있다는 것입니다.

state는 원칙적으로 항상 일부 이벤트 핸들러에서 생성되어야하고 이벤트 핸들러는 래핑되어야 하는데, `"always"`이 이러한 상태를 가장 잘 포착합니다. 그러나 단위 테스트에서는 해당 모드를 사용하고 싶지 않을 것입니다.

드물지만 computed 속성에서 observable을 느리게 생성하는 경우에는 `runInAction`을 사용하여 action에서 생성 애드혹(ad-hoc)을 래핑할 수 있습니다.

#### `computedRequiresReaction: boolean`

action 또는 reaction 외부에서 관찰되지 않은 computed 값에 대해 직접 액세스 하는 것을 금지합니다.
따라서 MobX가 값을 캐시하지 않는 방식으로 computed 값을 사용하지 않는 것을 보장합니다. **디폴트: `false`**.

다음 예에서 MobX는 computed 값을 첫 번째 코드 블록에 캐시하지 않고 결과를 두 번째 및 세 번째 블록에 캐시합니다.

```javascript
class Clock {
    seconds = 0

    get milliseconds() {
        console.log("computing")
        return this.seconds * 1000
    }

    constructor() {
        makeAutoObservable(this)
    }
}

const clock = new Clock()
{
    // 이것은 두 번 계산되지만 이 플래그에 의해 경고됩니다.
    console.log(clock.milliseconds)
    console.log(clock.milliseconds)
}
{
    runInAction(() => {
        // 한 번만 계산합니다.
        console.log(clock.milliseconds)
        console.log(clock.milliseconds)
    })
}
{
    autorun(() => {
        // 한 번만 계산합니다.
        console.log(clock.milliseconds)
        console.log(clock.milliseconds)
    })
}
```

#### `observableRequiresReaction: boolean`

관찰되지 않은 observable 액세스에 대해 경고합니다.
"MobX 컨텍스트" 없이 observable을 사용하고 있는지 확인하려면 `observableRequiresReaction`을 사용하세요.
이는 React 컴포넌트에서 누락된 `observer` 래퍼를 찾는 좋은 방법입니다. 그뿐만 아니라 누락된 action도 찾을 수 있습니다. **디폴트: `false`**

```javascript
configure({ observableRequiresReaction: true })
```

**참고:** `observer`로 래핑된 컴포넌트에서 propType을 사용하면 이 규칙에 대해 false positive가 트리거될 수 있습니다.

#### `reactionRequiresObservable: boolean`

reaction(예: `autorun`)이 observable에 액세스하지 않고 생성되면 경고합니다.
이렇게 하면 불필요하게 React 컴포넌트를 `observer`로 래핑하고 있거나 함수를 `action`으로 래핑하고 있는지, 또는 일부 데이터 구조나 속성을 observable로 설정하지 않았는지 찾아낼 수 있습니다. **디폴트: `false`**

```javascript
configure({ reactionRequiresObservable: true })
```

#### `disableErrorBoundaries: boolean`

기본적으로 MobX는 코드에서 발생하는 예외를 catch하고 다시 throw하여 하나의 예외적인 reaction이 다른 reaction의 예약된 실행을 방해하지 않도록 합니다. 즉, 예외가 발생했던 코드로 다시 전파되지 않으므로 try·catch를 사용하여 예외를 catch 할 수 없습니다.

error boundaries를 비활성화하면 예외가 derivation을 벗어날 수 있습니다. 이렇게 하면 디버깅이 쉬울 수 있지만, MobX 및 확장으로 인해 응용 프로그램이 복구할 수 없는 손상된 상태가 될 수 있습니다. **디폴트: `false`**.

이 옵션은 단위테스트에 적합하지만, 각 테스트 후에 `_resetGlobalState`를 호출해야 합니다. jest에서는 `afterEach`를 사용합니다.

```js
import { _resetGlobalState, observable, autorun, configure } from "mobx"

configure({ disableErrorBoundaries: true })

test("Throw if age is negative", () => {
    expect(() => {
        const age = observable.box(10)
        autorun(() => {
            if (age.get() < 0) throw new Error("Age should not be negative")
        })
        age.set(-1)
    }).toThrow("Age should not be negative")
})

afterEach(() => {
    _resetGlobalState()
})
```

#### `safeDescriptors: boolean`

MobX는 일부 필드를 **환경 설정할 수 없거**나 **쓸 수 없도록** 만들어서, 지원되지 않거나 코드를 손상할 가능성이 가장 큰 작업을 수행하지 못하도록 합니다. 그러나 이러한 설정은 테스트에서 **spying·mocking·stubbing**을 예방할 수도 있습니다.
`configure({ safeDescriptors: false })`를 사용하면 안전 조치가 비활성화되어 모든 항목에 대해 **환경설정** 및 **쓸 수** 있습니다.
기존 observable에는 영향을 주지 않으며, 환경설정이 완료된 후에 생성된 observable에만 영향을 줍니다.
필요할 때만 <span style="color:red">**주의해서 사용하세요**</span>. - 모든 테스트에 대해 해당 기능을 전역적으로 끄지 마세요. 그렇지 않으면, 오탐(코드가 깨진 테스트 통과)의 위험이 있습니다. **디폴트: `true`**

```javascript
configure({ safeDescriptors: false })
```

## 추가 환경설정 옵션

#### `isolateGlobalState: boolean`

동일한 환경에서 MobX 인스턴스가 여러 개 활성 상태인 경우 MobX의 전역 state를 격리합니다. 이러한 설정은 MobX를 사용하는 캡슐화된 라이브러리가 있고 MobX를 사용하는 앱과 동일한 페이지가 있을 때 유용합니다. 라이브러리에서 `configure({ isolateGlobalState: true })`을 호출하면 라이브러리 내부의 반응성이 자동으로 유지됩니다.

해당 옵션이 없고 여러 MobX 인스턴스가 활성 상태인 경우 내부 state는 공유됩니다. 이러한 경우 두 인스턴스의 observable을 함께 사용할 수 있다는 장점이 있으며, MobX 버전이 일치해야 한다는 단점이 있습니다. **디폴트: `false`**

```javascript
configure({ isolateGlobalState: true })
```

#### `reactionScheduler: (f: () => void) => void`

모든 MobX reaction을 실행하는 새 함수를 설정합니다.
기본적으로 `reactionScheduler`는 다른 동작 없이 `f` reaction만 실행합니다.
이 기능은 기본 디버깅을 수행하거나 애플리케이션 업데이트를 시각화하는 reaction 속도를 늦출 때 유용합니다. **디폴트: `f => f()`**

```javascript
configure({
    reactionScheduler: (f): void => {
        console.log("Running an event after a delay:", f)
        setTimeout(f, 100)
    }
})
```
