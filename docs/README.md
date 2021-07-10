---
title: MobX에 대하여
sidebar_label: MobX에 대하여
hide_title: true
---

<img src="https://mobx.js.org/assets/mobx.png" alt="logo" height="120" align="right" />

# MobX

_간단하고 확장 가능한 상태 관리._

[![Discuss on Github](https://img.shields.io/badge/discuss%20on-GitHub-orange)](https://github.com/mobxjs/mobx/discussions)
[![npm version](https://badge.fury.io/js/mobx.svg)](https://badge.fury.io/js/mobx)
[![OpenCollective](https://opencollective.com/mobx/backers/badge.svg)](backers-sponsors.md#backers)
[![OpenCollective](https://opencollective.com/mobx/sponsors/badge.svg)](backers-sponsors.md#sponsors)

---

MobX는 아래의 후원자들과 [다른 많은 후원자](backers-sponsors.md#backers)들의 도움이 있었기에 가능합니다. 후원은 프로젝트 지속에 직접적인 영향을 미칩니다.

**🥇 Gold sponsors (\$3000+ total contribution):** <br/>
<a href="https://mendix.com/"><img src="https://mobx.js.org/assets/mendix-logo.png" align="center" width="100" title="Mendix" alt="Mendix" /></a>
<a href="https://frontendmasters.com/"><img src="https://mobx.js.org/assets/frontendmasters.jpg" align="center" width="100" title="Frontend Masters" alt="Frontend Masters"></a>
<a href="https://opensource.facebook.com/"><img src="https://mobx.js.org/assets/fbos.jpeg" align="center" width="100" title="Facebook Open Source" alt="Facebook Open Source" /></a>
<a href="http://auctionfrontier.com/"><img src="https://mobx.js.org/assets/auctionfrontier.jpeg" align="center" width="100" title="Auction Frontier" alt="Auction Frontier"></a>
<a href="https://www.guilded.gg/"><img src="https://mobx.js.org/assets/guilded.jpg" align="center" width="100" title="Guilded" alt="Guilded" /></a>
<a href="https://coinbase.com/"><img src="https://mobx.js.org/assets/coinbase.jpeg" align="center" width="100" title="Coinbase" alt="Coinbase" /></a>
<a href="https://www.canva.com/"><img src="https://mobx.js.org/assets/canva.png" align="center" width="100" title="Canva" alt="Canva" /></a>

**🥈 Silver sponsors (\$100+ per month):**<br/>
<a href="https://www.codefirst.co.uk/"><img src="https://mobx.js.org/assets/codefirst.png" align="center" width="100" title="CodeFirst" alt="CodeFirst"/></a>
<a href="https://www.dcsl.com/"><img src="https://mobx.js.org/assets/dcsl.png" align="center" width="100" title="DCSL Guidesmiths" alt="DCSL Guidesmiths"/></a>
<a href="https://www.bugsnag.com/platforms/react-error-reporting?utm_source=MobX&utm_medium=Website&utm_content=open-source&utm_campaign=2019-community&utm_term=20190913"><img src="https://mobx.js.org/assets/bugsnag.jpg" align="center" width="100" title="Bugsnag" alt="Bugsnag"/></a>
<a href="https://curology.com/blog/tech"><img src="https://mobx.js.org/assets/curology.png" align="center" width="100" title="Curology" alt="Curology"/></a>
<a href="https://modulz.app/"><img src="https://mobx.js.org/assets/modulz.png" align="center" width="100" title="Modulz" alt="Modulz"/></a>
<a href="https://space307.com/?utm_source=sponsorship&utm_medium=mobx&utm_campaign=readme"><img src="https://mobx.js.org/assets/space307.png" align="center" width="100" title="Space307" alt="Space307"/></a>

**🥉 Bronze sponsors (\$500+ total contributions):**<br/>
<a href="https://mantro.net/jobs/warlock"><img src="https://mobx.js.org/assets/mantro.png" align="center" width="100" title="mantro GmbH" alt="mantro GmbH"></a>
<a href="https://www.algolia.com/"><img src="https://mobx.js.org/assets/algolia.jpg" align="center" width="100" title="Algolia" alt="Algolia" /></a>
<a href="https://talentplot.com/"><img src="https://mobx.js.org/assets/talentplot.png" align="center" width="100" title="talentplot" alt="talentplot"></a>
<a href="https://careers.dazn.com/"><img src="https://mobx.js.org/assets/dazn.png" align="center" width="100" title="DAZN" alt="DAZN"></a>
<a href="https://blokt.com/"><img src="https://mobx.js.org/assets/blokt.jpg" align="center" width="100" title="Blokt" alt="Blokt"/></a>

---

## 소개

애플리케이션 상태에서 파생되는 모든 것은 자동으로 되어야 합니다.

MobX는 functional reactive programming을 투명하게 적용함으로써 상태 관리를 쉽고 확장성 있게 만들어주는 검증된 라이브러리입니다. (TFRP, Transparent Functional Reactive Programming).
MobX의 철학은 간단합니다.

<div class="benefits">
    <div>
        <div class="pic">😙</div>
        <div>
            <h5>쉽다.</h5>
            <p>미니멀하고 보일러 플레이트로부터 자유로운 코드를 통해 당신의 의도를 잘 담아내 보세요.
            데이터를 변경하고 싶습니까? 자바스크립트 할당문을 사용하면 됩니다.
            비동기 과정에서 데이터를 변경하고 싶습니까? 새로운 도구는 필요 없으며 MobX 시스템이 변경사항을 찾아내고 사용 중인 곳에 전달합니다.
            </p>
        </div>
    </div>
    <div>
        <div class="pic">🚅</div>
        <div>
            <h5>렌더링 최적화를 쉽게 할 수 있다.</h5>
            <p>
                데이터의 모든 변경과 사용은 런타임에 추적되고 상태와 출력 사이의 모든 관계를 나타내는 의존 트리(dependency tree)를 만듭니다. 
                따라서 리액트 컴포넌트들처럼 상태에 따라 필요한 경우에만 연산이 실행됩니다. 그래서 memoization, selectors 등을 사용하여 컴포넌트 최적화 작업을 할 필요가 없습니다.
            </p>
        </div>
    </div>
    <div>
        <div class="pic">🤹🏻‍♂️</div>
        <div>
            <h5>구조가 자유롭다</h5>
            <p>
                UI 프레임워크 밖에서 애플리케이션 상태를 관리 할 수 있습니다. 따라서 코드 분리가 쉽고 다른 곳에서 사용하기 유용하며 무엇보다 쉽게 테스트 할 수 있습니다.
            </p>
        </div>
    </div>
</div>

## 간단한 예제

MobX를 사용하는 코드

```javascript
import React from "react"
import ReactDOM from "react-dom"
import { makeAutoObservable } from "mobx"
import { observer } from "mobx-react"

// 애플리케이션 상태를 모델링합니다.
class Timer {
    secondsPassed = 0

    constructor() {
        makeAutoObservable(this)
    }

    increase() {
        this.secondsPassed += 1
    }

    reset() {
        this.secondsPassed = 0
    }
}

const myTimer = new Timer()

// observable state를 사용하는 사용자 인터페이스를 구축합니다.
const TimerView = observer(({ timer }) => (
    <button onClick={() => timer.reset()}>Seconds passed: {timer.secondsPassed}</button>
))

ReactDOM.render(<TimerView timer={myTimer} />, document.body)

// 매초마다 Seconds passed: X를 업데이트 합니다.
setInterval(() => {
    myTimer.increase()
}, 1000)
```

리액트 컴포넌트인 `TimerView`를 감싸고 있는 `observer`는 observable인 `timer.secondsPassed`에 의존하여 자동으로 렌더링 됩니다. reactivity 시스템은 나중에 해당 필드가 _정확하게_ 수정될 때 컴포넌트를 다시 렌더링 합니다.

모든 이벤트(`onClick`, `setInterval`)는 observable state(`myTimer.secondsPassed`)를 변경시키는 _action_(`myTimer.increase`, `myTimer.reset`)을 호출합니다.
observable state의 변경 사항은 모든 연산과 변경사항에 따라 달라지는 부수 효과(`TimerView`)에 전파됩니다.

<img alt="MobX unidirectional flow" src="https://mobx.js.org/assets/flow2.png" align="center" />

위 그림은 위의 예시 또는 MobX를 사용하는 다른 애플리케이션에 적용할 수 있습니다.

더 복잡한 예시를 이용한 [The gist of MobX](https://mobx.js.org/the-gist-of-mobx.html), [10 minute interactive introduction to MobX and React](https://mobx.js.org/getting-started)를 통해 MobX의 핵심 개념에 대해 알아볼 수 있습니다.
MobX 모델의 철학과 장점은 [UI as an afterthought](https://michel.codes/blogs/ui-as-an-afterthought) 와 [How to decouple state and UI (a.k.a. you don’t need componentWillMount)](https://hackernoon.com/how-to-decouple-state-and-ui-a-k-a-you-dont-need-componentwillmount-cc90b787aa37) 에서 자세하게 확인할 수 있습니다.

<div class="cheat"><a href="https://gum.co/fSocU"><button title="Download the MobX 6 cheat sheet and sponsor the project">Download the MobX 6 cheat sheet</button></a></div>

## 다른 사람들이 말하기를...

> 몇 주 동안 MobX를 개인 프로젝트에 사용한 후 팀에 소개하게 되어 매우 기쁩니다. 시간은 절반이 걸리고 재미는 두 배였습니다.

> MobX를 사용하면서 "이건 너무 간단해서 절대 작동하지 않을 것 같아"라고 계속 생각이 드는 것은 잘못된 것으로 증명되었습니다.

> MobX를 사용하여 규모가 큰 애플리케이션을 구축했습니다. Redux를 사용하던 앱과 비교했을 때 읽기 쉽고 추론하기도 훨씬 쉬웠습니다.

> MobX는 내가 항상 원하는 것이다! 정말 놀랍고 간단하고 빠르다! 정말 멋지다! 놓치지 마라!

## 추가 자료

-   [MobX cheat sheet (also sponsors the project)](https://gum.co/fSocU)
-   [10 minute interactive introduction to MobX and React](https://mobx.js.org/getting-started)
-   [Egghead.io course, based on MobX 3](https://egghead.io/courses/manage-complex-state-in-react-apps-with-mobx)

### MobX 책

[<img src="https://mobx.js.org/assets/book.jpg" height="80px"/> ](https://books.google.nl/books?id=ALFmDwAAQBAJ&pg=PP1&lpg=PP1&dq=michel+weststrate+mobx+quick+start+guide:+supercharge+the+client+state+in+your+react+apps+with+mobx&source=bl&ots=D460fxti0F&sig=ivDGTxsPNwlOjLHrpKF1nweZFl8&hl=nl&sa=X&ved=2ahUKEwiwl8XO--ncAhWPmbQKHWOYBqIQ6AEwAnoECAkQAQ#v=onepage&q=michel%20weststrate%20mobx%20quick%20start%20guide%3A%20supercharge%20the%20client%20state%20in%20your%20react%20apps%20with%20mobx&f=false)

Created by [Pavan Podila](https://twitter.com/pavanpodila) and [Michel Weststrate](https://twitter.com/mweststrate).

### 영상

-   [Introduction to MobX & React in 2020](https://www.youtube.com/watch?v=pnhIJA64ByY) by Leigh Halliday, _17min_.
-   [ReactNext 2016: Real World MobX](https://www.youtube.com/watch?v=Aws40KOx90U) by Michel Weststrate, _40min_, [slides](https://docs.google.com/presentation/d/1DrI6Hc2xIPTLBkfNH8YczOcPXQTOaCIcDESdyVfG_bE/edit?usp=sharing).
-   [CityJS 2020: MobX, from mutable to immutable, to observable data](https://youtu.be/sP7dtZm_Wx0?t=27050) by Michel Weststrate, _30min_.
-   [OpenSourceNorth: Practical React with MobX (ES5)](https://www.youtube.com/watch?v=XGwuM_u7UeQ) by Matt Ruby, _42min_.
-   [HolyJS 2019: MobX and the unique symbiosis of predictability and speed](https://www.youtube.com/watch?v=NBYbBbjZeX4&list=PL8sJahqnzh8JJD7xahG5zXkjfM5GOgcPA&index=21&t=0s) by Michel Weststrate, _59min_.
-   [React Amsterdam 2016: State Management Is Easy](https://www.youtube.com/watch?v=ApmSsu3qnf0&feature=youtu.be) by Michel Weststrate, _20min_, [slides](https://speakerdeck.com/mweststrate/state-management-is-easy-introduction-to-mobx).
-   {🚀} [React Live 2019: Reinventing MobX](https://www.youtube.com/watch?v=P_WqKZxpX8g) by Max Gallo, _27min_.

And an all around [MobX awesome list](https://github.com/mobxjs/awesome-mobx#awesome-mobx).

## 공(Credits)

MobX는 spreadsheets에서 볼 수 있는 reactive programming 원칙에서 영감을 받았습니다. 또 MVVM 프레임워크인 MeteorJS tracker, knockout, Vue.js 에서 영감을 받았습니다. MobX는 한 단계 더 나아가 독립 실행형을 제공한 _Transparent Functional Reactive Programming_ 입니다. MobX는 결함이 없고 동기적이며 예측 가능하고 효율적인 방식으로 TFRP를 구현했습니다.

[Mendix](https://github.com/mendix)는 MobX를 유지할 수 있는 유연성과 지원과 실제 복잡한 성능이 중요한 애플리케이션에서 MobX의 철학을 입증할 수 있는 기회를 제공하는 데 많은 공을 들였습니다.
