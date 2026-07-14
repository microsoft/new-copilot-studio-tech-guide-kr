# BlastBox Omega 데모 솔루션

이 폴더에는 BlastBox Omega 데모를 구성하는 **이식 가능한 Copilot Studio 솔루션**이
있습니다. 솔루션 zip 2개, 압축을 푼 소스, 커넥터 코드, 스킬 번들을 포함합니다.

## 배포에는 `deploy/` 사용

수동으로 구성하지 마세요. 저장소의 교차 플랫폼 스크립트가 두 솔루션 가져오기, 모든
MCP 커넥터의 인라인 코드 배포, 사용자 지정 게시, 인증 없는 연결 생성, 에이전트 게시를
처리합니다. 마지막으로 API가 없어 자동화할 수 없는 에이전트별 MCP 서버 재연결 단계
(약 2분)를 안내합니다.

```bash
pac auth create                   # 최초 한 번: 대상 테넌트에 pac 로그인
node deploy/deploy.mjs            # 프로필과 환경을 선택해 배포
node deploy/deploy.mjs --help     # 전체 옵션 확인
```

요구 사항과 수동 재연결 표를 포함한 전체 절차는
[`deploy/README.md`](../../deploy/README.md)를 참고하세요.

## 폴더 구성

| 파일/폴더 | 설명 |
|---|---|
| `BlastBoxConnectors_1_0_0_1.zip` | 인라인 MCP 서버 4개가 포함된 **커넥터** 솔루션. 고유 이름 `BlastBoxConnectors`로 가져옵니다. |
| `BlastBoxAgents_1_0_0_1.zip` | 에이전트 4개와 Python 스킬이 포함된 **에이전트** 솔루션. 고유 이름 `BlastBoxDeploy`로 가져옵니다. |
| `connectors/<slug>/` | 각 MCP 커넥터의 `apiDefinition.json`, `apiProperties.json`, 인라인 `script.csx`. |
| `skills/` | 에이전트 솔루션에 포함된 Python 스킬 번들의 참조 사본. |
| `src/` | 두 솔루션을 `pac solution unpack`으로 푼 소스. 검토와 diff 용도이며 직접 가져오지 않습니다. |

### 커넥터: 인라인 MCP 서버 4개

| 커넥터 | Slug | 스키마 이름 |
|---|---|---|
| Membership MCP v2 | `membership-mcp-v2` | `cat_membership-20mcp-20v2` |
| Order Management MCP | `order-management-mcp` | `new_order-20management-20mcp` |
| Policy RAG MCP v2 | `policy-rag-mcp-v2` | `new_policy-20rag-20mcp-20v2` |
| Warehouse MCP | `warehouse-mcp` | `new_warehouse-20mcp` |

### 에이전트 4개

| 에이전트 | 역할 | 도구/연결된 에이전트 |
|---|---|---|
| **Store Associate Assistant** | 상위 대표 에이전트: 블록 파티 트레이드업 | Order Management MCP + Membership MCP v2, 연결: Store Policy 및 Inventory & Fulfillment, 스킬: prorated-refund-calculator, points-reconciliation, slip-pdf-generator |
| **Returns & Service Assistant** | 상위 셀프 서비스 에이전트: 카드 재발급 | Membership MCP v2, 연결: Store Policy, 스킬: card-reissue, membership-card-png |
| **Store Policy Agent** | 연결된 하위 에이전트 | Policy RAG MCP v2 |
| **Inventory & Fulfillment Agent** | 연결된 하위 에이전트 | Warehouse MCP |

## 요구 사항

- **Dataverse**와 **Copilot Studio**가 활성화되고 새로운 Copilot Studio 환경을
  사용할 수 있는 Power Platform 환경. **First Release / Early Release** 환경을
  권장합니다.
- 해당 환경에서 솔루션을 가져오고 연결을 만들 수 있는 권한.

## 두 시나리오 검증

배포 후 스크립트가 안내하는 재연결 단계를 완료하고 각 상위 에이전트의 **Preview**에서
시나리오 대화를 실행하세요. 모든 MCP 도구와 Python 스킬이 실제로 동작하는
엔드투엔드 흐름입니다.

**셀프 서비스 카드 재발급**: Returns & Service Assistant에서 회원
`MEGA-BLAST-1024`를 사용합니다. 예상 흐름:
`get_membership` → 본인 확인 → `reissue_card` → `membership-card-png` →
`blastpass_card.png`.

**블록 파티 트레이드업**: Store Associate Assistant에서 회원
`MEGA-BLAST-1024`를 사용합니다. `$76.66` / `$100.00` / `$23.34`, MEGA 전용
게임 3개, 생성된 PDF 정산서를 확인합니다.

한국어 프롬프트는
[한국어 사이트](https://microsoft.github.io/new-copilot-studio-tech-guide-kr/#scenarios)에서
복사할 수 있습니다.

## 참고

- 모든 데이터는 모의 데이터이며 일련번호, 가격, 확인 번호는 예시입니다.
- Python 스킬은 에이전트 솔루션에 포함되고 게시할 때 등록되므로 수동 업로드가
  필요하지 않습니다.
- `src/`는 두 zip을 `pac solution unpack`으로 푼 결과입니다. 커넥터 코드와
  에이전트 정의를 비교하거나 검토하는 용도이며 직접 가져오지 않습니다.
