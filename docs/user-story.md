# User Story: Jinwan's Day in a Life

> 나는 개인을 하나의 **자율형 기업(Autonomous Enterprise)**으로 경영하며 살고 싶다.
> 네 가지 자본(경제·직업·인문·신체)을 복리로 키우면서, 나다움을 잃지 않고 목표에 가까워지는 삶.
> 이 시스템은 그 삶을 지탱하는 AI-Native 운영체제다.

---

## 전제: 나는 두 개의 환경에서 살고 있다

```
[개인 MacBook — LifeOS]        [SAP MacBook — WorkOS]
  · 경제, 신체, 인문 자본 관리       · 업무 실행 환경
  · 주간/월간 계획 + 리뷰           · GTM, 솔루션 설계, 고객 대응
  · 지식 축적 + 자기 성찰           · Professional Capital 생산
        ↑                              ↓
        └── Google Drive Bridge (단방향) ──┘
                workos_tasks.json
```

---

## Day in a Life — 평일 (기말고사 스프린트 中, 2026-06)

### 🌅 06:30 — 기상·카디오 (신체자본)

SAP 출근 전 40분 러닝. 기말 기간에도 카디오 앵커만큼은 지킨다.

```
[자동 기록 — LifeOS Health Coach]
  Apple Health 동기화 → wellness snapshot 갱신
  오늘 카디오 O → "주 5세션 목표: 3/5" 카운터 증가
```

*시스템이 하는 일*: 운동 세션 자동 집계. Jinwan은 그냥 뛴다.

---

### ☕ 07:30 — Morning Brief (LifeOS)

```
Claude Code /daily 실행 →
  · 오늘 집중점: "기말고사 통계학 D-3. Run 방어 최소화"
  · 경제자본: 6월 현재 지출 페이스 (월 목표 대비 %)
  · 신체자본: 주간 운동 앵커 달성률
  · Bridge: WorkOS 마지막 업데이트 "3h ago" — OK
  · 오늘 Top 3 태스크 추천 (CR 알고리즘 기반)
```

*시스템이 하는 일*: BridgePayload.is_stale() 체크, Capital snapshot 조회, 태스크 우선순위 계산.

---

### 🏢 09:00 — WorkOS 시작 (SAP MacBook)

GTM 전략 발표 준비. 고객사 CIO 대상 솔루션 아키텍처 프레젠테이션.

```
[WorkOS 태스크 로그]
  WorkOS: task_manager.py in_progress --id "workos-101"
  → axis: "build", skill_tags: ["gtm-strategy", "solution-design"]
  → complexity: "L", audience: "CIO", delivery_channel: "presentation"
```

*시스템이 하는 일*: Professional Capital 신호(skill_tags, complexity) 태스크에 자동 부착.

---

### 🎤 14:00 — 발표 완료

```
[WorkOS 태스크 완료]
  task_manager.py done --id "workos-101" --output "링크/발표자료.pptx"

[PostToolUse Hook 자동 실행]
  → BridgeProtocol.write(tasks, GDrive/bridge/workos_tasks.json)
    · axis="build" (→ LifeOS Build 버킷에 반영)
    · skill_tags: gtm-strategy Level 2→3 진행 중
    · Track Record: complexity=L + High Impact (CIO 대상)
    · Influence: audience=C-level, delivery_channel=presentation
```

*나는 발표를 마쳤다. 시스템은 그 사실을 세 가지 Professional Capital 신호로 기록했다.*

---

### 🏋️ 18:30 — 웨이트 트레이닝 (신체자본 + PT 세션)

PT 세션. Strong 앱으로 세트 기록.

```
[LifeOS Health Coach — 운동 기록]
  워크아웃 스냅샷 갱신:
    · 이번 주 웨이트 2/4 완료
    · 총 볼륨 누적
  목표 트래킹:
    "PT 2회 + 주말 개인 2 + 주중 카디오 — 카디오 앵커 3/4 달성"
```

---

### 📚 20:00 — 대학원 통계학 공부 (Build — Learning Capital)

기말고사 D-3. 핵심 개념 정리.

```
/study "베이즈 추론 — 사전분포와 사후분포 관계"
  → Learning Agent: knowledge/topics/venture/statistics/bayes.md 업서트
  → 새 Concept 노드 생성: Bayes.posterior
  → Concept-[related_to]-Concept 링크 (Phase 2 — 현재는 wikilink)
```

*시스템이 하는 일*: 학습 내용을 Knowledge 레이어에 축적. 매일 조금씩 wiki가 자란다.

---

### 🌙 22:30 — 일일 마감 + 지출 로그 (경제자본)

오늘 카드 지출 3건 유니버설 저널에 입력.

