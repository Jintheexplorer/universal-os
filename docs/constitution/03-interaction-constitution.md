🏛️ Universal OS Kernel Agent Interaction Specification (v3.1)
문서 유형: 에이전트 인터랙션 헌법 (Interaction Constitution)
적용 대상: Universal OS Memory Manager, Context Provider, Generative UI Core
설계 목표: 파라메트릭 메모리(LLM 내부 지식)의 한계를 극복하고, 토큰 비용 및 레이턴시를 제어하며, 프롬프트 오염을 원천 차단하는 에이전트 거버넌스 구축.

> **이 헌법의 영토**: 에이전트가 어떻게 기억하고, 어떤 권한을 갖고, 어떻게 말하는가.
> SSEP(의미론적 퇴거 정책), SCPM(페르소나 행렬)의 단일 권위 정의 위치.
> SkillOpt GitOps 배포 가드레일의 오너 (트리거 메커니즘은 Workflow G3.3 참조).
> **참조**: `constitution.md` Part I (원칙 오너십 테이블)

---

📜 Guideline 1. 무기억 단기 렌더링 및 계층형 컨텍스트 원칙 (Stateless Short-term Rendering & Tiered Context)

1.1 하드웨어 메모리 아키텍처 모방 (Memory Hierarchies)
커널은 에이전트의 메모리를 절대 단일 통짜 컨텍스트(Flat Window)로 취급하지 않는다. 하드웨어의 L1/L2/L3 캐시 구조를 추상화하여 3개 계층으로 격리 제어한다.

L1 Context (초단기 휘발성 캐시): 현재 실행 중인 실시간 대화 스레드 및 직전 실행 노드의 텔레메트리 데이터. 최대 토큰 제한을 엄격하게 바운딩하여 이중 구역으로 파티셔닝한다 (G1.3 참조).

L2 Context (단기 시맨틱 인덱스): 최근 3일 이내의 실행 로그 요약, 유저의 실시간 상태 토큰, 연관 엔티티 관계도(Graph DB). 정적 커널이 필요 시점에 관련 노드만 쿼리하여 L1에 주입(Hydrate)한다.

L3 Context (장기 저널 저장소): 수개월 전의 원천 불변 데이터 및 백업 로그. 절대 프롬프트에 직접 로드되지 않으며, 벡터 임베딩 기반 검색(Vector RAG) 또는 정적 분석 엔진을 거쳐 고도로 정제된 '추상화된 사실'의 형태로만 상위 레이어로 전달된다.

1.2 무상태형 Grounding (Stateless Grounding — Owner: This Constitution)
확률론적 엔진(LLM)은 자체적인 장기 기억을 가지지 않는 완전한 무상태성(Stateless)을 유지해야 한다. 에이전트가 답변을 생성할 때, 오직 커널이 그 순간 제공한 L1 스냅샷의 물리적 경계 내부 데이터만을 신뢰 기반(Grounding)으로 삼도록 강제한다.

> **Grounding 소스**: Data 헌법이 생성하는 SSOT Snapshot이다. (참조: Data G1.2 및 Governance G3.3)
> **커널 제어권**: 상태 관리의 제어권은 정적 커널이 독점한다. (참조: Workflow G3.1)

컨텍스트 윈도우를 벗어난 데이터에 대해 에이전트가 추측하거나 내부 지식으로 환각을 일으키는 조짐이 보이면, 후처리 가드레일 레이어에서 즉시 세션을 차단한다.

1.3 의미론적 우선순위 기반 퇴거 정책 (Semantic Salience Eviction Policy, SSEP)

1.3.1 L1 내부의 이중 구역 분리 (Dual-Zone Partitioning)
L1 컨텍스트 윈도우는 단일 슬라이딩 버퍼가 아닌, 물리적으로 분리된 두 구역으로 파티셔닝한다.

