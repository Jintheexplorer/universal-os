# Universal OS — Product Requirements Document (PRD)

> **What does this system do?**
> Universal OS는 사용자가 대화를 통해 AI와 함께 자신의 삶을 운영하고 진화시키는 시스템이다.
> 기능 요구사항, User Story, 성공 기준을 정의한다.

**참조:** `01-vision.md` (Why) · `03-architecture.md` (How)

---

## User Story — Day in a Life

> Jinwan, SAP 솔루션 아키텍트 + 대학원생. 두 개의 MacBook에서 일하고 공부하고 운동한다.
> 4개 자본을 매일 조금씩 쌓아 나다운 삶으로 나아간다.

### 🌅 06:30 — 기상·카디오 (신체자본)

SAP 출근 전 40분 러닝. 기말 기간에도 카디오 앵커만큼은 지킨다.

```
시스템이 하는 일:
  Apple Health 동기화 → wellness snapshot 갱신
  오늘 카디오 O → "주 5세션 목표: 3/5" 카운터 증가
```

Jinwan은 그냥 뛴다. 시스템이 기록한다.

---

### ☕ 07:30 — Morning Brief (LifeOS)

```
Claude Code /daily →
  · 오늘 집중점: "기말고사 통계학 D-3. Run 방어 최소화"
  · 경제자본: 6월 현재 지출 페이스 (월 목표 대비 %)
  · 신체자본: 주간 운동 앵커 달성률
  · Bridge: WorkOS 마지막 업데이트 "3h ago" — OK
  · 오늘 Top 3 태스크 추천 (CR 알고리즘 기반)
```

---

### 🏢 09:00 — WorkOS 시작 (SAP MacBook)

GTM 전략 발표 준비. 고객사 CIO 대상 솔루션 아키텍처 프레젠테이션.

```
WorkOS 태스크 로그:
  task_manager.py in_progress --id "workos-101"
  → axis: "build"
  → skill_tags: ["gtm-strategy", "solution-design"]
  → complexity: "L", audience: "CIO", delivery_channel: "presentation"
```

---

### 🎤 14:00 — 발표 완료

```
task_manager.py done --id "workos-101" --output "링크/발표자료.pptx"

PostToolUse Hook 자동 실행:
  → BridgeProtocol.write(tasks, GDrive/bridge/workos_tasks.json)
    · axis="build" → LifeOS Build 버킷 반영
    · skill_tags: gtm-strategy Level 2→3 진행 중
    · Track Record: complexity=L + High Impact
    · Influence: audience=C-level, delivery_channel=presentation
```

나는 발표를 마쳤다. 시스템은 그 사실을 세 가지 Professional Capital 신호로 기록했다.

---

### 🏋️ 18:30 — 웨이트 트레이닝 (신체자본 + PT 세션)

Strong 앱으로 세트 기록.

```
워크아웃 스냅샷 갱신:
  · 이번 주 웨이트 2/4 완료
  · 총 볼륨 누적
  목표: "PT 2회 + 주말 개인 2 + 주중 카디오 — 카디오 앵커 3/4 달성"
```

---

### 📚 20:00 — 대학원 통계학 공부 (Build — Learning)

기말고사 D-3.

```
/study "베이즈 추론 — 사전분포와 사후분포 관계"
  → Learning Agent: knowledge/topics/venture/statistics/bayes.md 업서트
  → 새 Concept 노드 생성: Bayes.posterior
  → Concept-[related_to]-Concept 링크 (Phase 2)
```

---

### 🌙 22:30 — 일일 마감 + 지출 로그 (경제자본)

카드 지출 3건 유니버설 저널에 입력.

```
LifeOS Finance — log_entry 프로세스:
  PERCEIVE: JournalEntry schema 검증
  ACT: universal_journal.csv 추가
  (PLAN + REFLECT disabled)

  Exception 발생 시:
    → apply_policy(schema, unknown_enum_value) → PolicyAction.REJECT
    → Claude Code: "category '야식' 없음. wants/needs 중 어디에?"
    → 사용자 답변 → config.yaml 업데이트
```

---

### 📋 금요일 저녁 — 주간 리뷰

```
/weekly-review →
  [경제자본] 이번 주 지출 ₩xxx,xxx — wants 비율 체크
  [업력자본] WorkOS 태스크 Bridge → professional.json 집계
             GTM 발표 → Track Record: L×High 추가
  [신체자본] 카디오 4/4 ✅, 웨이트 4/4 ✅, PT 2/2 ✅
  [인문자본] philosopher: "기말 압박 중 나다움 유지했나?"
  [SkillOpt] "finance.md log_entry 섹션 예외 처리 지시 모호" → 개선 제안
```

