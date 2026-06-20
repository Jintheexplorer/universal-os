# 🏛️ Universal OS Kernel Constitution (Integrated v4.0)

문서 유형: 통합 커널 헌법 (Master Constitution)
적용 대상: Universal OS Kernel Core 
작성 기준: Data v1.0 · Workflow v3.0 · Interaction v3.1 · Governance v4.0

> 이 문서는 네 개별 헌법의 내용을 복제하지 않는다.
> 네 헌법이 어떻게 연결되는지, 각 원칙의 오너가 어디인지, 수정할 때 어떤 파급 효과가 있는지를 정의한다.
> 개별 헌법을 읽기 전에 이 문서를 먼저 읽어라.

---

## 서문: 네 헌법의 존재 이유

AI-Native OS는 네 가지 서로 다른 실패 모드를 가진다:

| 실패 모드 | 원인 | 이를 방지하는 헌법 |
|---------|------|----------------|
| 데이터 오염 | 원본이 수정되거나, 진실의 원천이 여럿이 되거나, 재현 불가능한 상태 전이 | **Data 헌법** |
| 실행 이탈 | 에이전트가 정의되지 않은 행동을 취하거나, 비가역적 실패 후 무한 재시도 | **Workflow 헌법** |
| 인식 오염 | 에이전트가 환각을 사실로 말하거나, 스스로 자아를 수정하거나, 맥락 폭발로 비용이 무한증가 | **Interaction 헌법** |
| 불투명한 운영 | 무엇이 잘못되었는지 알 수 없거나, 외부 침입을 감지 못하거나, 헌법 간 신호가 불일치 | **Governance 헌법** |

네 헌법은 각각 하나의 실패 모드를 봉쇄하는 전문 방어선이다. 하나라도 없으면 해당 실패 모드는 통제 불가능해진다.

$$\text{AI-Native OS} = \text{Data(과거+복리)} + \text{Workflow(현재)} + \text{Interaction(미래)} + \text{Governance(투명성)}$$

---

## 헌법 간 의존성 다이어그램

```
┌─────────────────────────────────────────────────────────────────┐
│                    GOVERNANCE CONSTITUTION                       │
│   (관찰·보안·인터페이스 계약 — 나머지 세 헌법을 위에서 감시)          │
│                                                                   │
│  Consumes: Exception Ledger (Data) · FSM Events (Workflow)       │
│            SSOT Snapshots (Data) · Telemetry spans (All)         │
└──────────┬────────────────┬───────────────────┬──────────────────┘
           │ observes       │ observes           │ observes
           ▼                ▼                    ▼
┌──────────────┐  ┌──────────────────┐  ┌──────────────────────┐
│     DATA     │  │    WORKFLOW      │  │    INTERACTION       │
│ CONSTITUTION │  │  CONSTITUTION    │  │   CONSTITUTION       │
│              │  │                  │  │                      │
│ 무엇이 저장   │  │ 어떻게 실행되는가  │  │ 에이전트가 어떻게      │
│ 되는가?      │  │                  │  │ 생각하고 말하는가?    │
│              │  │                  │  │                      │
│ Produces:    │  │ Consumes:        │  │ Consumes:            │
│ SSOT →──────────────────────────────▶ L1 Anchor (read-only) │
│ Exception ──▶│  │ Exception Ledger │  │                      │
│ Ledger       │  │ (Forward Rec.)   │  │ Triggered by:        │
│              │  │                  │◀─── FSM State Events   │
│ CQRS Rule ──▶│  │ (참조: Data G2)  │  │ (참조: Workflow G2)   │
│ (오너: Data) │  │                  │  │                      │
│              │  │ SkillOpt ───────────▶ GitOps Guard         │
│              │  │ (오너: Workflow)  │  │ (참조: Workflow G3)  │
└──────────────┘  └──────────────────┘  └──────────────────────┘
```

**화살표 읽는 법:**
- `→` 데이터/이벤트 흐름 (단방향)
- `참조:` 동일 원칙, 오너 헌법만 정의 (소비자 헌법은 "→ 참조"로 처리)
- `오너:` 이 원칙의 단일 권위 정의 위치

---

## Part I — 원칙 오너십 매트릭스 (4×3 MECE 테이블)

