# Universal OS — Architecture Specification

> **How is this system structured?**
> UniversalOS의 추상 사양. 모든 OS 인스턴스(LifeOS, WorkOS, 미래 OS)가 상속해야 하는
> 불변 계층 구조, 프로세스 계약, 스키마 표준, 디렉토리 규약을 정의한다.
>
> **인스턴스 값(구체적 경로, 실제 데이터, OS별 설정)은 이 문서에 없다.**
> 인스턴스 값은 각 OS의 Variant 문서(`lifeos-variant.md`, `workos-variant.md`)에 있다.

**참조:** `01-vision.md` (Why) · `02-prd.md` (What) · `lifeos-variant.md` · `workos-variant.md`

---

## 핵심 공식

```
SSOT  +  Process & Architecture  +  Closed Learning Loop
  =  System of Context + Action
```

---

## 5계층 구조

```
L1  Principle      왜 이 OS가 존재하는가?        (운영 계약 — 가장 안정적)
L2  Identity       누가 운영하는가?               (자아 정의 — 안정적)
L3  Purpose        어디로 가는가?                 (비전+미션 — 전환점에서 변경)
L4  Capital        무엇을 키우는가?               (자원 유형+측정 — 천천히 진화)
L5  Execution      어떻게 실행하고 학습하는가?     (프로세스+데이터+루프 — 자주 변경)
```

**계층 우선순위:** L1이 가장 안정적. 두 결정이 충돌하면 L1이 이긴다.
**L3 ≠ L5:** Purpose는 목적지를 정의. Execution은 경로를 정의. Run/Build는 L5에 속한다.

각 계층은 두 레벨에서 정의된다:
- **System spec** (보편적·추상적·이 문서에 있음)
- **Instance variant** (OS별 구체적 값·각 OS의 Variant 문서에 있음)

---

## L1 — Principle

**System spec:**
OS의 운영 원칙 — 존재 이유. 모든 것이 여기서 흐른다. 하위 계층 간 충돌을 해소하는 기준.

**규칙:**
- 변경 주체: 사람만. AI 자동 수정 금지.
- 갈등 해소 proxy: ① 자본을 생성/보호하는가? ② L3 Purpose에 부합하는가? ③ OS별 SSOT 독립성을 보존하는가?

**Variant 선언 형식:**
```
파일: system/principles.md
내용: 해당 OS의 운영 원칙 선언
```

---

## L2 — Identity

**System spec:**
사람이 누구인지에 대한 구조화된 정의. 직함(occupation)이 아닌 소명(vocation)으로 정의한다.

**Identity 정의 구조:**
- **Driving question:** "나는 무엇을 하기 위해 여기 있는가?"
- **Core values:** 불확실성 속에서 결정을 이끄는 원칙
- **Core beliefs:** 세상에 대해 진실이라고 믿는 것
- **Development stage:** 현재 직업적 정체성 발전 단계

**규칙:**
- 파일: `system/identity.md`
- propose-and-confirm만 허용. AI 단독 수정 금지.

**인스턴스 간 관계:**
```
전인적 자아 Identity (LifeOS)
  └── 직업적 Identity (WorkOS) — 같은 자아가 업무 맥락에서 어떻게 표현되는가
```

**Variant 선언 형식:**
```
파일: system/identity.md
필수 섹션: driving_question, core_values[], development_stage
```

---

## L3 — Purpose

**System spec:**
사람이 어디로 가는지 — 장기 방향. Identity(누구인가)와 Execution(매일 무엇을 하는가)을 잇는 다리.
Purpose는 목적지를 정의하고, Execution은 경로를 정의한다.

**Purpose 정의 구조:**
- **Vision:** 만들거나 기여하고자 하는 세상
- **Mission:** 고유한 기여 방법
- **Roadmap:** 미션을 추구하는 단계별 진행

**규칙:**
Purpose는 수년에 걸쳐 안정적이다. 커리어 전환, 가치관 재평가 같은 진정한 삶의 전환점에서 변경된다.
주간 계획 사이클에서 변경하는 것이 아니다.

**Variant 선언 형식:**
```
파일: system/purpose.md
필수 섹션: vision, mission, roadmap (단계별 진행)
```

---

## L4 — Capital

**System spec:**
Capital = 복리로 축적되는 자원. 투자하면 늘고 방치하면 줄어든다.
모든 OS는 다음을 정의해야 한다:
1. 어떤 Capital type을 관리하는가
2. 축적을 어떻게 측정하는가
3. SSOT snapshot이 어디에 있는가