[Anchor Zone] — 비퇴거 고정 구역 (Non-Evictable Pinned Segment)
OS 메모리 관리의 `mlock()` 시스템 콜에 해당하는 개념. 해당 세그먼트는 퇴거 알고리즘의 대상에서 영구 제외된다.
저장 대상: 유저의 현재 목표(Active Goal), 현재 실행 중인 오류의 근본 원인(Root Cause Stack), 세션 개시 시 선언된 핵심 전제(Session-level Premise).
이 구역의 데이터는 오직 정적 커널이 명시적으로 교체(Replace) 명령을 내릴 때만 갱신된다.

[Sliding Zone] — 의미론적 점수 기반 퇴거 구역 (Salience-Scored Eviction Segment)
현재의 대화 턴(Turn), 중간 과정 로그(Process Log), 단순 확인 응답(Acknowledgment) 등이 위치한다.
만충(Overflow) 시 FIFO가 아닌, 각 토큰 블록에 부여된 의미론적 중요도 점수(Salience Score)를 기준으로 최저점 블록부터 L2로 Flush한다.

1.3.2 의미론적 중요도 스코어링 함수 (Salience Scoring Function)

$$S(block) = \alpha \cdot R(block) + \beta \cdot G(block) + \gamma \cdot C(block)$$

- $R$: 목표 연관성 (Goal Relevance) — 현재 Active Goal 벡터와의 코사인 유사도
- $G$: 인과 의존성 깊이 (Causal Graph Depth) — 해당 블록이 현재 실행 DAG의 몇 단계 선행 노드인가
- $C$: 최근성 감쇠 (Recency Decay) — 시간 $t$ 경과에 따른 지수 감쇠 $e^{-\lambda t}$

가중치 $(\alpha, \beta, \gamma)$는 워크플로우 유형에 따라 정적 커널이 사전 정의한 파라메터 테이블에서 로드한다. LLM이 가중치를 결정하지 않는다.

---

📜 Guideline 2. 권한 비대칭 및 자아 경계 원칙 (Asymmetric Privilege & Ego Boundary)

2.1 제안과 확정의 분리 (Propose vs Commit — Data G2 CQRS의 에이전트 레이어 구현체)

> **이 원칙의 근거**: Data 헌법 G2 CQRS. 이 조항은 그 원칙이 에이전트 인터랙션 레이어에서 어떻게 구현되는가를 정의한다. 중복이 아닌 계층별 구현 분리다.

아무리 정교하게 튜닝된 에이전트라 할지라도, 시스템의 핵심 아이덴티티나 사용자의 근본 가치관을 스스로 수정할 수 없다. 에이전트 인터랙션은 오직 '비대칭적 권한 체계' 위에서만 작동한다.

에이전트 (Inference Runtime): 현재 주어진 상황을 분석하여 자아 상태나 규칙의 변경을 '제안(Proposed Delta)'할 수만 있다.
커널 및 인간 (Kernel & HITL): 이 제안이 전체 시스템의 헌법을 위배하지 않는지 정적 검증기로 검사한 뒤, 오직 인간의 명시적 승인이 있을 때만 이를 '확정(Commit)'하여 상태 기계에 반영한다.

2.2 가상 자아 오염 차단 (Prompt Injection Defenses — LLM Parsing Layer)

> **이 조항의 영토**: LLM 추론 레이어 내부의 파싱 방어. 시스템 경계(외부 API, 입력 채널)에서의 방어는 Governance G2.1이 담당한다. 두 조항은 계층적 방어(Defense in Depth)를 구성한다.

유저의 입력이나 외부 서드파티 API를 통해 유입되는 비정형 텍스트가 에이전트의 시스템 지시어(System Prompt)와 섞이는 것을 커널 레벨에서 격리한다. 샌드위치 가드레일을 적용하여, 외부 입력 데이터는 오직 `[Data Layer]` 태그 데이터 블록 내부에만 위치하도록 강제하며, 해당 블록 내부의 명령형 텍스트는 실행 엔진이 '코드'가 아닌 단순 '리터럴 문자열'로 인식하도록 파싱 콘텍스트를 변환한다.

2.3 FSM 단계 연동 페르소나 행렬 (Stage-Correlated Persona Matrix, SCPM)

