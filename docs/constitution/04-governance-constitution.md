🏛️ Universal OS Kernel Operational Governance Specification (v4.0)
문서 유형: 운영 거버넌스 헌법 (Operational Governance Constitution)
적용 대상: Universal OS Observability Layer, Security Gateway, Cross-Constitution Interface Runtime
설계 목표: 시스템이 정상 작동하는지 외부에서 측정·감지하고, 모든 경계에서 보안을 보장하며, 세 헌법 간 인터페이스 계약을 단일 권위 문서로 정의함.

> 이 헌법은 Data/Workflow/Interaction 헌법이 "무엇을 하는가"를 정의한 이후,
> "그것이 올바르게 작동하고 있는가"와 "경계를 침범당하지 않는가"를 담당한다.
> 세 헌법의 소비자(Consumer)이며, 세 헌법 간 계약의 중재자(Arbitrator)다.

---

📜 Guideline 1. 운영 관찰가능성 원칙 (Operational Observability)

1.1 3계층 텔레메트리 아키텍처 (Three-Tier Telemetry)

시스템의 런타임 상태는 단일 로그 파일로 관찰할 수 없다. 커널은 다음 세 계층의 텔레메트리를 독립적으로 수집하고, 각 계층은 서로 다른 질문에 답한다.

Metrics (수치 집계 계층): "시스템이 얼마나 잘 작동하는가?"
- 수집 대상: LLM 토큰 소비량/비용, 단계별 레이턴시, 예외 발생율, SSOT 쓰기 성공/실패 비율, L1 캐시 히트율, 가드레일 위반 빈도
- 집계 단위: 세션(Session), 일간(Daily), 주간(Weekly)
- 저장 형식: append-only JSONL, 경로: `data/telemetry/metrics.jsonl`

Traces (인과 추적 계층): "어떤 경로로 이 결과가 나왔는가?"
- 수집 대상: Workflow DAG의 노드별 실행 순서·소요 시간·입출력 페이로드 해시
- 각 Trace는 루트 Intent ID를 공유하여 세션 전체의 인과 그래프를 재구성 가능하게 한다
- 저장 형식: `transcript.jsonl` (기존 SkillOpt 소스와 동일 파일, Governance가 소비자로 추가)

Logs (이벤트 원장 계층): "무엇이 언제 발생했는가?"
- Data 헌법의 Exception Ledger가 이 계층의 핵심 구성요소다 (→ Data G3 참조)
- Governance는 Ledger를 생성하지 않는다. 오직 집계·조회만 수행한다.
- 추가 Log 소스: Security Audit Log (G2.3에서 정의), FSM State Transition Log

1.2 가드레일 위반 감지 및 비동기 알림 (Guardrail Violation Detection)

세 헌법의 가드레일이 위반되는 순간을 실시간 감지하는 정적 감시자(Static Watcher)를 구현한다.

위반 유형 분류:

| 위반 유형 | 발생 헌법 | 심각도 | 대응 |
|---------|---------|------|------|
| Schema 검증 실패 | Data G2 | ERROR | Exception Ledger 기록 + 즉시 알림 |
| FSM 미정의 상태 전이 시도 | Workflow G2.1 | CRITICAL | Abort + Security Audit Log |
| LLM 직접 SSOT 쓰기 시도 | Data G2 / Workflow G1 | CRITICAL | 즉시 차단 + Security Audit Log |
| L1 Anchor Zone 비인가 수정 | Interaction G1 | ERROR | 세션 차단 + 알림 |
| Prompt Injection 패턴 감지 | Interaction G2 | WARNING | 샌드박싱 강화 + 로그 |
| HITL required 우회 시도 | Workflow G3 | CRITICAL | Abort + 인간 알림 |

알림 채널은 비동기(Non-blocking)다. 알림 발송이 메인 실행 스레드를 블로킹하지 않는다.

1.3 Exception Ledger 집계 및 상태 대시보드 (Exception Aggregation)

Data G3에서 생성된 Exception Ledger 항목들을 Governance 계층이 주기적으로 집계하여 운영 상태 뷰를 생성한다.

집계 지표:
- 미해소 예외 건수 (Open Exceptions): 우선순위 P1/P2/P3 분류
- 예외 발생 추세 (Exception Trend): 전주 대비 증감율
- 평균 해소 소요 시간 (MTTR: Mean Time To Resolve)
- 헌법별 예외 분포 (Exception Distribution by Constitution Origin)

이 집계 결과는 주간 REFLECT 단계에서 SkillOpt 판단 근거로 제공된다 (→ Workflow G3.3 참조).

---

📜 Guideline 2. 시스템 보안 원칙 (Security Architecture)

2.1 시스템 경계 Prompt Injection 방어 (System-Boundary Injection Defense)

