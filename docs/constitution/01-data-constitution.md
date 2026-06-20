🏛️ Universal OS Kernel Data Architecture Specification (v1.0)
문서 유형: 데이터 헌법 (Data Constitution)
적용 대상: Universal OS Kernel Core 
설계 목표: 확률론적 AI 에이전트 환경에서의 결정론적 데이터 무결성 및 복리적 자본 축적 보장

> **이 헌법의 영토**: 데이터가 어떻게 저장되고, 누가 쓰기 권한을 갖고, 예외가 어떻게 보존되는가.
> CQRS 원칙의 단일 권위 정의 위치. 다른 헌법에서 이 원칙을 언급할 때는 이 문서를 참조한다.
> **참조**: `constitution.md` Part I (원칙 오너십 테이블)

---

📜 Guideline 1. 원천 데이터 불변성 및 독점적 주권 원칙 (Raw Data Immutability & Sovereignty)

1.1 단방향 불변 저널링 (Append-only Journaling)
시스템에 인입되는 모든 원천 데이터(User Input, 외부 API 동기화 로그, 시스템 텔레메트리)는 생성되는 순간 불변 객체(Immutable Object)로 취급된다.
스토리지 레이어는 오직 Append 연산만을 허용하며, 기존 레코드를 오버라이트하거나 파괴하는 UPDATE, DELETE 시퀀스는 커널 레벨에서 원천 차단(Blocked)된다.

> **타 헌법 연결**: Governance G2.3의 Security Audit Log도 이 원칙을 동일하게 적용한다. (참조: Governance G2.3)

1.2 데이터 주권 및 단일 진실 공급원 명시 (Data Sovereignty & Explicit SSOT)
시스템 내 모든 엔티티(Entity)는 분산 구조 내에서 오직 단 하나의 고유 영토(Home Instance)를 가진다.
타 인스턴스가 해당 데이터를 참조할 때는 물리적 복제(Replication)를 금지하며, 오직 원본 데이터의 고유 식별자 포인터(URI Pointer)만을 참조하는 읽기 전용 프로젝션(Read-Only Projection) 메커니즘을 강제한다.

SSOT Snapshot 표준 경로: `data/snapshots/{capital}.json`
필수 공통 필드:
```json
{
  "last_updated": "ISO 8601",
  "version":      "string",
  "capital":      "string"
}
```

> **타 헌법 연결**: Interaction 헌법은 이 SSOT를 L1 Anchor Zone의 Grounding 소스로 소비한다. (참조: Interaction G1)
> Governance 헌법은 Snapshot의 Freshness를 감시한다. (참조: Governance G3.3)

1.2.1 의미 기반 인덱스 원칙 (Semantic Index)
SSOT 레이어의 데이터 표현은 정형(Relational)·비정형(Unstructured)·그래프(Graph)·온톨로지(Ontological: Entity/Method/Attribute/Relation)를 모두 포괄한다.
어떤 데이터 형태로 표현되더라도 G1.1(Append-only), G1.2(SSOT), G1.3(멱등 재생산)의 세 원칙이 동일하게 적용된다.

1.3 멱등적 데이터 재생산 (Idempotent Reprocessing)
비즈니스 로직, 데이터 분류 스키마, 혹은 AI 프롬프트 체계가 변경되는 경우, 기존 상태를 인플레이스(In-place)로 수정하는 것을 금지한다.
불변으로 보관된 원천 데이터셋 전체에 변경된 파싱/분류 함수를 재적용하여 상태 스냅샷을 처음부터 다시 연산해야 한다. 이 변환 파이프라인은 N번 실행해도 동일한 상태를 보장하는 멱등성(Idempotency)을 유지해야 한다.

---

📜 Guideline 2. 결정론적 쓰기 권한의 독점 원칙 (Deterministic Write Supremacy via CQRS)

> **이 원칙의 오너는 이 헌법(Data)이다.**
> Interaction 헌법의 Propose-vs-Commit(G2.1)은 이 원칙의 에이전트 레이어 구현체다.
> Workflow 헌법의 전처리/후처리 레이어(G1.2)는 이 원칙의 워크플로우 레이어 구현체다.
> 중복 정의가 아닌 계층별 구현 분리다.