**측정 원칙:** 자기 평가는 노이즈. 증거가 신호. 만들었거나 전달했거나 외부에서 인정받은 산출물로 측정한다.

**Capital 구성은 확장 가능하다.** 새 Capital type은 Variant 문서에 항목을 추가하는 것으로 정의된다.

**Variant 선언 형식:**
```yaml
# system/user-config.yaml
capitals:
  - name: economic
    snapshot_file: data/snapshots/economic.json
    philosophy_file: system/capitals/economic.md
    agent_file: .claude/agents/finance.md
  # ... 추가 Capital
```

---

### Professional Capital 측정 프레임 (보편 사양)

모든 Professional Capital을 관리하는 OS는 이 세 자산을 구현한다.

#### Skill — "내가 할 수 있는 것"

이산적이고 증명 가능한 능력. Skill ≠ Knowledge. Skill은 실행 가능한 역량이다.

**4단계 숙련도:**

| Level | Label | 행동 지표 |
|-------|-------|---------|
| 1 | **Learning** | 개념 이해. 감독하에 실행 |
| 2 | **Applying** | 표준 상황에서 독립 실행 |
| 3 | **Leading** | 복잡성 처리. 타인 지도 가능 |
| 4 | **Defining** | 방향과 기준 설정. 외부 인정 |

**도메인:** Technical · Business
**감쇠:** Technical ~2–3년 · Business ~5–7년 · 항상 증거로 검증

#### Experience — "내가 해낸 것"

검증 가능한 증거 포트폴리오. Skill이 실제 조건에서 적용된 기록.

**복잡도 × 임팩트 매트릭스:**

|  | Low Complexity | High Complexity |
|--|---------------|-----------------|
| **High Impact** | Leverage | Peak work |
| **Low Impact** | Learning | Demonstrated capability |

**복잡도:** S(단독·명확) / M(크로스팀) / L(전략·조직) / XL(그린필드)
**임팩트:** 영향받은 사람 수, 비즈니스 가치, 변화 범위

#### Influence — "누가 나를 무엇으로 아는가"

**3R 모델:**

| R | 신호 | 측정 |
|---|------|------|
| **Reach** | 내 생각이 닿는 사람 수 | 발표 참석자, 글 조회수 |
| **Recognition** | 전문가로 호출되는 빈도 | 초청, 인용, 추천 요청 |
| **Reputation** | 도메인이 내 이름과 연결되는 정도 | "X 하면 너를 떠올린다" |

**커리어 단계:** Evangelist (Reach) → Architect (Recognition) → Entrepreneur (Reputation)

**태스크 완료 신호 연결:**
```
skill_tags + skill_domain   →  Skill 레벨 신호
output_link + complexity    →  Experience 증거
audience + delivery_channel →  Influence 신호
```

---

## L5 — Execution

### 5a. AI-Native 4-Stage Loop

**두 기둥:**

| 기둥 | 성격 | 역할 |
|------|------|------|
| **Deterministic** | Python 런타임 | 검증, 계산, 저장, 스키마 강제. 재현 가능, 감사 가능 |
| **Probabilistic** | Claude Code Runtime | 예측, 시뮬레이션, 추론, 생성, 개선. 맥락 감응, 생성적 |

**4단계 매크로 루프:**

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ ① PERCEIVE  │───▶│  ② PLAN     │───▶│   ③ ACT     │───▶│ ④ REFLECT   │
│             │    │              │    │              │    │              │
│ What is     │    │ What should  │    │ Execute and  │    │ What did     │
│ true now?   │    │ be done?     │    │ record.      │    │ we learn?    │
│             │    │              │    │              │    │              │
│ det: load,  │    │ det: compute │    │ det: calc,   │    │ det: update  │
│ validate,   │    │ priorities,  │    │ store SSOT,  │    │ history,     │
│ check miss  │    │ constraints  │    │ lock period  │    │ generate     │
│             │    │              │    │              │    │ reports      │
│ prob: detect│    │ prob:simulate│    │ prob: resolve│    │ prob: extract│
│ anomalies   │    │ scenarios,   │    │ exceptions,  │    │ insights,    │
│             │    │ recommend    │    │ edge cases   │    │ SkillOpt     │
└──────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
       │                   ▲
       └───────────────────┘
          closed loop
