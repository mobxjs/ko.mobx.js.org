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

해당 저장소는 <a href="https://mobx.js.org/">https://mobx.js.org</a>의 내용을 영-한 번역을 통해 모국어가 한국어인 개발자가 mobx를 쉽게 접할 수 있도록 하는데 목표를 두고 있습니다.

## 기여방법

### 의사소통
- <a href="https://join.slack.com/t/ko-mobx/shared_invite/zt-skky1w3m-UD2_sY9880STvmnyJzdt9Q">슬랙</a>을 통해 번역 관련 궁금한 점 또는 mobx 관련 다양한 이야기를 나눕니다.

### 가이드라인
- 중복된 작업을 피하기 위해, 슬랙 채널(번역-진행사항)을 통해 작업 시작할 파일 명을 입력합니다. ex) actions.md
- <a href="https://github.com/mobxjs/ko.mobx.js.org/wiki/%EB%B2%88%EC%97%AD-%EB%AA%A8%EB%B2%94-%EC%82%AC%EB%A1%80">번역 모범 사례</a>를 참고하여 번역을 진행합니다.
- <a href="https://docs.google.com/spreadsheets/d/1fYaEI8vz26N3R2VaxrlNnk9fMQ8zIy4RpvjRp4jZd0Q/edit#gid=843106813">합의된 번역어</a>로 번역합니다. 공동작업에선 번역어 통일이 매우 중요합니다.
- 경어체를 사용합니다.
- PR 전 <a href="http://speller.cs.pusan.ac.kr/">맞춤법 검사기</a>를 사용해 틀린 부분을 교정합니다. 검사기를 돌리지 않았다고 판단되는 커밋은 PR 받지 않겠습니다. 리뷰자 역시 맞춤법 검사기를 사용해, 번역자가 맞춤법을 지켜 번역했는지 확인합니다.
- 공백(스페이스), 큰따옴표("), 작은따옴표('), 대시(-), 백틱(`)을 비롯한 모든 특수문자는 수정하시면 안 됩니다. 자연어만 수정(알파벳을 한글로 수정)해주세요.

### Pull Request 규칙
번역과 관련된 PR에는 번역 규칙을 준수하지 않은 경우 PR을 받지 않습니다.
PR을 올릴 시 체크리스트를 꼭 확인해주세요.
PR에는 이슈를 확인할 수 있게 의미 있는 제목을 기재해주세요.
#### 예시
- [문서번역] installation.md
- [오역수정] lazy-observables.md

### 로컬 서버 세팅 방법
1. 개인 저장소에 포크 받은 후, 포크된 저장소를 클론합니다.
```angular2html
git clone https://github.com/나의GitHub유저네임/ko.mobx.js.org
```
2. 해당 프로젝트를 열고 다음 명령어를 실행합니다.
```angular2html
cd website && yarn && yarn start
```