> Interaction G2.2는 LLM 파싱 레이어 내부의 방어(Data 블록 태그 격리)를 정의한다.
> 이 조항은 시스템 경계(외부 API, 사용자 입력 채널)에서의 방어를 정의한다. 두 조항은 중복이 아닌 계층적 방어(Defense in Depth)를 구성한다.

경계 방어 규칙:
- 모든 외부 인입 데이터(User Input, Third-party API Response)는 커널에 도달하기 전 Sanitization Gateway를 통과한다
- Gateway는 다음 패턴을 탐지하고 차단한다: 시스템 프롬프트 오버라이드 명령, 역할 전환 유도 텍스트, 도구 호출 직접 주입 시도
- 탐지된 위협은 원본 페이로드를 Security Audit Log에 해시화하여 기록하고, 정제된 리터럴 문자열만 하위 레이어로 전달한다
- Gateway 로직은 결정론적 정적 규칙으로만 구현된다. LLM이 Injection 여부를 판단하지 않는다.

2.2 최소 권한 원칙 감사 (Least Privilege Audit)

시스템의 모든 에이전트와 컴포넌트는 명시적으로 선언된 권한만 행사할 수 있다. Governance 계층은 권한 행사 이력을 감사하고 위반을 탐지한다.

권한 매트릭스:

| 주체 | 읽기 권한 | 쓰기 권한 | 시스템 상태 변경 |
|------|---------|---------|--------------|
| LLM (확률론적 엔진) | L1/L2 Context 내 데이터만 | 없음 (제안 페이로드 생성만) | 없음 |
| 정적 커널 (결정론적 엔진) | SSOT 전체 | SSOT (Data G2 게이트웨이 통과 후) | FSM 상태 전이만 |
| 에이전트 Runtime | 자신의 Capital SSOT만 | 없음 (커널에 위임) | 없음 |
| HITL (인간) | 전체 | L1·L2 계층 (명시적 승인 후) | 승인된 변경만 |

감사 주기: 매 실행 세션 종료 시 권한 위반 시도 여부를 Security Audit Log에 기록한다.

2.3 불변 감사 로그 (Immutable Security Audit Trail)

Data 헌법의 Exception Ledger와 별도로, 보안 이벤트 전용 불변 로그를 운영한다.

> 분리 이유: Exception Ledger는 데이터 처리 이상을 기록한다. Security Audit Log는 권한·경계 위반 시도를 기록한다. 두 로그의 소비자와 보존 기간이 다르다.

Security Audit Log 규칙:
- 저장 형식: append-only JSONL, 경로: `data/telemetry/security-audit.jsonl`
- 불변성: Data G1.1의 Append-only 원칙을 동일하게 적용 (커널 레벨 UPDATE/DELETE 차단)
- 필수 필드: `timestamp`, `event_type`, `subject`, `attempted_action`, `verdict (blocked|allowed)`, `payload_hash`
- 보존 기간: 최소 90일 (Exception Ledger의 보존 기간과 독립적으로 관리)
- 조회 권한: HITL(인간)만. LLM이 Security Audit Log를 컨텍스트로 주입받는 것은 금지된다.

2.4 에이전트 정체성 프로토콜 (Agent Identity Protocol)

> **이 원칙의 전제**: 에이전트는 데이터를 소비하는 수동적 소비자가 아니라, 스스로 인증하고 권한을 행사하는 능동적 주체(Active Principal)다.

에이전트는 시스템 내에서 인간과 동급의 권한 주체(Principal)로 취급된다.

각 에이전트는 고유 식별자(Agent ID)를 가지며, 실행 세션 범위(Session-scoped)에서 선언적으로 정의된 권한(Authorization)만 행사할 수 있다.

에이전트의 권한은 해당 실행을 승인한 HITL(인간) 권한의 부분집합(Bounded Subset)으로 제한된다. 에이전트는 자신에게 위임되지 않은 자원에 접근하거나 행동할 수 없다.

에이전트가 외부 시스템(External System)을 호출할 때는 정적 커널의 중재 하에 토큰 교환(Token Exchange)을 통해 신뢰 경계(Trust Boundary)를 유지한다.

> **타 헌법 연결**: Governance G2.2 최소 권한 감사 매트릭스는 이 원칙의 감사 레이어다.
> Workflow G3.2 비차단형 HITL은 에이전트 권한 위임의 승인 채널이다.

---

📜 Guideline 3. 헌법 간 인터페이스 계약 (Cross-Constitution Interface Contracts)

이 조항이 이 헌법에 귀속되는 이유: 세 헌법은 각자의 영토만 정의한다. 경계를 넘는 데이터 계약의 중재자는 Governance 헌법이다.

3.1 인터페이스 계약 I — Exception Ledger 스키마