```

**단계 정의:**

**① PERCEIVE** — 결정 전에 맥락을 수집·검증
- Deterministic: SSOT 파일 로드, schema 검증, check_missing, 현재 상태 계산
- Probabilistic: 이상치 감지, 비정상 패턴 플래그, 누락 데이터 예측

**② PLAN** — 두 시간 지평에서 무엇을 할지 결정
- *Strategic* (중장기): 시나리오 모델링, 자본 성장 시뮬레이션, 장기 배분
- *Tactical/Operational* (단기): 태스크 우선순위 결정, Run/Build 예산 배분
- Deterministic: CR/WSPT 기반 우선순위 계산, 제약 조건 적용
- Probabilistic: 시나리오 시뮬레이션, 의사결정 추천 (`hitl: required`)

**③ ACT** — 정밀하게 실행하고 기록
- Deterministic: 계산 실행, SSOT 저장, period lock, exception policy 적용
- Probabilistic: 모호한 예외 해소, 선언 외 edge case 처리

**④ REFLECT** — 의미를 추출하고 시스템을 개선
- Deterministic: 이력 파일 갱신, 구조화된 리포트 생성
- Probabilistic: 인사이트 추출, 기간별 패턴 식별, SkillOpt

**PDCA 대응:**

| PDCA | AI-Native 단계 | AI가 추가하는 것 |
|------|---------------|----------------|
| Plan | PERCEIVE + PLAN | 이상치 감지, 시나리오 시뮬레이션 |
| Do | ACT | 예외 추론, edge case 처리 |
| Check | REFLECT (det) | 자동 조정, 리포트 생성 |
| Act-on-system | REFLECT (prob) | SkillOpt: 시스템이 스스로 프로세스를 개선 |

---

#### 단계 가중치 (프로세스 유형별)

```yaml
# 프로세스 유형별 stage weight 선언
process_type    PERCEIVE  PLAN    ACT     REFLECT
record_to_report  heavy   minimal  heavy   medium
weekly_plan       medium  heavy    light   medium
log_entry         light   none*    heavy   none*
financial_sim     medium  heavy    none*   medium
distill           light   none*    light   heavy
# * enabled: false로 비활성화
```

---

#### Runtime: Claude Code

**Claude Code가 Runtime이자 Orchestrator다.** 이것은 고정 전제다.

```
universalos.yaml 읽기
  → 단계별 컴포넌트를 순서대로 라우팅
  → Python 스크립트를 결정론적 도구로 호출 (Bash/Read/Write)
  → SKILL.md 지시를 확률론적 노드로 실행
  → 4단계 루프는 Claude Code 세션 내에서 실행
