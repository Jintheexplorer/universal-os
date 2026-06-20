🏛️ Universal OS Kernel Workflow & Process Architecture Specification (v3.0)
문서 유형: 워크플로우 헌법 (Workflow Constitution)
적용 대상: Universal OS Execution Engine, MCP Kernel Daemon, Agent Runtime
설계 목표: 확률론적 AI 추론 엔진의 자율성을 극대화하되, 시스템 상태 전이의 결정성을 확보하고, 외부 사이드 이펙트를 안전하게 제어하며, 운영 비용을 최적화함.

> **이 헌법의 영토**: 실행이 어떻게 흐르는가, FSM이 어떻게 상태를 통제하는가, 실패 시 어떻게 회복하는가.
> FSM State Transition Event payload의 단일 권위 정의 위치.
> SkillOpt 트리거 메커니즘의 오너 (배포 가드레일은 Interaction G3 참조).
> **참조**: `constitution.md` Part I (원칙 오너십 테이블)

---

📜 Guideline 1. 실행 이원화 아키텍처 및 하이브리드 프로세싱 원칙 (Execution Dualism & Hybrid Processing)

1.1 인지 모드 이원화 라우팅 (Dual-Path Routing: System 1 & System 2)
커널은 인입되는 모든 태스크를 복잡도와 파괴성에 따라 두 가지 실행 경로로 분기하여 계산 자원을 최적화한다.

System 1 (Deterministic Fast-Track): 정형 데이터 가공, 단순 CRUD 연산, 상태 비변경 API 호출 등 규칙 기반으로 확정할 수 있는 마이크로 태스크는 확률론적 AI 엔진을 완전히 우회(Bypass)한다. 커널 내부의 정적 스크립트가 단독 집행하여 밀리초 단위의 레이턴시와 0에 수렴하는 토큰 비용을 구현한다.

System 2 (Probabilistic Deep-Track): 비정형 맥락 분석, 중장기 계획 수립, 다차원 의사결정이 필요한 매크로 워크플로우에 한해 PERCEIVE → PLAN → ACT → REFLECT의 4단계 선형 라이프사이클을 강제 전개한다.

1.2 비정형 노드의 샌드위치 가드레일 (Sandboxed Hybrid Execution)
System 2 트랙의 각 실행 노드는 확률론적 엔진이 단독으로 파일 시스템이나 데이터베이스에 직접 접근하는 것을 원천 차단한다.

전처리 레이어 (Deterministic Pre-processing): 정적 커널이 SSOT 데이터베이스에서 컨텍스트를 안전하게 로드하고 주입 스키마를 1차 검증한다. (CQRS 쓰기 권한 독점 원칙 구현 → Data G2 참조)

추론 레이어 (Probabilistic Inference): 확률론적 엔진이 컨텍스트를 해석하고 판단하여 '제안 페이로드(Proposed Payload)'만을 생성한다.

후처리 레이어 (Deterministic Post-processing): 정적 가드레일이 제안된 아웃풋을 정적 스키마 검증기로 최종 확정한 후 커밋한다. (CQRS 게이트웨이 → Data G2.2 참조)

> **무상태 LLM 원칙**: 확률론적 엔진은 자체 실행 상태를 내부에 보관하지 않는다. (전체 정의 → Interaction G1 참조)

---

📜 Guideline 2. 정적 상태 기계 바운딩 및 하이브리드 복구 원칙 (State-Machine Bounding & Hybrid Resilience)

2.1 FSM 기반의 유향 비순환 그래프 제약 (FSM-Constrained Dynamic DAG)
확률론적 엔진은 목표 달성을 위해 하위 태스크 그래프(DAG)를 동적으로 생성하고 오케스트레이션할 수 있다. 그러나 이 그래프의 노드 실행에 따른 커널의 상위 글로벌 상태 전이는 시스템 커널에 하드코딩된 정적 유한 상태 기계(FSM)의 규칙 바운더리를 절대 초월할 수 없다. 정의되지 않은 상태로의 전이 시도는 즉각 기각(Abort)된다.

FSM State Transition Event Payload (전체 스키마 정의 → Governance G3.2):
```json
{
  "event_id":     "string (UUID v4)",
  "timestamp":    "ISO 8601",
  "from_state":   "perceive | plan | act | reflect",
  "to_state":     "perceive | plan | act | reflect",
  "trigger":      "string (컴포넌트 ID)",
  "session_id":   "string",
  "context_diff": {
    "completed_outputs":  ["string"],
    "pending_exceptions": ["string (ledger_id)"]
  }
}
```

