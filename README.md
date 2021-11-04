<img src="https://mobx.js.org/assets/mobx.png" alt="logo" height="120" align="right" />

# MobX

_Simple, scalable state management._

[![GitHub deployments](https://img.shields.io/github/deployments/mobxjs/ko.mobx.js.org/production?label=vercel&logo=vercel)](https://vercel.com/mobxjs/ko-mobx-js-org)
[![npm version](https://badge.fury.io/js/mobx.svg)](https://badge.fury.io/js/mobx)
[![OpenCollective](https://opencollective.com/mobx/backers/badge.svg)](docs/backers-sponsors.md#backers)
[![OpenCollective](https://opencollective.com/mobx/sponsors/badge.svg)](docs/backers-sponsors.md#sponsors)

[![Discuss on Github](https://img.shields.io/badge/discuss%20on-GitHub-orange)](https://github.com/mobxjs/mobx/discussions)
[![View changelog](https://img.shields.io/badge/changelogs.xyz-Explore%20Changelog-brightgreen)](https://changelogs.xyz/mobx)

---

해당 저장소는 <a href="https://mobx.js.org/">https://mobx.js.org</a>의 내용을 영-한 번역을 통해 모국어가 한국어인 개발자가 MobX를 쉽게 접할 수 있도록 하는데 목표를 두고 있습니다.

## 소개

애플리케이션 상태에서 파생되는 모든 것은 자동으로 되어야 합니다.
MobX는 functional reactive programming을 투명하게 적용함으로써 상태 관리를 쉽고 확장성 있게 만들어주는 검증된 라이브러리입니다. (TFRP, Transparent Functional Reactive Programming).

### MobX의 철학은 간단합니다.

#### 쉽다.
- 미니멀하고 보일러 플레이트로부터 자유로운 코드를 통해 당신의 의도를 잘 담아내 보세요.
- 데이터를 변경하고 싶습니까? 자바스크립트 할당문을 사용하면 됩니다.
- 비동기 과정에서 데이터를 변경하고 싶습니까? 새로운 도구는 필요 없으며 MobX 시스템이 변경사항을 찾아내고 사용 중인 곳에 전달합니다.

#### 렌더링 최적화를 쉽게 할 수 있다.
- 데이터의 모든 변경과 사용은 런타임에 추적되고 상태와 출력 사이의 모든 관계를 나타내는 의존 트리(dependency tree)를 만듭니다. 따라서 리액트 컴포넌트들처럼 상태에 따라 필요한 경우에만 연산이 실행됩니다. 그래서 memoization, selectors 등을 사용하여 컴포넌트 최적화 작업을 할 필요가 없습니다.


#### 구조가 자유롭다.
- UI 프레임워크 밖에서 애플리케이션 상태를 관리 할 수 있습니다. 따라서 코드 분리가 쉽고 다른 곳에서 사용하기 유용하며 무엇보다 쉽게 테스트 할 수 있습니다.




## 기여방법

### 가이드라인
- 중복된 작업을 피하기 위해, 슬랙 채널(번역-진행사항)을 통해 작업 시작할 파일 명을 입력합니다. ex) actions.md
- <a href="https://github.com/mobxjs/ko.mobx.js.org/wiki/%EB%B2%88%EC%97%AD-%EB%AA%A8%EB%B2%94-%EC%82%AC%EB%A1%80">번역 모범 사례</a>를 참고하여 번역을 진행합니다.
- <a href="https://docs.google.com/spreadsheets/d/1fYaEI8vz26N3R2VaxrlNnk9fMQ8zIy4RpvjRp4jZd0Q/edit#gid=843106813">합의된 번역어</a>로 번역합니다. 공동작업에선 번역어 통일이 매우 중요합니다.
- 경어체를 사용합니다.
- PR 전 <a href="http://speller.cs.pusan.ac.kr/">맞춤법 검사기</a>를 사용해 틀린 부분을 교정합니다. 검사기를 돌리지 않았다고 판단되는 커밋은 PR 받지 않겠습니다. 리뷰자 역시 맞춤법 검사기를 사용해, 번역자가 맞춤법을 지켜 번역했는지 확인합니다.
- 공백(스페이스), 큰따옴표("), 작은따옴표('), 대시(-), 백틱(`)을 비롯한 모든 특수문자는 수정하시면 안 됩니다. 자연어만 수정(알파벳을 한글로 수정)해주세요.

### Pull Request 규칙
PR을 올릴 시 체크리스트를 꼭 확인해주세요.
PR에는 이슈를 확인할 수 있게 의미 있는 제목을 기재해주세요.
#### 예시
- [문서번역] installation.md
- [오번역수정] lazy-observables.md

### 로컬 서버 세팅 방법
1. 개인 저장소에 포크 받은 후, 포크된 저장소를 클론합니다.
```angular2html
git clone https://github.com/나의GitHub유저네임/ko.mobx.js.org
```
2. 해당 프로젝트를 열고 다음 명령어를 실행합니다.
```angular2html
cd website && yarn && yarn start
```

