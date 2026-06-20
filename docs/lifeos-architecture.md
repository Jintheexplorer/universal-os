# LifeOS — Instance Variant

> **Universal OS Architecture를 상속받은 LifeOS 인스턴스 선언.**
> 이 문서는 `03-architecture.md`의 모든 추상 사양을 구체화한다.
> LifeOS 고유의 Capital 구성, 경로, 설정, 현재 구현 상태를 정의한다.

**참조:** `03-architecture.md` (base spec) · `01-vision.md` · `02-prd.md`
**저장소:** `Jintheexplorer/LifeOS`

---

## L1 — Principle (LifeOS Instance)

```
파일: system/principles.md
원칙: "나다운 삶 — 자율적이고 나답게 살며 네 가지 자본을 복리로 성장시킨다"
갈등 해소: L1 > L2 > L3 > L4 > L5 순서
핵심 가치 우선순위: 안전 > 자율 > 성장 > 혁신 > 창의
```

---

## L2 — Identity (LifeOS Instance)

```
파일: system/identity.md
범위: 전인적 자아 (Whole person)
정의 방식: 소명(Vocation)으로 정의
상태: ✅ 작성됨
변경 규칙: propose-and-confirm만 허용
```

---

## L3 — Purpose (LifeOS Instance)

```
파일: system/purpose.md
상태: ✅ 작성됨
내용: 장기 목표, 단계별 로드맵
```

---

## L4 — Capital Configuration (LifeOS Instance)

LifeOS는 4개 Capital을 관리한다.

```yaml
# system/user-config.yaml (SSOT)
capitals:
  - name: economic
    snapshot_file: data/snapshots/economic.json
    philosophy_file: system/capitals/economic.md
    agent_file: .claude/agents/finance.md

  - name: professional
    snapshot_file: data/snapshots/professional.json
    philosophy_file: system/capitals/professional.md
    agent_file: .claude/agents/career.md

  - name: humanistic
    snapshot_file: data/snapshots/humanistic.json
    philosophy_file: system/capitals/humanistic.md
    agent_file: .claude/agents/philosopher.md

  - name: wellness
    snapshot_file: data/snapshots/wellness.json
    philosophy_file: system/capitals/wellness.md
    agent_file: .claude/agents/wellness.md
```

### Professional Capital 측정 (LifeOS 집계)

WorkOS가 생산한 Professional Capital 신호를 LifeOS가 집계·분석한다.

```
WorkOS tasks.json (Bridge 경유)
  → skill_tags 누적 → Craft 레벨 분포
  → output_link + complexity → Track Record 포트폴리오
  → audience + delivery_channel → Influence 3R 누적
→ data/snapshots/professional.json 갱신
```

---

## L5 — Execution (LifeOS Instance)

### 프로세스 목록

| 도메인 | 프로세스 유형 | config 경로 | 트리거 |
|--------|------------|------------|-------|
| Finance | record_to_report | `processes/operations/finance/config.yaml` | /monthly, 대화 |
| Finance | log_entry | `processes/operations/finance/config.yaml` | 지출 입력 시 |
| Wellness | aggregate | `processes/operations/wellness/config.yaml` | 운동 기록 시, /weekly |
| Goal Cadence | plan | `processes/operations/goal_cadence/config.yaml` | /weekly-plan |
| Bridge | bridge | `processes/bridge/config.yaml` | /daily (읽기) |

### 스킬 (Skills)

| 스킬 | 경로 | 역할 |
|------|------|------|
| /weekly-plan | `.claude/commands/weekly-plan.md` | 주간 자원배분 선언 |
| /log | `.claude/commands/log.md` | 일일 캡처 |
| /weekly-review | `.claude/commands/weekly-review.md` | 주간 리뷰 |
| /distill | `.claude/commands/distill.md` | 루프 닫기·SkillOpt |
| /study | `.claude/commands/study.md` | 학습 Wiki Ingest |
| /check | `.claude/commands/check.md` | 신체 이상치 점검 |
| /goal-board | `.claude/commands/goal-board.md` | 목표 현황 시각화 |

### Loop 트리거 (LifeOS)

| 단계 | 트리거 |
|------|-------|
| PERCEIVE 입력 | Capital snapshots, 목표 파일, 캘린더 |
| ACT 기록 대상 | `data/snapshots/*.json` |
| REFLECT 증류 대상 | `knowledge/insights/` |
| Probabilistic runtime | Claude Code |

---

## 디렉토리 현황

| 경로 | 기능 | 상태 |
|------|------|------|
| `system/principles.md` | L1 | ✅ |
| `system/identity.md` | L2 | ✅ |
| `system/purpose.md` | L3 | ✅ |
| `system/user-config.yaml` | Capital SSOT | ✅ |
| `system/capitals/*.md` | Capital 철학 | ✅ (4개) |
| `system/goals/` | L5 목표 | ✅ |
| `processes/operations/finance/` | Finance 프로세스 | ✅ |
| `processes/operations/wellness/` | Wellness 프로세스 | ✅ |
| `processes/operations/goal_cadence/` | Goal Cadence | ✅ |
| `processes/tasks/task_manager.py` | Task CRUD | ✅ |
| `data/snapshots/` | 자본 스냅샷 | ✅ (4개) |
| `data/inputs/finance/universal_journal.csv` | 경제자본 입력 SSOT | ✅ |
| `knowledge/` | Wiki + Patterns + Insights | ✅ |
| `memory/` | 단기 맥락 | ✅ |
| `.claude/agents/*.md` | 도메인 에이전트 | ✅ (4개) |
| `processes/{domain}/config.yaml` | 프로세스 config | ⚠️ 미외부화 (하드코딩) |
| `knowledge/ontology/` | Ontology registry | ❌ 미구현 |

---

## 마이그레이션 Gap (Universal OS 패키지 도입 시)

Plan B에서 처리할 항목:

| Gap | 현재 상태 | 목표 |
|-----|---------|------|
| schema import 경로 | `processes/operations/finance/schema.py` | `universal_os.schema.journal` |
| `_OPERATING_TYPES` 하드코딩 | `process.py` 내 상수 | `processes/finance/config.yaml` |
| Task schema 필드 | `axis`, `priority`, `skill_tags` 없음 | Universal Task Schema v1.0 |
| bridge_loader.py | 자체 구현 | `universal_os.process_types.bridge` 사용 |

---

## LifeOS 고유 Task 확장 필드

Universal Task Schema v1.0 기반 + LifeOS 전용 필드:

```
goal_id       string    연결된 목표 ID
project_id    string    연결된 프로젝트 ID
milestone_id  string    연결된 마일스톤 ID
capital       enum      economic | professional | humanistic | wellness
task_type     enum      Operation | Project | Individual
```