> **타 헌법 연결**: 이 Event를 수신하여 페르소나를 교체하는 것은 Interaction SCPM이다. (참조: Interaction G2.3)
> Governance는 이 Event를 FSM State Transition Log에 기록한다. (참조: Governance G1.2)

2.2 이원적 실패 회복 메커니즘 (Dual-Mode Recovery: Saga vs Forward Recovery)
프로세스 실행 중 비정상적 예외나 가드레일 위반이 발생했을 때, 커널은 사이드 이펙트의 비가역성을 판별하여 복구 전략을 이원화한다.

역방향 가상 롤백 (Distributed Saga Pattern): 내부 데이터베이스 변경 등 가역적이고 멱등성이 보장되는 연산의 실패는 각 노드와 쌍을 이루는 '보상 트랜잭션(Compensating Action)'을 역순으로 트리거하여 시스템을 의미론적 정상 상태로 회복시킨다.

전방향 회복 및 격리 (Forward Recovery via Exception Ledger): 외부 메일 발송, 서드파티 클라우드 동기화 등 비가역적 외부 사이드 이펙트가 발생한 후의 실패는 억지 롤백을 금지하고 즉각 실행을 중지(Halt)한다. 현재의 전황(DAG 상태 및 에러 맥락)을 Exception Ledger에 안전하게 격리한 뒤 (→ Data G3 참조), 인간(HITL)의 개입을 통해 현재 상태에서 '앞으로 전진하는 방향'의 복구 시나리오를 주입받아 프로세스를 재개한다.

---

📜 Guideline 3. 제어권 역전 및 비동기적 메타 가버넌스 원칙 (Inversion of Control & Autonomic Evolution)

3.1 상태 보존형 커널과 무상태형 추론 엔진의 격리 (Stateful Kernel & Stateless LLM)
제어의 주체는 확률론적 엔진이 아니라, 이를 구동하는 결정론적 커널(Python 기반 MCP 코어 시스템)에 있다.

워크플로우의 실행 상태 관리, FSM 가드레일 구동, 프로세스 타임라인 로깅, 그리고 대기 상태 시 메모리 스냅샷을 영속화 스토리지를 향해 직렬화(Serialization) 및 역직렬화(Deserialization)하는 제어권(Inversion of Control)은 정적 커널이 독점한다.

> **무상태 LLM의 전체 정의**: Interaction G1에 있다. 이 조항은 커널 제어권의 오너십만 정의한다.

3.2 위험도 기반 비차단형 인간 인터럽트 (Asynchronous HITL Matrix)
모든 프로세스 액션은 자본 집행 규모 및 시스템 파괴성에 따라 자율성 등급(Level 1~3)이 매겨진다. `hitl: required` 노드에 도달하면, 커널은 메인 실행 스레드를 물리적으로 블로킹하지 않는다. 현재까지의 실행 컨텍스트를 저장하고 해당 프로세스를 '비차단형 대기 유예(Non-blocking Suspension)' 상태로 동결한 뒤 계산 자원을 즉시 반환한다. 이후 인간의 승인 이벤트가 비동기적으로 인입되는 순간 역직렬화하여 프로세스를 재개(Resume)한다.

3.3 폐쇄 루프 기반 자가 스킬 최적화 트리거 (SkillOpt Trigger — Owner: This Constitution)
매크로 루프의 최종 성찰(REFLECT) 단계가 완료되면, 커널은 해당 실행 스레드가 남긴 불변의 실행 로그(transcript.jsonl)를 백그라운드 런타임에서 분석한다. 확률론적 엔진이 실패 패턴이나 비효율을 진단하고 개선 지시어를 도출한다.

> **SkillOpt의 역할 분리**:
> 이 조항(Workflow G3.3)은 SkillOpt **트리거 메커니즘**의 오너다: "언제, 어떤 조건에서 SkillOpt가 시작되는가"
> Interaction G3.2는 SkillOpt **배포 가드레일**의 오너다: "개선된 SKILL.md가 어떤 승인 절차를 거쳐 배포되는가"
> 두 조항은 중복이 아닌 파이프라인의 앞단/뒷단 분리다.

---

👨‍🏫 핵심 요약

"G1은 실행 비용을 최적화하고, G2는 실패를 안전하게 격리하며, G3는 시스템이 스스로 진화할 수 있는 통제된 채널을 제공합니다. 이 세 원칙이 맞물릴 때, 에이전트는 자율적으로 행동하면서도 결코 레일을 이탈하지 않습니다."
