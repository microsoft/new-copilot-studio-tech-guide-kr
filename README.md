# Copilot Studio 기술 가이드 한국어판

Microsoft Copilot Studio의 새로운 에이전트 및 워크플로 환경을 소개하는 사이트와
배포 가능한 샘플입니다.

**한국어 사이트**: https://microsoft.github.io/new-copilot-studio-tech-guide-kr/

**영문 원본**: https://github.com/microsoft/new-copilot-studio-tech-guide

## 저장소 구성

| 폴더 | 설명 |
|---|---|
| `src/` | Astro 사이트: 랜딩 페이지, 시나리오 단계별 가이드, 문서 |
| `deploy/` | Power Platform 환경에 샘플을 반복 배포하는 스크립트 |
| `sample/` | Copilot Studio 솔루션(`sample/solution/`)과 이전 버전 보관 자료(`sample/archive/`) |

## 샘플 배포

샘플에는 대표 Store Associate Assistant, 셀프 서비스용 Returns & Service Assistant,
연결된 에이전트 2개, 인라인 MCP 커넥터 4개가 포함됩니다. 모든 구성 요소는 Power
Platform 안에서 실행되며 외부 서버가 필요하지 않습니다.

다음 교차 플랫폼 스크립트로 배포할 수 있습니다. **Node.js 18 이상**, **pac CLI**,
**Azure CLI**가 필요합니다.

```bash
pac auth create                   # 최초 한 번: 대상 테넌트에 pac 로그인
node deploy/deploy.mjs            # 프로필과 환경을 선택해 배포
node deploy/deploy.mjs --help     # 전체 옵션 확인
```

스크립트는 두 솔루션 가져오기, 커넥터 코드 배포, 연결 생성, 에이전트 게시를 수행한 뒤
API로 자동화할 수 없는 약 2분 분량의 수동 연결 단계를 안내합니다.

자세한 내용은 [`deploy/README.md`](./deploy/README.md), 솔루션 구성과 두 데모
시나리오는 [`sample/solution/README.md`](./sample/solution/README.md)에서 확인하세요.

## 사이트 개발

Node.js **22.12 이상**이 필요합니다.

```bash
npm ci
npm run dev        # http://localhost:4321/new-copilot-studio-tech-guide-kr/
npm run build      # ./dist/에 프로덕션 사이트 생성
```

사이트 분석이 필요하면 `.env.example`을 참고해 `PUBLIC_CLARITY_ID`를 설정하세요.
값을 설정하지 않으면 프로덕션 빌드에서도 Microsoft Clarity를 로드하지 않습니다.

## 번역 원칙

제품명, 에이전트명, MCP 도구명, 스킬명과 파일명은 샘플에서 그대로 찾을 수 있도록
영문을 유지합니다. 사용자에게 노출되는 설명과 시나리오 대화는 한국어로 제공합니다.

## 기여

이슈와 개선 제안을 환영합니다. 프로젝트 기여 정책은 영문 원본의
[CONTRIBUTING](https://github.com/microsoft/new-copilot-studio-tech-guide/blob/main/CONTRIBUTING.md)을
따릅니다.

## 라이선스

MIT License. Copyright (c) Microsoft Corporation.
