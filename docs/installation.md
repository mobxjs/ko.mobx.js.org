---
title: 설치 방법
sidebar_label: 설치 방법
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# 설치 방법

MobX는 브라우저와 NodeJS를 포함한 모든 ES5 환경에서 작동합니다.

React 바인딩에는 함수형 컴포넌트만 지원하는 `mobx-react-lite`와 클래스 기반 컴포넌트도 지원하는 `mobx-react` 이렇게 총 두가지 종류가 있습니다. 사용 목적에 맞도록 하단의 _Yarn_ 또는 _NPM_ 커맨드를 참고하세요.

**Yarn:** `yarn add mobx`

**NPM:** `npm install --save mobx`

**CDN:** https://cdnjs.com/libraries/mobx / https://unpkg.com/mobx/dist/mobx.umd.production.min.js

## 클래스 속성에 대한 transpilation 설정

⚠️ **경고:** Typescript 또는 Babel과 함께 MobX를 사용할 때 클래스를 사용할 계획이라면, 클래스 필드에 TC-39 사양을 가진 transpilation을 사용하도록 구성을 업데이트 해야 합니다. 그렇지 않으면 클래스 필드가 초기화되기 전에 observable을 만들 수 없습니다.

-   **TypeScript**: `"useDefineForClassFields": true`로 설정합니다.
-   **Babel**: 7.12 버전 이상만 사용 가능하며, 아래와 같이 설정합니다.
    ```json
    {
        "plugins": [["@babel/plugin-proposal-class-properties", { "loose": false }]],
        // Babel >= 7.13.0 (https://babeljs.io/docs/en/assumptions)
        "assumptions": {
            "setPublicClassFields": false
        }
    }
    ```
검증을 위해 소스 코드 가장 앞줄에 아래와 같은 코드를 입력하세요. (예 : index.js)
```javascript
if (!new class { x }().hasOwnProperty('x')) throw new Error('Transpiler is not configured correctly');
```

## 오래된 Javascript 환경의 MobX

기본적으로 MobX는 최적의 성능과 호환성을 위해 `Proxy`를 사용합니다. 그러나 오래된 Javascript 엔진에서는 `Proxy`를 사용할 수 없습니다.([Proxy 지원](https://kangax.github.io/compat-table/es6/#test-Proxy)을 확인해주세요.) 예를 들어 Internet Explorer(Edge 이전), Node.js < 6, iOS < 10, RN 0.59 이전의 Android가 있습니다.

이러한 경우 MobX는 프록시 지원 없이 거의 동일하게 작동하는 ES5 호환 구현으로 대체할 수 있으나, 몇 가지 [제한 사항](configuration.md#limitations-without-proxy-support)이 있습니다. [`useProxies`](configuration.md#proxy-support)를 구성하여 대체 구현을 명시적으로 활성화해야 합니다.

```javascript
import { configure } from "mobx"

configure({ useProxies: "never" }) // Or "ifavailable".
```

## MobX 및 Decorators

이전에 MobX를 사용하거나 온라인 튜토리얼을 수강한 경험이 있다면, `@observable`과 같은 데코레이터가 있는 MobX를 보았을 것입니다.
MobX 6에서는 표준 Javscript와의 최대 호환성을 위해 데코레이터에서 벗어나는 것을 기본으로 합니다.
그래도 [활성화](enabling-decorators.md)하면 계속 사용할 수 있습니다.

## Development vs Production

`.[production|development].min.js`로 끝나는 사전 빌드 배포를 사용하지 않는다면, Mobx는 `process.env.NODE_ENV` 변수를 사용하여 환경을 감지합니다. production에서 `"production"`으로 설정되어 있는지 확인해보세요. 이러한 방식은 번들러([webpack](https://reactjs.org/docs/optimizing-performance.html#webpack)
    [Rollup](https://reactjs.org/docs/optimizing-performance.html#rollup)
    [Browserify](https://reactjs.org/docs/optimizing-performance.html#browserify)
    [Brunch](https://reactjs.org/docs/optimizing-performance.html#brunch))에서 잘 작동할 것입니다.

[`enforceAction`](https://mobx.js.org/configuration.html#enforceactions)과 같은 대부분의 안전 검사는 development 환경에서만 실행됩니다.

## 다른 프레임워크와 플랫폼에서 사용하는 MobX

-   [MobX.dart](https://mobx.netlify.app/): Flutter, Dart에 대한 MobX
-   [lit-mobx](https://github.com/adobe/lit-mobx): lit-element에 대한 MobX
-   [mobx-angular](https://github.com/mobxjs/mobx-angular): angular에 대한 MobX
-   [mobx-vue](https://github.com/mobxjs/mobx-vue): Vue에 대한 MobX