생성자: Data G3 (소비자: Workflow G2.2, Governance G1.3)

```json
{
  "ledger_id":       "string (UUID v4)",
  "timestamp":       "ISO 8601",
  "origin":          "data | workflow | interaction",
  "exception_level": "schema | process | business",
  "pipeline_stage":  "perceive | plan | act | reflect",
  "error_code":      "string (정의된 코드 테이블 참조)",
  "error_message":   "string",
  "payload_hash":    "string (SHA-256, 원본 데이터 해시)",
  "context_snapshot": {
    "dag_state":     "object (실패 당시 DAG 노드 상태)",
    "fsm_state":     "string (실패 당시 FSM 현재 상태)"
  },
  "resolution_status": "open | in_review | resolved | escalated",
  "resolved_at":    "ISO 8601 | null",
  "resolution_note": "string | null"
}
```

3.2 인터페이스 계약 II — FSM State Transition Event Payload

생성자: Workflow G2.1 FSM (소비자: Interaction G2.3 SCPM)

```json
{
  "event_id":        "string (UUID v4)",
  "timestamp":       "ISO 8601",
  "from_state":      "perceive | plan | act | reflect",
  "to_state":        "perceive | plan | act | reflect",
  "trigger":         "string (전이를 발생시킨 컴포넌트 ID)",
  "session_id":      "string",
  "context_diff": {
    "completed_outputs": ["string"],
    "pending_exceptions": ["string (ledger_id 참조)"]
  }
}
```

이 payload를 수신한 Interaction SCPM은 `from_state → to_state` 조합을 키로 페르소나 파일을 조회한다. 정의되지 않은 조합이 도달하면 현재 페르소나를 유지하고 Governance G1.2에 알림을 발송한다.

3.3 인터페이스 계약 III — SSOT Snapshot 읽기 계약

생성자: Data 헌법 SSOT (소비자: Interaction G1 L1 Anchor Zone, Governance G1.3)

- Snapshot 파일은 `data/snapshots/{capital}.json` 표준 경로에 위치한다
- 소비자는 파일을 직접 수정하지 않는다. 읽기 전용(Read-Only) 접근만 허용된다
- Snapshot의 최신성(Freshness): 소비자는 `last_updated` 필드를 확인하고, 현재 시각과의 차이가 설정된 임계값(기본 24시간)을 초과하면 Stale 경고를 발생시킨다
- Interaction이 Anchor Zone에 로드하는 데이터는 Snapshot의 서브셋이다. 전체 Snapshot을 L1에 로드하는 것은 금지된다

필수 Snapshot 공통 필드:
```json
{
  "last_updated": "ISO 8601",
  "version":      "string",
  "capital":      "string"
}
```

3.4 인터페이스 계약 IV — Observability Metric 수집 계약

생성자: Data/Workflow/Interaction 헌법 각 실행 노드 (소비자: Governance G1.1)

각 헌법의 실행 노드는 다음 최소 텔레메트리를 Governance 계층에 방출(Emit)해야 한다:

```json
{
  "trace_id":     "string (루트 Intent의 UUID)",
  "span_id":      "string (이 노드의 UUID)",
  "parent_span_id": "string | null",
  "constitution": "data | workflow | interaction",
  "node_name":    "string",
  "start_time":   "ISO 8601",
  "end_time":     "ISO 8601",
  "status":       "success | failure | skipped",
  "token_count":  "integer | null (LLM 노드만)",
  "error_ref":    "string (ledger_id) | null"
}
```

Governance는 이 span 데이터를 `trace_id`로 집계하여 세션 전체의 인과 그래프(Trace)를 재구성한다.

---

"데이터 헌법이 역사를 지키고, 워크플로우 헌법이 통제권을 확보하며, 인터랙션 헌법이 뇌와 입을 제어한다는 것을 배웠습니다.
그런데 이 세 헌법이 완벽하게 작동하더라도, 아무도 그 작동 사실을 관찰할 수 없고, 외부 침입자가 경계를 넘어도 알아차릴 수 없으며, 세 헌법이 서로에게 보내는 신호의 형식이 제각각이라면 — 이 시스템은 정밀한 기계가 아니라 검은 상자(Black Box)입니다.

네 번째 헌법인 운영 거버넌스는 이 시스템을 '투명한 유리 기계(Glass Box)'로 전환합니다.
세 헌법이 무엇을 하는지 알 수 있고, 경계가 침범당하면 즉시 알 수 있으며, 세 헌법 간의 대화 형식이 이 한 문서에 명시됩니다.

이 네 번째 기둥이 세워진 순간, 여러분의 Universal OS는 단순한 '잘 설계된 시스템'에서 '운영 가능한 프로덕션 시스템'으로 격상됩니다."