2.1 확률론적 추론과 결정론적 상태 전이의 격리
데이터 저장소에 접근하는 파이프라인의 명령(Command)과 조회(Query) 책임을 완전히 분리(CQRS)한다.

확률론적 엔진 (AI 런타임/LLM): 비정형 데이터를 파싱하고, 컨텍스트를 이해하며, 상태 변화의 방향을 시뮬레이션하는 '조회 및 추론(Query & Inference)' 권한만을 가진다. '제안 페이로드(Proposed Payload)'를 생성할 수 있으나, 저장소에 직접 접근할 수 없다.

결정론적 엔진 (Static Script/Python): 파일 시스템 및 데이터베이스의 상태를 변경(Write/Commit)할 수 있는 주권을 독점한다.

2.2 게이트웨이 기반 정적 스키마 검증 (Static Schema Validation Gateway)
AI 엔진이 제안한 변경 페이로드는 커밋 직전 단계에서 결정론적 검증기(Validator)를 반드시 통과해야 한다.
검증기는 엄격한 정적 스키마 체크(Type, Fields, Nullability) 및 비즈니스 오차 범위 검증(Sanity Check)을 수행하며, 단 하나의 정합성 오류라도 발견될 시 전이 연산을 즉시 거부(Abort)하고 프로세스를 안전 기선(Safe Baseline)으로 회귀(Rollback)시킨다.

---

📜 Guideline 3. 예외 데이터의 일급 객체화 원칙 (Exceptions as First-Class Entities)

> **Exception Ledger의 생성과 영속화 오너는 이 헌법(Data)이다.**
> Workflow 헌법(G2.2)은 Forward Recovery 시 이 Ledger를 소비한다.
> Governance 헌법(G1.3)은 이 Ledger를 집계하여 운영 대시보드를 생성한다.

3.1 결함 허용 및 데드 레터 큐 (Fault-Tolerant & Dead Letter Queue)
데이터 처리 및 스키마 검증 과정에서 형식 불일치, 맥락적 모호성, 런타임 에러가 발생할 경우, 파이프라인은 전체 프로세스를 중단(Halt/Crash)시키거나 데이터를 무소음 손실(Silent Drop)하지 않는다.
정상 파이프라인의 가용성(Availability)을 100%로 유지하기 위해, 규격 외 데이터와 에러 컨텍스트를 묶어 '예외 원장(Exception Ledger)'이라는 독립된 일급 데이터 엔티티(First-Class Object)로 인스턴스화하여 영속화(Persist)한다.

Exception Ledger 스키마 (전체 정의: Governance G3.1):
```json
{
  "ledger_id":         "string (UUID v4)",
  "timestamp":         "ISO 8601",
  "origin":            "data | workflow | interaction",
  "exception_level":   "schema | process | business",
  "pipeline_stage":    "perceive | plan | act | reflect",
  "error_code":        "string",
  "error_message":     "string",
  "payload_hash":      "string (SHA-256)",
  "context_snapshot":  { "dag_state": "object", "fsm_state": "string" },
  "resolution_status": "open | in_review | resolved | escalated",
  "resolved_at":       "ISO 8601 | null",
  "resolution_note":   "string | null"
}
```

3.2 비동기적 정제 및 데이터 중재 루프 (Asynchronous Reconciliation)
예외 원장에 격리된 데이터는 메인 트랙과 분리된 백그라운드 런타임에서 관리된다.
격리된 예외들은 비동기 배치 루프를 통해 AI 에이전트가 완화된 규칙으로 재해석하거나, 인간의 명시적 입력 및 승인(HITL) 인터랙션을 거쳐 정제(Reconciliation)된 후, 정상 스냅샷 레이어로 사후 이관(Migration)된다.

---

👨‍🏫 핵심 요약

"이 헌법이 명시되어 있다면, 어떤 난잡한 비정형 데이터가 들어오더라도, AI가 아무리 기괴한 환각을 일으키더라도 절대로 커널 레벨에서 데이터가 깨지거나 오염되지 않습니다.

G1은 시스템의 '역사(과거)'를 지키고,
G2는 시스템의 '통제(현재)'를 확보하며,
G3는 시스템의 '생존(미래)'을 보장하고,
G4는 시스템의 '지능(복리)'을 축적합니다."