```

- **State** = SSOT 파일 (`data/snapshots/*.json`). 두 번째 상태 레이어 없음.
- **조건부 라우팅** = config의 `condition:` 필드. Claude Code가 평가·라우팅.
- **Human-in-the-Loop** = Claude Code의 자연스러운 대화. `hitl: required`는 진행 전 사용자 확인.
- **오류 복구** = 컴포넌트별 `on_error:` 정책.

---

#### SkillOpt — 자기개선 메커니즘

REFLECT 단계 후 `skillopt: true`일 때 트리거:
1. `transcript.jsonl`에서 반복 실패/비효율 패턴 스캔
2. 담당 SKILL.md 식별
3. 개선된 지시로 재작성
4. 이전 버전은 교체 전 archive

---

### 5b. 컴포넌트 모델

각 단계는 **컴포넌트**로 구성된다. 두 가지 유형:

| 유형 | 제공자 | Ontology 자동 등록 | 사용 시점 |
|------|--------|------------------|---------|
| `component:` | Universal OS 표준 라이브러리 | ✅ 자동 | Universal OS가 커버하는 표준 로직 |
| `script:` | 각 OS (escape hatch) | ⚠️ 수동 선언 | 도메인별 커스텀 계산 |

두 유형 모두 `on_error:` 정책 지원: `halt | warn_continue | skip | flag_for_review`

---

#### Loop Configuration (선언 형식)

```yaml
# processes/{domain}/config.yaml
process_type: record_to_report
version: "1.0"

object_types: [JournalEntry, AssetSnapshot, FinancialMetrics, Statement]

config_history:
  - version: "1.0"
    effective_from: "YYYY-MM"
    change_type: initial   # additive | reclassification

exception_policy:
  schema:
    unknown_enum_value:
      action: reject       # or: warn_and_bucket
    missing_required_field:
      action: halt
  process:
    reconcile_gap:
      tolerance_krw: 10000
      action_over_tolerance: halt
  business:
    allowed_statuses: [posted, pending, reversed, exceptional]

loop:
  perceive:
    weight: heavy          # heavy | medium | light | none
    deterministic:
      - component: check_missing
        config:
          required_modules: [INCOME, CASH, ASSET]
          on_missing: halt
    probabilistic:
      - component: detect_anomalies
        config:
          skill: finance.md#perceive
          threshold_sigma: 2.0

  plan:
    enabled: false         # 또는 weight 선언

  act:
    weight: heavy
    deterministic:
      - component: calculate
        config: { ... }
      - script:
          path: processes/finance/pension_reconcile.py
          on_error: warn_continue
          output_key: pension_check
      - component: store_snapshot
        config:
          target: data/snapshots/economic.json

  reflect:
    weight: medium
    deterministic:
      - component: generate_report
        config:
          output: data/outputs/finance/statement_{period}.md
    probabilistic:
      - component: extract_insights
        config:
          skill: finance.md#reflect
          skillopt: true
```

---

### 5c. Exception Handling

**원칙: 예외는 에러 상태가 아닌 1급 데이터다.**

예외는 이유와 함께 기록되고 감사 가능하며 해소 가능하다.

**3단계 예외 레벨:**

| Level | 설명 | 예시 | 기본 동작 |
|-------|------|------|---------|
| **Schema exception** | 데이터가 선언된 타입/유효값과 불일치 | 미지 module 값, 필수 필드 누락 | 거부 후 exception ledger 기록 |
| **Process exception** | 계산 결과가 이상하거나 해소 불가 | reconcile gap > tolerance | 설정 가능: halt / warn-continue / auto-resolve |
| **Business exception** | 표준 흐름에서 의도적 이탈 | 취소 항목, 소급 수정 | `reversed / exceptional / pending` 상태 명시 |

---

### 5d. Config Versioning

**두 가지 변경 유형:**

| 유형 | 이력 영향 | 필요 조치 |
|------|---------|---------|
| **Additive** (새 유형, 새 필드) | 이력 데이터 유효 | 재처리 불필요 |
| **Reclassification** (카테고리 변경, 임계값 변경) | 이전 KPI가 새 규칙과 불일치 | 재처리 필요 |

**불변 원칙:** 원본 입력 데이터(저널 항목, 자산 스냅샷)는 절대 수정하지 않는다.
Config 변경은 계산 레이어에만 영향을 준다. 원본 데이터는 항상 재처리 가능하다.

---

### 5e. Data Architecture (3-Tier)

```
DATA ARCHITECTURE
├── Snapshots     (운영 상태 — 현재, 구조화, 기계 판독 가능)
│   지금 무엇이 사실인가. 모든 에이전트가 행동 전에 읽는 Ground Truth.
│   갱신 사이클마다 덮어씀. 대화 이력에서 재구성하지 않음.
│
├── Knowledge     (축적된 의미 — 내구적, 의미론적, 사람+기계)
│   무엇을 학습했는가. REFLECT 사이클을 통해 성장.
│   Wiki + Patterns + Insights + Ontology.
│
└── Memory        (단기 맥락 — 세션 또는 주 단위, 빠른 접근)
    지금 무엇이 필요한가. 용어집, 사용자 모델, 활성 참조.
    세션 또는 계획 기간으로 범위 제한.
```

---

#### Knowledge Ontology (목표 아키텍처)

Palantir Ontology 모델 차용: 원본 데이터 위의 객체지향, 그래프 인식 레이어.

**Object Types:**
```
Task           { id, title, status, axis, skill_tags, complexity }
Skill          { name, domain, level, last_validated }
Project        { id, title, capital, goal_id }
JournalEntry   { doc_id, module, period, direction, amount_krw, status }
FinancialMetrics { period, net_income, savings_rate, capital }
WellnessMetrics  { period, workout_sessions, total_volume, body_weight }
Insight        { content, period, topic, confidence }
Concept        { name, definition, domain }
Person         { name, relationship, last_interaction }
```

**Links (관계):**
```
Task          -[applies]→       Skill
Task          -[belongs_to]→    Project
Task          -[generates]→     Insight
JournalEntry  -[aggregates_to]→ FinancialMetrics
Insight       -[about]→         Concept
Concept       -[related_to]→    Concept   ← 도메인 간 그래프 합성
```

**Actions (상태 변경 시 트리거):**
```
Task.complete()               → Skill 레벨 신호 갱신, Experience 항목 추가
JournalEntry.post()           → PERCEIVE 단계 검증 트리거
FinancialMetrics.calculate()  → ACT 단계 계산 실행
Insight.distill()             → Concept 추출, Links 생성
Skill.decay_check()           → last_validated > decay_window이면 플래그
```

**Ontology 자동 등록:**
```bash
universalos register          # config 스캔 → registry.yaml 재생성
universalos register --diff   # 마지막 등록 이후 변경 사항 표시
universalos graph             # 현재 ontology 그래프 출력
```

---

## System Architecture

### 공유 디렉토리 규약

**같은 기능적 역할 = 같은 디렉토리명 + 같은 파일 형식.** 이것이 호환성 계약이다.

| 기능 | 경로 (모든 OS) | 책임 |
|------|--------------|------|
| L1 Principles | `system/principles.md` | 운영 원칙 선언 |
| L2 Identity | `system/identity.md` | 정체성 정의 |
| L3 Purpose | `system/purpose.md` | 비전·미션·로드맵 |
| L4 Capital config | `system/user-config.yaml` | Capital 구성 SSOT |
| L4 Capital philosophy | `system/capitals/{name}.md` | 각 Capital WHY-HOW |
| L5 Goals | `system/goals/` | annual.md, monthly.md |
| L5 Process configs | `processes/{domain}/config.yaml` | 루프 선언 |
| L5 Execution | `processes/` | 도메인 Python 스크립트 |
| Agents | `.claude/agents/*.md` | 도메인 에이전트 |
| Knowledge | `knowledge/` | wiki, patterns, insights, ontology |
| Memory | `memory/` | 단기 맥락 |
| Snapshots | `data/snapshots/` | 자본 현재 상태 |
| Outputs | `data/outputs/` | 생성된 리포트, HTML |
| Inputs | `data/inputs/` | 원본 입력 데이터 (불변) |

---

### Universal OS Runtime (패키지 구조)

```
universal_os/                    # pip install git+https://github.com/Jintheexplorer/universal-os.git
  schema/
    base.py                      # ExtensibleSchema — config 기반 schema 확장 기반
    task.py                      # Task v1.0 (axis, priority, Professional Capital 필드 포함)
    journal.py                   # JournalEntry (경제자본 트랜잭션)
    asset.py                     # AssetSnapshot + Account
    professional.py              # Skill, Experience, Influence (Professional Capital 측정)
    wellness.py                  # WorkoutLog, WellnessMetrics (신체자본)
    snapshot.py                  # CapitalSnapshot base — 모든 자본 스냅샷의 공통 구조

  config/
    loader.py                    # load_process_config(path) → ProcessConfig
    validator.py                 # validate_config(cfg) → ValidationResult
                                 #   additive vs reclassification 감지 포함

  process_types/
    base.py                      # BaseProcess: PERCEIVE/PLAN/ACT/REFLECT 계약
    record_to_report.py          # R2R: 월 결산 (경제자본)
    log_entry.py                 # 이벤트 로그: 트랜잭션 단건 기록
    aggregate.py                 # 집계: 신체자본 주/월 집계
    plan.py                      # 계획 선언: 주간 자원배분
    bridge.py                    # Bridge: WorkOS→LifeOS 직렬화·전송

  capabilities/
    sense/
      check_missing.py           # 필수 데이터 존재 검증
      snapshot_loader.py         # SSOT 파일 로드
    act/
      ssot_writer.py             # SSOT 파일 쓰기
      history_updater.py         # 기간별 이력 JSON upsert
      period_lock.py             # 기간 잠금 (결산 완료)
    validate/
      schema_checker.py          # 선언된 schema 검증
      config_validator.py        # config.yaml ↔ 설치 버전 호환성 검증

  exception/
    policy.py                    # exception_policy 로드 + 정책 적용
    ledger.py                    # 추가 전용 JSONL exception 로그

  ontology/
    registry.py                  # config 스캔 → registry.yaml 생성 (Phase 1)
    graph.py                     # Link 순회 쿼리 (Phase 2)

  cli/
    main.py                      # universalos CLI
```

**CLI 커맨드:**
```bash
universalos update                     # GitHub에서 최신 버전으로 업그레이드
universalos init <os-type> <path>      # 디렉토리 구조 scaffold
universalos validate <config>          # config schema + 호환성 검증
universalos validate --config-history  # 재처리 필요 버전 변경 감지
universalos register <os-root>         # ontology registry 재생성
universalos register --diff            # 변경 사항 표시
universalos graph <os-root>            # ontology 그래프 출력 (Phase 2)
universalos reprocess --from YYYY-MM   # 지정 기간부터 RECORD 단계 재실행 (Phase 2)
universalos diff-config v1.1 v1.2      # config 버전 간 차이 표시 (Phase 2)
```

---

### Universal Task Schema v1.0

```
필수 (모든 태스크):
  id              string    OS 접두사 포함 (lifeos-*, workos-*)
  title           string    한 줄 설명
  status          enum      todo | in_progress | blocked | completed | cancelled
  priority        enum      P1 | P2 | P3
  axis            enum      run | build
  owner           string
  created_at      ISO 8601

표준 선택 필드:
  due_date, started_at, completed_at, estimate
  blocked_by      array     태스크 ID 목록
  tags            array
  output_link     string
  comment         string

Professional Capital 필드 (WorkOS 주, LifeOS 읽기):
  skill_tags      array     ["gtm-strategy", "solution-design"]
  skill_domain    enum      technical | business
  complexity      enum      S | M | L | XL
  audience        string
  delivery_channel string
```

OS별 확장 필드는 Variant 문서에 선언한다.

---

### Bridge: SSOT 전송 프로토콜

```
WorkOS SSOT (tasks.json)
  → [PostToolUse hook]
  → Universal Task Schema로 직렬화
  → GDrive/bridge/workos_tasks.json 쓰기 (WorkOS만 쓰기)
  → [LifeOS bridge_loader.py가 /daily에서 읽기]
  → LifeOS 태스크 뷰 + professional.json 집계
```

**규칙:**
- 같은 스키마 = 변환 레이어 없음
- WorkOS 권위. LifeOS는 읽기만.
- Stale 임계값: 24시간 경고

---

## 설계 제약

1. **독립성** — 각 OS는 자체 SSOT 유지. 합병 없음.
2. **단방향 Bridge** — WorkOS 전송, LifeOS 읽기. 역방향 금지.
3. **인간 권위** — L1, L2 변경은 명시적 인간 승인 필요.
4. **공유 디렉토리 규약** — 같은 기능 역할 = 같은 디렉토리명 + 파일 형식.
5. **공유 태스크 스키마** — 같은 필드 정의. 각자 SSOT. Bridge에서 변환 레이어 없음.
6. **System/Instance 분리** — 이 문서에 개인 데이터 없음. 인스턴스 값은 각 OS의 Variant 문서.
7. **L3 ≠ L5** — Purpose는 목적지. Execution은 경로. Run/Build는 L5.
8. **Variant 확장성** — 새 OS는 Variant 컬럼만 추가. 이 문서의 어떤 섹션도 변경 불필요.
9. **예외는 1급 데이터** — 조용한 실패 없음. 모든 이탈은 exception ledger에 기록, 감사 가능, 해소 가능.
10. **원본 데이터 불변** — Config 변경은 계산 레이어에만 영향. 원본 SSOT 데이터는 항상 재처리 가능.
11. **Ontology follows config** — Schema와 process config 변경은 `universalos register`를 통해 ontology registry에 자동 전파.
12. **대화로 진화** — 사용자는 코드를 작성하지 않는다. 대화 → AI가 config 수정 → universalos validate → 시스템 진화. 모든 확장은 선언된 Config/Extension 방법론 안에서만.

---

## Open Questions (미해결)

1. **WorkOS `system/identity.md`** — Vocation 정의 미작성. 최우선 gap.
2. **Ontology Links + Actions** — Phase 2. SQLite graph vs DuckDB vs MCP tool 평가 필요.
3. **Config versioning tooling** — `universalos reprocess`, `diff-config` 미구현.
4. **Exception ledger 형식** — 비즈니스 예외를 위한 추가 전용 로그 구조 미표준화.
5. **SkillOpt 구현** — transcript.jsonl 스캔 + SKILL.md 자동 개선 메커니즘 미구현.