> 각 원칙은 단 하나의 헌법이 오너다. 다른 헌법에서 같은 원칙이 언급될 때는 반드시 오너 헌법을 참조한다.

| 원칙 # | 원칙명 | 오너 헌법 | 타 헌법에서의 취급 |
|--------|------|---------|----------------|
| D-1 | 단방향 불변 저널링 (Append-only) | **Data** | Governance G2.3: Security Audit Log에 동일 원칙 적용 |
| D-2 | 단일 진실 공급원 (SSOT) | **Data** | Workflow G1.2: SSOT에서 컨텍스트 로드 (소비자) · Governance G3.3: Snapshot 읽기 계약 |
| D-3 | 멱등적 데이터 재생산 | **Data** | — |
| D-4 | 결정론적 쓰기 독점 (CQRS) | **Data** | Workflow G1.2: 전처리/후처리 레이어 구현 · Interaction G2.1: "Data G2 CQRS의 에이전트 레이어 구현체"로 명시 |
| D-5 | 정적 스키마 검증 게이트웨이 | **Data** | Workflow G1.2 후처리 레이어 (소비자) |
| D-6 | 예외 원장 1급 객체화 | **Data** | Workflow G2.2: Forward Recovery에서 소비 · Governance G1.3: 집계 소비 |
| D-7 | 의미 기반 인덱스 (Semantic Index) | **Data** | 정형·비정형·그래프·온톨로지 모두 G1.1/G1.2/G1.3 동일 적용 (Data G1.2.1) |
| D-8 | 결정 추적 및 복리 지능 (Decision Trace) | **Data** | Interaction G1.2: L2 Context 소비 · Governance G1.3: Freshness 감시 |
| W-1 | System 1/2 이원화 라우팅 | **Workflow** | — |
| W-2 | 샌드위치 가드레일 실행 | **Workflow** | — |
| W-3 | FSM 기반 DAG 제약 | **Workflow** | Interaction G2.3: FSM Event 소비 (소비자) · Governance G3.2: FSM Event payload 계약 오너 |
| W-4 | 이원적 실패 회복 (Saga/Forward) | **Workflow** | — |
| W-5 | 커널 제어권 역전 (IoC) | **Workflow** | — |
| W-6 | 비차단형 HITL | **Workflow** | — |
| W-7 | 자가 스킬 최적화 (SkillOpt 트리거) | **Workflow** | Interaction G3.2: GitOps 배포 가드레일 (소비자) |
| I-1 | 계층형 컨텍스트 (L1/L2/L3) | **Interaction** | — |
| I-2 | 의미론적 퇴거 정책 (SSEP) | **Interaction** | — |
| I-3 | 무상태형 Grounding | **Interaction** | Workflow G3.1: "Interaction G1 참조" |
| I-4 | 권한 비대칭 (Propose vs Commit) | **Interaction** | Data G2 CQRS를 에이전트 레이어에서 구현한 것임을 명시 |
| I-5 | Prompt Injection 방어 (LLM 파싱) | **Interaction** | Governance G2.1: 시스템 경계 방어 (계층적 방어 구성) |
| I-6 | FSM 연동 페르소나 행렬 (SCPM) | **Interaction** | — |
| I-7 | GitOps SkillOpt 배포 가드레일 | **Interaction** | Workflow G7 트리거를 소비 (소비자) |
| G-1 | 3계층 텔레메트리 | **Governance** | — |
| G-2 | 가드레일 위반 감지·알림 | **Governance** | — |
| G-3 | Exception Ledger 집계 | **Governance** | Data G6 소비 (소비자) |
| G-4 | 시스템 경계 Injection 방어 | **Governance** | Interaction I-5와 계층적 방어 구성 (중복 아님) |
| G-5 | 최소 권한 감사 | **Governance** | — |
| G-6 | 불변 Security Audit Log | **Governance** | Data D-1과 동일 원칙 적용 (참조) |
| G-7 | 에이전트 정체성 프로토콜 (Agent Identity) | **Governance** | Workflow G3.2 HITL: 권한 위임 승인 채널 · Governance G2.2: 감사 레이어 |

**MECE 검증:** 29개 원칙, 각각 단 하나의 오너 헌법. 중복 정의 없음.

---

