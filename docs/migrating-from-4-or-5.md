---
title: MobX 4/5에서 마이그레이션 하기
sidebar_label: MobX 4/5에서 마이그레이션 하기 {🚀}
hide_title: true
---

<script async type="text/javascript" src="//cdn.carbonads.com/carbon.js?serve=CEBD4KQ7&placement=mobxjsorg" id="_carbonads_js"></script>

# MobX 4/5에서 마이그레이션 하기 {🚀}

MobX 6는 MobX 5와는 사뭇 다릅니다. 이 페이지에서는 MobX 4 및 5에서 6까지의 마이그레이션 가이드와 모든 변경 사항에 대한 광범위한 목록을 다룹니다.

자세한 내용은 MobX 6.0 [CHANGELOG](https://github.com/mobxjs/mobx/blob/main/packages/mobx/CHANGELOG.md#600)를 확인하세요.

_⚠️ **경고**: 코드베이스의 크기 및 복잡성, MobX 사용 패턴 및 자동 테스트의 품질과 같은 요소에 따라 이 마이그레이션 가이드를 수행하는 데 1시간에서 이틀 정도가 소요됩니다. 예기치 않은 결함을 발생시킬 정도로 지속적인 통합 또는 QA∙테스트 절차를 신뢰하지 않는 경우 업그레이드를 자제하십시오. MobX 자체의 변경 또는 Babel∙TypeScript 빌드 구성에 필요한 변경으로 인해 예기치 않은 동작 변경이 발생할 수 있습니다.  ⚠️_

## 시작하기

1. `mobx`를 최신 버전의 MobX 4/5로 업데이트하고 deprecation 메시지를 해결합니다.
2. `mobx`를 6 버전으로 업데이트합니다.
3. MobX 4에서 업그레이드하며 프록시 없이 Internet Explorer∙React Native를 지원해야 하는 경우, 애플리케이션을 초기화할 때 `import { configure } from "mobx"; configure({ useProxies: "never" })`를 호출하여 프록시 구현을 back-out(⭐️백업?)합니다. 자세한 내용은 [프록시(Proxy) 지원](configuration.md#proxy-support) 섹션을 참조하세요.
4. babel 사용 시
    - Babel을 사용 중이며 클래스 속성(class-properties)을 사용하도록 설정한 경우 레거시 loose 필드 지원 `["@babel/plugin-proposal-class-properties", { "loose": false }]`를 사용하지 않도록 설정합니다.
    - (선택사항) MobX 6에서는 데코레이터가 옵트인(opt-in) 되었습니다. 더 이상 데코레이터를 사용하지 않으려면 babel 구성 및 종속성에서 `plugin-proposal-decorators`를 제거하세요. 자세한 내용은 [데코레이터 사용하기 {🚀}](enabling-decorators.md) 섹션을 참조하세요.
5. Typescript 사용 시
    - 컴파일러 config에 `"useDefineForClassFields": true` 플래그를 추가합니다.
    - (선택사항)MobX 6에서는 데코레이터가 옵트인(opt-in) 되었습니다. 더 이상 데코레이터를 사용하지 않으려면 TypeScript config에서 `experimentalDecorators` 구성을 제거 또는 비활성화하세요. 자세한 내용은 [데코레이터 사용하기 {🚀}](enabling-decorators.md) 섹션을 참조하세요.
6. MobX 기본값 구성이 더욱 엄격해졌습니다. 업그레이드를 완료한 후 새 기본값을 적용하는 것이 좋습니다. [환경설정 {🚀}](configuration.md) 섹션을 확인해보세요. 마이그레이션 중에는 `import {configure} from "mobx"; configure({ enforceActions: "never" });`를 통해 즉시 v4/v5에서와 같은 방식으로 MobX를 구성하는 것이 좋습니다. 전체 마이그레이션 프로세스를 완료하고 프로젝트가 예상대로 작동하는지 확인한 후 `computedRequiresReaction`, `reactionRequiresObservable` 및 `observableRequiresReaction` 플래그를 활성화하고 `enforceActions: "observed"`를 적용하여 보다 자연스러운 MobX 코드를 작성합니다.

## `makeObservable`을 사용하기 위한 클래스 업그레이드

클래스 필드 구성 방식의 표준화된 JavaScript 제한으로 인해 MobX는 더 이상 Decorator나 `decorate` 유틸리티를 통해 클래스 필드의 동작을 변경할 수 없습니다. 대신 `constructor`에서 필드를 observable로 설정해야 합니다. 이 작업은 세 가지 방법으로 수행할 수 있습니다.

1. 모든 데코레이터를 제거하고 `constructor`에서 `makeObservable`를 호출한 후 어떤 데코레이터를 사용하여 어떤 필드를 observable로 만들지 명시적으로 정의합니다. 예: `makeObservable(this, { count: observable, tick: action, elapsedTime: computed })`(두 번째 인수는 `decorate`에 전달되는 인수에 해당합니다.) 코드 베이스에서 데코레이터를 삭제하려는 경우 프로젝트 규모가 아직 크지 않다면 이 방법을 사용하는 것이 좋습니다.
2. 모든 데코레이터를 제거하고 `constructor`에서 `makeObservable(this)`를 호출합니다. 그러면 데코레이터에서 생성한 메타데이터가 감지됩니다. MobX6 마이그레이션의 영향을 제한하려면 이 방법을 사용하는 것이 좋습니다.
3. 데코레이터를 제거하고 클래스의 `constructor`에서(⭐️constructor 클래스에서...는 아니겠지?) `makeAutoObservable(this)`를 사용하세요.

자세한 내용은 [makeObservable∙makeAutoObservable](observable-state.md)을 확인하세요.

참고사항:

1. `makeObservable`∙`makeAutoObservable`은 MobX 기반 멤버를 선언하는 모든 클래스 정의에서 사용되어야 합니다. 서브클래스나 super 클래스 모두 observable 멤버를 도입한다면, 둘 다 `makeObservable`을 호출해야 합니다.
2. `makeAutoObservable`은 새로운 데코레이터인 `autoAction`을 사용해 메서드를 표시하며, 파생 컨텍스트가 아닌 경우에만 `action`을 적용할 것입니다. 이렇게 하면 computed 속성에서도 자동으로 decorate 된 메서드를 호출할 수 있습니다.

클래스가 많은 대규모 코드 베이스를 마이그레이션하는 것은 어려울 수 있습니다. 하지만 걱정 마세요. 위의 프로세스를 자동화할 수 있는 code-mod가 있습니다!!

## `mobx-undecorate` codemod로 코드 업그레이드하기

기존 MobX 사용자라면 `decorate`하기 위해 코드 내에서 많은 데코레이터를 사용하거나 동등한 호출을 했을 것입니다.

[`mobx-undecorate`](https://www.npmjs.com/package/mobx-undecorate) 패키지는 MobX 6에 맞게 코드를 자동으로 업데이트해주는 codemod를 제공합니다. 설치할 필요가 없으며, [`npx`](https://www.npmjs.com/package/npx)를 사용하여 다운로드하고 실행합니다. 물론 `npx`가 설치되어 있어야 합니다.

MobX 데코레이터의 모든 사용을 제거하고 동등한 `makeObservable` 호출로 대체하려면 소스 코드가 포함된 디렉터리로 이동하여 다음을 실행합니다.

```shell
npx mobx-undecorate
```

MobX는 계속해서 데코레이터를 지원합니다. 따라서 데코레이터를 유지하고 필요한 경우에만 `makeObservable(this)`를 도입하려면 `--keepDecorators` 옵션을 사용하세요.

```shell
npx mobx-undecorate --keepDecorators
```

더 많은 옵션은 [문서](https://www.npmjs.com/package/mobx-undecorate)를 참고하세요.

### `mobx-undecorate`의 한계

`mobx-undecorate` 명령은 아직 생성자를 가지고 있지 않은 클래스에 생성자를 도입해야 합니다. 생성자의 기본 클래스가 인수를 필요로 하는 경우 codemod는 업그레이드되는 서브클래스에 해당 인수를 도입할 수 없으며 `super` 호출도 인수를 전달하지 않습니다. 이 부분은 직접 수정해야 합니다.
이 경우 툴에서 `// TODO: [mobx-undecorate]` 코멘트를 생성합니다.

React 클래스 구성요소가 옳은 일을 하고 super 클래스에 `props`을 전달하는 특별한 경우가 있습니다.(⭐️ 뭐야 이렇게 끝나는거 맞아?)