2.3.1 페르소나의 일급 설정 객체화 (Persona as First-Class Config)
에이전트의 페르소나(어조, 추론 스타일, 리스크 민감도 임계값)는 GitOps 원칙(G3.1)에 따라 형상 관리되는 독립 설정 파일로 엔지니어링 자산화한다.

```
SKILL.md (메인 지시어)
├── persona.perceive.md  — 정보 수집 단계: 관찰자 모드
├── persona.plan.md      — 계획 수립 단계: 시스템 아키텍트 모드
├── persona.act.md       — 실행 단계: 시스템 엔지니어 모드 (냉정·분리)
└── persona.reflect.md   — 성찰 단계: 철학자 코치 모드 (공감·통찰)
```

2.3.2 결정론적 정적 디스패치 (Deterministic Static Dispatch)
페르소나 교체의 트리거는 오직 FSM의 단계 전이 이벤트다 (→ FSM Event 생성: Workflow G2.1 참조).
LLM이 "지금 어떤 페르소나로 행동해야 하는가"를 스스로 추론하거나 결정하는 것은 완전히 금지된다.

```
FSM State Transition Event 수신 (from: Workflow G2.1)
  ↓ (정적 커널 실행)
from_state → to_state 조합으로 페르소나 파일 조회
시스템 프롬프트 내 [PERSONA_SEGMENT] 구역을 원자적(Atomic) 교체
  ↓
교체된 프롬프트를 무상태형 LLM에 주입 (Hydration)
```

2.3.3 페르소나 전이의 GitOps 관리
페르소나 파일의 수정은 G3.2의 SkillOpt PR Loop와 동일한 승인 파이프라인을 거친다.

---

📜 Guideline 3. 프롬프트 코드화 및 형상 관리 원칙 (Prompt-as-Code via GitOps)

3.1 SKILL.md의 엔지니어링 자산화
에이전트의 행동 지침, 페르소나, 도구 사용 명세는 코드베이스 외부의 텍스트 덩어리로 방치되어서는 안 된다. 모든 프롬프트는 SKILL.md라는 표준 파일로 형상 관리 시스템(Git)에 박제되어야 한다. 시스템의 동작 방식을 바꾼다는 것은 프롬프트를 수정한다는 뜻이며, 이는 곧 Git의 새로운 커밋과 배포 파이프라인(CI/CD)을 통과해야 함을 의미한다.

3.2 SkillOpt GitOps 배포 가드레일 (SkillOpt PR Loop — Owner: This Constitution)

> **역할 분리**: Workflow G3.3은 SkillOpt **트리거 메커니즘**의 오너 ("언제 시작되는가").
> 이 조항(Interaction G3.2)은 SkillOpt **배포 가드레일**의 오너 ("어떤 승인 절차를 거쳐 배포되는가").
> 두 조항은 파이프라인의 앞단/뒷단 분리이며 중복이 아니다.

Workflow G3.3 트리거에 의해 개선 지시어가 도출되면, 자동 수정된 파일이 라이브 커널에 인플레이스로 적용되는 것은 금지된다.

자가 개선 엔진은 반드시 새로운 브랜치를 생성하고 변경 사항에 대한 Pull Request(PR)를 발행해야 한다. 인간 아키텍트(Founder)가 이 Diff를 리뷰하고 병합(Merge)하는 순간, DevOps 배포 파이프라인이 작동하여 커널의 새로운 에이전트 런타임에 안전하게 동적 로드된다.

---

👨‍🏫 핵심 요약

"G1은 에이전트의 뇌 용량을 제어하여 환각과 비용 폭탄을 차단하고,
G2는 에이전트의 권한 경계를 설정하여 자아 오염을 방지하며,
G3는 에이전트의 지능을 코드로 관리하여 예측 가능한 진화를 보장합니다.

이 세 원칙이 Data·Workflow·Governance 헌법과 맞물릴 때,
에이전트는 비로소 '신뢰할 수 있는 지능'이 됩니다."