```
[LifeOS Finance — log_entry 프로세스]
  PERCEIVE: validate_input → JournalEntry schema 검증
  ACT: append_to_ssot → universal_journal.csv에 추가
  (PLAN + REFLECT disabled — log_entry weight 설정)

  Exception 없음 → 정상 기록
  만약 category가 선언되지 않은 값이라면:
    → apply_policy(schema, unknown_enum_value) → PolicyAction.REJECT
    → Claude Code: "category '야식' 없음. wants/needs 중 어디에 넣을까요?"
    → 사용자 답변 → config.yaml 업데이트 가능
```

---

## Week Close — 금요일 저녁 (주간 리뷰)

```
/weekly-review →

  [경제자본] 이번 주 지출 ₩xxx,xxx
    · 5월 초과 패턴 재발? → wants 비율 체크
    · Bridge에서 WorkOS 태스크 Build 완료 2개 확인

  [업력자본] Professional Capital 갱신
    · workos_tasks.json → professional.json 집계
    · GTM 전략 발표 → Track Record: L×High 추가
    · skill_tags 누적: gtm-strategy (3개 태스크), solution-design (5개)

  [신체자본] 이번 주 운동 결산
    · 카디오 4/4 ✅, 웨이트 4/4 ✅, PT 2/2 ✅
    · 모든 앵커 달성 — 기말 스프린트 중 최상

  [인문자본] 주간 성찰
    · philosopher: "기말 압박 중 나다움 유지했나?"
    · NPS 자기 평가 + 주간 패턴 감지

  [SkillOpt] REFLECT 단계에서 감지된 개선점:
    · "finance.md의 log_entry 섹션 — 카테고리 예외 처리 지시가 모호함"
    → finance.md 자동 개선 제안
```

---

## Month Close — 6월 말 (R2R 결산)

```
Claude Code: "6월 결산 시작"
  → load_process_config("processes/finance/config.yaml")

  [PERCEIVE]
    check_missing: INCOME ✅ CASH ✅ ASSET ✅
    load_journal: 6월 전체 저널 로드
    detect_anomalies: 지출 2-sigma 이상치 없음

  [ACT]
    calculate: P&L / B/S / CF 계산
      · 저축률 54% → 목표(50%) 초과 달성 ✅
      · reconcile_gap: 3,200원 (tolerance 10,000원 이내) ✅
    store_snapshot: economic.json 갱신

  [REFLECT]
    generate_report: statement_2026-06.md 생성
    extract_insights:
      · "기말 기간 외식 증가 패턴 — 5월과 동일. 시험 직전 2주 예산 별도 관리 필요"
      → knowledge/insights/finance-2026-06.md 저장

  결과: 목표 달성. 순자산 ₩xxx,xxx 증가. 6월 잠금.
```

---

## 어떻게 자본이 복리로 쌓이는가

```
매일 작은 기록 (카디오 1회, 저널 1건, 태스크 1완료)
  ↓
매주 리뷰 (패턴 발견, 다음 주 계획)
  ↓
매월 결산 (4개 자본 스냅샷 갱신, 인사이트 축적)
  ↓
분기/연간 (Craft 레벨 성장, Track Record 포트폴리오, 목표 재조정)
  ↓
커리어 단계 전환: Evangelist → Architect → Entrepreneur

오늘의 "발표 완료" 1건이 →
  · 오늘: WorkOS 태스크 완료 기록
  · 이번 주: Bridge → LifeOS Professional Capital 신호
  · 이번 달: professional.json Track Record 집계
  · 올해: gtm-strategy Craft Level 증거 누적
  · 5년 후: Architect 단계 진입의 복리 증거
```

---

## Universal OS가 없으면 일어나는 일

| 상황 | 시스템 없이 | Universal OS와 함께 |
|------|-----------|-------------------|
| 태스크 완료 | 그냥 끝남 | Craft/Track Record/Influence 신호 자동 기록 |
| 운동 | 앱에만 남음 | 신체자본 snapshot에 집계, 목표 달성률 추적 |
| 카드 지출 | 모름 | wants/needs 분류, 저축률 실시간 업데이트 |
| 발표 잘 함 | 기억에만 남음 | complexity=L, audience=CIO → Track Record 영구 기록 |
| 설계가 바뀜 | 모든 코드 수정 | config.yaml만 변경, raw 데이터는 불변 |
| WorkOS 월요일 피드백 | LifeOS 모름 | Bridge 통해 자동 전파, 24h 내 LifeOS 반영 |

---

*이 문서는 구현의 목적을 기억하기 위한 것이다. 코드는 이 삶을 지탱하는 인프라다.*
