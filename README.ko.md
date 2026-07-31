## 에이전트 오케스트레이션 · 결정론적 게이트 · AI 활용 개발

[English](README.md) · **한국어**

코드 기반 자동화와 에이전트 오케스트레이션 시스템: 권한이 기본 차단(fail-closed)인 에이전트
플랫폼, 평가 게이트가 달린 로컬 검색 파이프라인, 그리고 시드만 있으면 비트 단위로 동일하게
재생되는 결정론적 시뮬레이션.

이 페이지의 모든 수치는 추정이 아니라 측정값입니다. 수치를 만든 명령이 해당 저장소 안에 있고,
저장소가 발행 전에 그 수치를 다시 검산합니다.

---

## 대표 작업

| 프로젝트 | 무엇인가 | 측정된 사실 | 라이브 |
|---|---|---|---|
| **[baton](https://github.com/euuuuuuan/baton-public)** | 제한된 에이전트 작업을 위한 로컬 우선 오케스트레이션 플랫폼: fail-closed 권한 게이트 뒤의 Swift macOS 제어 데몬을 TypeScript 오케스트레이터가 MCP로 구동. *(플랫폼)* | `docs/TOOL_SURFACE.json`에 문서화된 **MCP 도구 63개**, **테스트·스펙 파일 234개**, **ADR 15건**, 그리고 승인이 지문에 묶여 **1회만 사용되는** 원격 승인 브로커(ADR-003). | — |
| **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** | 망분리 환경을 전제로 만든 한국어 RAG: 표준 라이브러리만으로 하이브리드 검색, PII 마스킹, 범위 밖 질문 거절 게이트. *(도구)* | **외부 의존성 0**, 빌드가 **50문항 골든셋**으로 게이트됨: hit@3 **100 %**(39/39), 거절 정밀도 **100 %** / 재현율 **81.8 %** — 그리고 약점인 인용 정확도 **36.75 %**도 숨기지 않고 그대로 게시. | — |
| **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** | 라이프사이클/CRM 마케터의 SQL 업무를 MCP 에이전트가 대신하되, 에이전트가 어디까지 갈 수 있고 무엇이 기록되는지를 거버넌스 커널이 결정. *(도구)* | **테스트 108개 통과**(MCP SDK 설치 시), 그중 커널을 겨냥한 **PII 레드팀 33개 + 거버넌스 레드팀 12개** 포함. 데이터는 **100 % 합성**이며 바이트 단위 재현: 시드 42 → 동일한 SQLite 다이제스트, 고객 5,000명 / 이벤트 99,922건. | — |
| **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** | 브라우저에서 도는 전술 진입 계획 + 실시간 분대 시뮬레이션; 클리어한 판이 URL로 공유되어 상대 브라우저에서 그대로 재시뮬레이션됨. *(플레이 가능한 빌드, 상업 출시 아님)* | **30 Hz 고정 틱 시뮬레이션** + 시뮬·콘텐츠 패키지에서 **비결정론 API 11종을 금지**하는 순수성 게이트 — 이것이 통과하기에 시드 + 입력 로그가 동일하게 재생됩니다. **테스트·스펙 파일 55개**, `tools/` 스크립트 40개 중 35개가 헤드리스 하니스(나머지 5개는 에셋 페처와 코드젠). | **[플레이](https://fatal-funnel.vercel.app)** |
| **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** | 결정론적 추론 장치: 범위 고정, 최저비용 반증 단계 계획, 맹점 스윕, 실행 증거 없는 주장을 기각하는 판정 게이트를 강제하는 MCP 도구 8개. *(도구)* | **런타임 의존성 0**, **테스트 33/33 통과** — 그중 하나가 보증 그 자체: 전체 표면이 파일 쓰기, 프로세스 생성, 네트워크 호출을 일절 하지 않음을 테스트가 강제. | — |

---

## 어떻게 만들었나

핵심은 "AI로 만들었다"가 아니라 **모델과 저장소 사이에 무엇을 두는가**입니다.

- **코딩 에이전트 둘, 심판은 결정론적 게이트 하나.** [`agent-relay`](https://github.com/euuuuuuan/agent-relay-public)는
  두 에이전트 CLI를 게이트가 통과할 때까지 맞붙이는 외부 드라이버입니다. "완료" 판정은 게이트가
  내립니다 — 에이전트가 자기 작업을 스스로 평가한 의견은 절대 아닙니다. 단위 테스트 12개는
  `PATH`를 시스템 디렉토리로 줄인 채 통과하므로, 두 에이전트가 없어도 드라이버의 거절 동작이
  증명됩니다.
- **결정론적 게이트가 모델 호출보다 먼저 돕니다.** 감이 아니라 순수성 검사기:
  fatal-funnel의 시뮬 게이트는 비결정론 API 11종을 금지하고, reasonforge의 읽기 전용 테스트는
  금지 패턴 6종을 강제합니다. 검색 품질도 빌드 게이트입니다 — 골든셋과 임계값이 회귀 시 빌드를
  실패시킵니다([rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)).
- **UI가 "된다"는 말은 기억이 아니라 프로브와 스크린샷에서 나옵니다.** [hollowmere](https://github.com/euuuuuuan/hollowmere-public)는
  `shot`·`relic-qa`·`mobile-qa` 하니스를 싣고, fatal-funnel은 `tools/` 40개 중 35개가 헤드리스
  하니스에 고정 스크린샷 카메라 4대입니다. 비싸게 배운 교훈입니다: "되는" 툴팁이 입력 필터 플래그
  하나 뒤에서 죽어 있을 수 있고, 그것은 합성 입력 프로브가 잡지 코드 읽기로는 못 잡습니다.
- **에셋은 생성되기 전에 라이선스 게이트를 통과합니다.** [assetforge](https://github.com/euuuuuuan/assetforge-public)는
  레지스트리에 없는 모델로는 생성 자체를 거부합니다. 하류에서는 **공개 스냅샷 27개 전부가
  4단계 재배포 등급표를 가진 `CREDITS.md`**를 싣습니다 — 포크하는 사람이 무엇을 제거해야 하는지
  정확히 알 수 있게.
- **정직한 라벨을 스키마로 강제합니다.** [euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)에서는
  게시되는 모든 지표가 `kind: z.enum(['measured', 'estimated'])` 타입입니다. 측정인지 추정인지
  선언하지 않으면 숫자를 게시할 수 없습니다. 이 페이지에 어림수 마케팅 수치가 없는 이유입니다.
- **발행은 복붙이 아니라 파이프라인입니다.** 여기의 모든 공개 저장소는 반복 가능한 새니타이징
  발행으로 생성되며, 클레임 파일이 README의 모든 수치를 재실행해 하나라도 어긋나면 커밋 생성을
  거부합니다. 스냅샷 27개에 걸쳐 **테스트·스펙 파일 629개**입니다.

---

## 선별 작업

27개 전부가 아니라, 구별되는 능력을 보여주는 것들만.

**브라우저 게임 (링크 열면 바로 플레이)**
- **[fatal-funnel](https://github.com/euuuuuuan/fatal-funnel-public)** — 결정론적 전술 시뮬, 총기 40종, 수제 미션 12개 · [플레이](https://fatal-funnel.vercel.app)
- **[hollowmere](https://github.com/euuuuuuan/hollowmere-public)** — 컴팩트 액션 RPG: 패링, 유물, 2개 지역. *v0.5.1 수직 슬라이스* · [플레이](https://hollowmere-two.vercel.app)
- **[neko-shift](https://github.com/euuuuuuan/neko-shift-public)** — 인력 배치 비율이 소재인 작고 완결된 방치형, 테스트 파일 18개 · [플레이](https://neko-shift.vercel.app)
- **[ashglass-reliquary](https://github.com/euuuuuuan/ashglass-reliquary-public)** — 아이소메트릭 ARPG 슬라이스. **hollowmere와 결정론적 시뮬레이션 코어를 공유**하되 전투 규칙·인카운터·콘텐츠는 독자적 · [플레이](https://ashglass-reliquary.vercel.app)
- **[moonshard-warden](https://github.com/euuuuuuan/moonshard-warden-public)** — 3연속 웨이브 아이소메트릭 아레나: 예고 동작을 읽고, 문컷을 연계하고, 확정된 공격을 대시로 통과. **테스트 파일 43개**에 이 세트에서 가장 엄격한 에셋 원장 — 파일별 SHA-256 100행, 전부 CC0 · [플레이](https://moonshard-warden.vercel.app)

**엔진 게임 (Godot / Unity — 수직 슬라이스·프로토타입, 상업 출시 없음)**
- **[voidclad](https://github.com/euuuuuuan/voidclad-public)** — 우주 전함 포격전: 포탄 비행 시간, 이동 예측 조준, 장갑 입사각 관통 판정을 결정론적 30 Hz 시뮬 위에서 (테스트 파일 27개)
- **[cairnfall](https://github.com/euuuuuuan/cairnfall-public)** — 솔로 보스 레이드 ARPG, 3페이즈 보스 10종; "레이드가 당신을 캐리하지 않는다"는 강제 불변식이 아니라 전투 후 측정되는 통계 — 아군이 막타를 치면 리포트에 그렇게 적힘 (테스트 파일 33개)
- **[todak](https://github.com/euuuuuuan/todak-public)** — 화면 아래쪽에 사는 데스크톱 픽셀 펫 (테스트 파일 36개)
- **[hordecaller](https://github.com/euuuuuuan/hordecaller-public)** (30) · **[ragtail](https://github.com/euuuuuuan/ragtail-public)** (29) · **[driftfolk](https://github.com/euuuuuuan/driftfolk-public)** + **[Unity 이식판](https://github.com/euuuuuuan/driftfolk-unity-public)** — 이식판의 핵심은 원본과의 수치 동일성 · **[gatewarden](https://github.com/euuuuuuan/gatewarden-public)** · **[emberline](https://github.com/euuuuuuan/emberline-public)**
- **[tidewrack](https://github.com/euuuuuuan/tidewrack-public)** — 지휘형 배틀로얄, Unity. 따로 적는 이유: 이 스냅샷은 **플레이 가능한 슬라이스가 아니라 검증 골격**입니다 — 결정론적 시뮬과 골든 파일 하니스가 실린 부분이고, 번호 붙은 소크 어서션 15개 중 11개가 아직 대기 중.

**AI·에이전트 도구**
- **[baton](https://github.com/euuuuuuan/baton-public)** — 에이전트 플랫폼: 63도구 MCP 표면, 승인 브로커, 오케스트레이터 패키지 16개, Swift 소스 90개
- **[reasonforge](https://github.com/euuuuuuan/reasonforge-public)** — 읽기 전용 추론 하니스, MCP 도구 8개, 의존성 0
- **[agent-relay](https://github.com/euuuuuuan/agent-relay-public)** — 두 코딩 에이전트 사이의 게이트 종결 루프
- **[assetforge](https://github.com/euuuuuuan/assetforge-public)** — fail-closed 라이선스 게이트 뒤의 로컬 무료 에셋 생성
- **[content-qa-pipeline](https://github.com/euuuuuuan/content-qa-pipeline-public)** — 생성 → 심사 → 조판 → 발행, "확신 없음"은 내보내지 않고 보류

**데이터·백엔드·도메인 도구**
- **[officegrid](https://github.com/euuuuuuan/officegrid-public)** — 테넌트 경계가 PostgreSQL 안에 있는 멀티테넌트 자산 관리: 마이그레이션 11개에 걸친 **행 수준 보안(RLS) 정책 82개**와 이를 겨냥한 pgTAP 테스트 6파일 — 애플리케이션 레이어 필터링이 아님
- **[marketing-knowledge-rag](https://github.com/euuuuuuan/marketing-knowledge-rag-public)** · **[rag-eval-demo](https://github.com/euuuuuuan/rag-eval-demo-public)** — 평가 게이트 자체가 핵심인 검색
- **[crm-mcp-agent](https://github.com/euuuuuuan/crm-mcp-agent-public)** — 전량 합성 CDP 위의 에이전트 + 거버넌스 커널
- **[ad-compliance-linter](https://github.com/euuuuuuan/ad-compliance-linter-public)** — 한국 금융·보험 광고 문구의 1차 필터. 실리는 규칙셋 5개 전부 데이터 자체에 `"verified": false`로 표기되고, 리포트는 면책 문구 없이 출력되지 않음: 사람 검토를 대체하지 않고 검토 대상을 좁히는 용도
- **[loopsmith](https://github.com/euuuuuuan/loopsmith-public)** — 악기를 연주하거나 패턴을 쓰거나; 같은 곡을 양쪽에서 무손실로 편집 (테스트 파일 27개)
- **[euan-portfolio](https://github.com/euuuuuuan/euan-portfolio-public)** — 모든 수치가 측정/추정을 선언하는 2개 언어 정적 사이트 · [라이브](https://euuuuuuan.pages.dev)

---

## 이 저장소들에 대해

27개 전부 새니타이즈된 스냅샷입니다: 코드는 Apache-2.0, 에셋은 저장소별 4단계 `CREDITS.md`가
관리합니다. 게임은 개인 빌드와 수직 슬라이스입니다 — 플레이 가능하고, 게이트로 검증되고,
정직하게 라벨링됐지만 상업 출시물은 아닙니다.

**연락:** 이곳 아무 저장소의 이슈나 디스커션으로 주세요.
