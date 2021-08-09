---
title: 인수가 필요한 computed
sidebar_label: 인수가 필요한 computed {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 인수가 필요한 computed {🚀}

`computed` 주석은 인수가 필요 없는 getter에만 사용할 수 있습니다.
인수가 필요한 computation은 어떻게 해야 할까요?
특정 `Item`을 렌더링하고 다중 선택을 지원하는 리액트 컴포넌트 예시를 살펴보세요.

`store.isSelected(item.id)`와 같은 derivation은 어떻게 구현할 수 있을까요?

```javascript
import * as React from 'react'
import { observer } from 'mobx-react-lite'

const Item = observer(({ item, store }) => (
    <div className={store.isSelected(item.id) ? "selected" : ""}>
        {item.title}
    </div>
))
```

구현 방식엔 총 네 가지 방법이 있고, [CodeSandbox](https://codesandbox.io/s/multi-selection-odup1?file=/src/index.tsx)에서 직접 시도해볼 수 있습니다.

## 1. derivation은 `computed`가 될 필요가 없습니다.

MobX에서 함수를 추적하기 위해 함수를 `computed`로 표시할 필요는 없습니다.
위의 예시는 이미 기본적으로 완벽하게 작동합니다.
computed 값은 _캐싱 포인트_일 뿐입니다.
derivation이 순수하다면(그래야만 합니다.), `computed`가 아닌 함수나 getter는 동작이 다르지는 않습니다. 단지, 약간 덜 효율적일 뿐입니다.

위의 예제는 `isSelected`가 `computed`가 않음에도 불구하고 잘 작동합니다. 함수는 추적되는 렌더링의 일부로 실행되기 때문에 observer 컴포넌트는 `isSelected`에 의해 읽힌 모든 observable을 탐지하고 구독합니다.

이때 모든 `Item` 컴포넌트가 향후 선택 변경 사항에 대해 반응하는 것을 이해하는 것이 좋습니다.
왜냐하면 모든 `Item` 컴포넌트가 선택 항목을 캡쳐하는 observable에 직접 구독하기 때문입니다.
해당 예제는 최악의 예시입니다. 일반적으로 아무 표시가 없는 함수를 갖는 것은 완전히 괜찮으며, 숫자가 다른 작업을 수행한다는 것을 증명하기 전까지 이러한 전략은 좋습니다.

## 2. 인수에 대해 닫기(Close over the arguments)

해당 구현은 원본보다 더 효율적입니다.

```javascript
import * as React from 'react'
import { computed } from 'mobx'
import { observer } from 'mobx-react-lite'

const Item = observer(({ item, store }) => {
    const isSelected = computed(() => store.isSelected(item.id)).get()
    return (
        <div className={isSelected ? "selected" : ""}>
            {item.title}
        </div>
    )
})
```

reaction 중간에 새로운 computed 값을 만들어낼 수 있습니다. 이렇게 하면 추가 캐싱 포인트를 통해 모든 컴포넌트가 모든 선택 변경에 직접 반응 하는 것을 방지할 수 있습니다.
이러한 접근 방식은 `isSelected` state가 전환되는 컴포넌트만 다시 렌더링 된다는 장점이 있습니다. 그리고 이러한 경우에 `className`을 변경하기 위해 실제로 다시 렌더링 됩니다.

다음 렌더에서 새로운 `computed`를 생성하는 것은 괜찮습니다. 
생성한 `computed`는 캐싱 포인트가 되고 이전 `computed`는 잘 정리될 것입니다.
이러한 방식은 훌륭하고 고급 최적화 기술입니다.

## 3. state 이동

위의 상황에서 `Item`에 대한 선택항목은 `isSelected` observable로 저장할 수 있습니다. 그러면 스토어안에서 사용되는 선택항목은 observable이 아닌 `computed`로 표현할 수 있습니다. `get selection() { return this.items.filter(item => item.isSelected) }`와 같이 사용할 수 있습니다. 그리고 이제 `isSelected`가 필요하지 않습니다.

## 4. computedFn 사용하기 {🚀}

마지막으로,
`mobx-utils`의 [`computedFn`](https://github.com/mobxjs/mobx-utils#computedfn)을 `todoStore.selected` 정의에 사용하여 `isSelected`를 자동으로 메모이제이션 할 수 있습니다.
입력 인수의 모든 조합에 대한 출력을 메모이제이션 하는 함수를 만듭니다.

이러한 방법을 먼저 사용하지 않는 것이 좋습니다. 메모리 사용량에 대해 추론하기 전에 함수를 호출할 인수가 몇개 인지 생각해야 하는 것이 일반적인 메모이제이션 방식입니다.
그러나 어떠한 reaction으로도 결과가 관찰되지 않으면 항목이 자동으로 정리되므로 정상적인 상황에서는 메모리 누수가 발생하지 않습니다.

다시 한번 [연결된 CodeSandbox](https://codesandbox.io/s/multi-selection-odup1?file=/src/index.tsx)를 확인하여 사용해보세요.