---

### 🗂️ 월말 — R2R 결산

```
Claude Code: "6월 결산 시작"
  → load_process_config("processes/finance/config.yaml")

  PERCEIVE: INCOME ✅ CASH ✅ ASSET ✅ / 이상치 없음
  ACT: P&L / B/S / CF 계산
       · 저축률 54% → 목표(50%) 초과 ✅
       · reconcile_gap 3,200원 (tolerance 이내) ✅
       store_snapshot: economic.json 갱신
  REFLECT: statement_2026-06.md 생성
           insight: "기말 기간 외식 증가 — 시험 2주 전 예산 별도 필요"
```

---

## 기능 요구사항

### FR-1. Capital Snapshot 관리

| ID | 요구사항 |
|----|---------|
| FR-1-1 | 4개 자본(Economic, Professional, Humanistic, Wellness)의 현재 상태를 각각 단일 JSON 파일(snapshot)에 유지한다 |
| FR-1-2 | snapshot은 도메인 Python 스크립트를 통해서만 갱신된다. 직접 편집 금지 |
| FR-1-3 | 각 자본의 측정 방식은 config.yaml로 선언하며 대화로 AI가 수정할 수 있다 |

### FR-2. Process Loop (PERCEIVE → PLAN → ACT → REFLECT)

| ID | 요구사항 |
|----|---------|
| FR-2-1 | 모든 프로세스는 4단계 루프를 따른다. 단계별 활성화/비활성화는 config로 선언한다 |
| FR-2-2 | 결정론적 단계(계산, 검증, 저장)는 Python이 실행한다 |
| FR-2-3 | 확률론적 단계(예측, 시뮬레이션, 인사이트 추출)는 Claude Code가 실행한다 |
| FR-2-4 | 예외는 policy.yaml 선언대로 처리하고 exception ledger에 기록한다 |

### FR-3. Bridge (WorkOS → LifeOS)

| ID | 요구사항 |
|----|---------|
| FR-3-1 | WorkOS 태스크 완료 시 Universal Task Schema 형식으로 GDrive bridge 파일에 직렬화한다 |
| FR-3-2 | LifeOS는 bridge 파일을 읽기만 한다. 역방향 쓰기 금지 |
| FR-3-3 | bridge 파일이 24시간 이상 미갱신이면 LifeOS /daily 실행 시 경고를 표시한다 |

### FR-4. Professional Capital 측정

| ID | 요구사항 |
|----|---------|
| FR-4-1 | 태스크 완료 시 skill_tags, skill_domain → Skill 레벨 신호를 기록한다 |
| FR-4-2 | output_link, complexity → Experience(Track Record) 신호를 기록한다 |
| FR-4-3 | audience, delivery_channel → Influence(3R) 신호를 기록한다 |
| FR-4-4 | Skill 레벨은 4단계(Learning/Applying/Leading/Defining)로 측정하며 증거 기반으로만 갱신한다 |

### FR-5. Knowledge 축적

| ID | 요구사항 |
|----|---------|
| FR-5-1 | /study 명령으로 학습 내용을 Knowledge wiki에 upsert한다 |
| FR-5-2 | REFLECT 단계 후 인사이트를 knowledge/insights/에 저장한다 |
| FR-5-3 | SkillOpt: REFLECT 단계 후 해당 SKILL.md의 개선 제안을 생성한다 |

### FR-6. Config 진화 (대화 기반)

| ID | 요구사항 |
|----|---------|
| FR-6-1 | 사용자가 대화로 지출 분류 변경을 요청하면 AI가 config.yaml을 수정하고 validate한다 |
| FR-6-2 | additive 변경(새 항목 추가)은 즉시 적용, reclassification 변경은 reprocess 필요 여부를 표시한다 |
| FR-6-3 | config 변경 이력은 config_history 필드에 버전별로 기록된다 |

---

## 성공 기준

| 기준 | 측정 방법 |
|------|---------|
| 매일 자동 집계 | 운동·지출·태스크 로그가 수동 개입 없이 capital snapshot에 반영됨 |
| 대화로 Config 변경 | "배달음식을 wants로 분류해줘" → config.yaml 수정 → validate → 다음 결산부터 반영 |
| Bridge 24h 이내 동기화 | WorkOS 태스크 완료 후 24h 이내 LifeOS Morning Brief에 반영 |
| 4개 자본 월간 스냅샷 | 매월 말 4개 자본 snapshot이 자동 갱신됨 |
| SkillOpt 작동 | 월간 리뷰 후 관련 SKILL.md 개선 제안이 생성됨 |