## Part II — 헌법 간 인터페이스 계약 요약

> 계약의 전체 스키마 정의는 Governance 헌법 G3에 있다. 여기서는 흐름만 요약한다.

```
[I.1] Exception Ledger
  생성: Data G3 → 기록: data/telemetry/ 또는 exception_ledger.jsonl
  소비: Workflow G2.2 (Forward Recovery 재개 판단)
        Governance G1.3 (집계 대시보드)

[I.2] FSM State Transition Event
  생성: Workflow G2.1 FSM
  소비: Interaction G2.3 SCPM (페르소나 교체 트리거)
        Governance G1.2 (FSM State Transition Log)

[I.3] SSOT Snapshot
  생성: Data 헌법 (SSOT 쓰기, 경로: data/snapshots/)
  소비: Interaction G1 L1 Anchor Zone (읽기 전용)
        Governance G1.3 (Freshness 체크)

[I.4] Observability Telemetry Spans
  생성: 모든 헌법의 각 실행 노드
  소비: Governance G1.1 (Trace 재구성, Metrics 집계)
```

---

## Part III — 헌법 수정 파급 효과 테이블

> 한 헌법을 수정할 때 반드시 검토해야 하는 다른 헌법 조항을 명시한다.

| 수정 대상 | 반드시 검토할 항목 |
|---------|----------------|
| Data G2 CQRS 수정 | Interaction G2.1 (CQRS 구현체) 정합성 확인 |
| Data G3 Exception Ledger 스키마 변경 | Governance G3.1 인터페이스 계약 I.1 업데이트 |
| Data G4 Decision Trace 구조 변경 | Interaction G1.2 (L2 Context 소비 방식) · Governance G1.3 (Freshness 감시) 검토 |
| Workflow G2.1 FSM 상태 추가/제거 | Governance G3.2 인터페이스 계약 I.2 업데이트 · Interaction G2.3 SCPM 페르소나 매핑 검토 |
| Workflow G3.3 SkillOpt 트리거 조건 변경 | Interaction G3.2 GitOps 가드레일 영향 검토 |
| Interaction G1 L1 크기 제한 변경 | Interaction G1.3 SSEP 가중치 재조정 · Governance G1.1 토큰 비용 Metrics 임계값 검토 |
| Interaction G2.3 SCPM 페르소나 파일 추가 | Workflow G2.1 FSM 상태와의 매핑 완결성 검증 |
| Governance G2.4 Agent Identity 권한 범위 변경 | Governance G2.2 최소 권한 감사 매트릭스 동기화 · Workflow G3.2 HITL 승인 채널 검토 |
| Governance G3 인터페이스 계약 스키마 변경 | 해당 계약의 생성자·소비자 헌법 동시 업데이트 필수 |

---

## Part IV — 헌법 수정 절차

### 원칙 수정 시
1. 수정하려는 원칙의 오너 헌법을 확인한다 (Part I 테이블)
2. 해당 헌법 파일을 수정한다
3. Part III 테이블에서 파급 효과 목록을 확인한다
4. 영향받는 헌법 파일의 "참조" 조항을 업데이트한다
5. `constitution.md` Part I 매트릭스를 갱신한다

### 인터페이스 계약 수정 시
1. Governance G3에서 스키마를 수정한다
2. 해당 계약의 생성자·소비자 헌법을 동시에 업데이트한다
3. 기존 구현체(Python 스크립트, YAML config)의 호환성을 검증한다

### 신규 원칙 추가 시
1. 가장 적합한 오너 헌법을 결정한다
2. Part I 테이블에 새 행을 추가한다 (오너 단일 지정 필수)
3. 타 헌법에서 이 원칙을 참조해야 한다면 "참조" 조항만 추가한다

---

## 현황 요약 (2026-06-05 기준)

| 헌법 | 버전 | 파일 | 상태 |
|------|------|------|------|
| Data | v1.1 | `01-data-constitution.md` | ✅ |
| Workflow | v3.0 | `02-workflow-constitution.md` | ✅ |
| Interaction | v3.1 | `03-interaction-constitution.md` | ✅ |
| Governance | v4.1 | `04-governance-constitution.md` | ✅ |
| **통합 헌법** | **v4.1** | **`00-constitution.md`** | **✅** |
