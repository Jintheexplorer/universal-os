# WorkOS — Instance Variant

> **Universal OS Architecture를 상속받은 WorkOS 인스턴스 선언.**
> 이 문서는 `03-architecture.md`의 모든 추상 사양을 구체화한다.
> WorkOS는 Professional Capital 실행 환경에 집중한다.
> 나머지 Capital(Economic, Humanistic, Wellness)은 LifeOS가 관리한다.

**참조:** `03-architecture.md` (base spec) · `lifeos-variant.md` (연결 OS)
**저장소:** `Jintheexplorer/WorkOS` (예정)
**연결:** WorkOS → GDrive Bridge → LifeOS (단방향)

---

## L1 — Principle (WorkOS Instance)

```
파일: system/principles.md
원칙: "업(Vocation)을 통한 나다움 실현 — 직(Job)이 아닌 소명으로 일한다"
상태: ⚠️ implicit only (명시적 문서 미작성)
```

---

## L2 — Identity (WorkOS Instance)

```
파일: system/identity.md
범위: 직업적 자아 (Professional expression of the whole-person identity)
관계: LifeOS Identity의 업무 맥락 표현
상태: ❌ 미작성 (최우선 Gap)

작성 필요 섹션:
  driving_question: "업무에서 나는 무엇을 하기 위해 여기 있는가?"
  vocation: 소명 정의 (역할 제목이 아닌)
  development_stage: Evangelist | Architect | Entrepreneur
  core_strengths: 업무 맥락에서 발휘되는 강점
```

---

## L3 — Purpose (WorkOS Instance)

```
파일: system/purpose.md (또는 WorkOS PRD §0)
상태: ✅ PRD §0에 inline 존재
이전 필요: system/purpose.md로 분리 권장
```

---

## L4 — Capital Configuration (WorkOS Instance)

WorkOS는 **Professional Capital 실행 환경**에만 집중한다.

```yaml
# system/user-config.yaml
capitals:
  - name: professional
    snapshot_file: data/snapshots/professional.json
    philosophy_file: system/capitals/professional.md
    agent_file: .claude/agents/career.md
```

나머지 Capital(Economic, Humanistic, Wellness)은 LifeOS가 관리.
WorkOS는 Bridge를 통해 LifeOS에 Professional Capital 원료를 전송한다.

### Professional Capital 생산 (WorkOS 역할)

WorkOS는 원료를 생산하고, LifeOS가 집계한다.

```
WorkOS 태스크 완료
  → skill_tags: ["gtm-strategy"] (Skill 신호)
  → complexity: "L", output_link: "..." (Experience 신호)
  → audience: "CIO", delivery_channel: "presentation" (Influence 신호)
  → BridgeProtocol.write() → GDrive/bridge/workos_tasks.json
  → LifeOS professional.json 집계 (다음 /daily 또는 /weekly-review)
```

---

## L5 — Execution (WorkOS Instance)

### 프로세스 목록

| 도메인 | 프로세스 유형 | 트리거 |
|--------|------------|-------|
| Task | log_entry | 태스크 시작/완료 시 |
| Task | plan | /morning (일일 우선순위) |
| Bridge | bridge | 태스크 완료 시 PostToolUse hook |

### 스킬 (목표 상태)

| 스킬 | 역할 |
|------|------|
| /morning | 일일 우선순위 설정 + CR/WSPT 스케줄링 |
| /weekly | 주간 리뷰 + Build/Run 배분 결산 |
| /tasks | 태스크 목록 + 우선순위 관리 |

### Loop 트리거 (WorkOS)

| 단계 | 트리거 |
|------|-------|
| PERCEIVE 입력 | 태스크 큐, 프로젝트 신호, 마감일 |
| ACT 기록 대상 | `data/snapshots/tasks.json` |
| REFLECT 증류 대상 | `knowledge/` 도메인 wiki |
| Bridge ACT | `GDrive/bridge/workos_tasks.json` (쓰기 전용) |

### Run / Build 배분

```
axis: "run"   → 현재가치 실현 (고객 대응, 기존 업무 실행)
axis: "build" → 미래가치 창조 (이니셔티브, GTM, 역량 개발)

배분 목표: system/principles.md에 선언
주간 결산: /weekly에서 실제 배분 vs. 목표 비교
```

---

## 디렉토리 목표 상태 (Universal OS 규약 기준)

| 경로 | 기능 | 현재 상태 | 목표 |
|------|------|---------|------|
| `system/principles.md` | L1 | ⚠️ implicit | 명시적 작성 |
| `system/identity.md` | L2 | ❌ 없음 | **최우선 작성** |
| `system/purpose.md` | L3 | ⚠️ PRD §0 inline | 별도 파일로 분리 |
| `system/user-config.yaml` | Capital SSOT | ❌ 없음 | 생성 필요 |
| `system/capitals/professional.md` | Professional Capital 철학 | ⚠️ partial | 완성 |
| `system/goals/` | L5 목표 | ❌ 없음 | annual.md + monthly.md |
| `data/snapshots/` | 자본 스냅샷 | ⚠️ `Tasks/` 등 비표준 경로 | 표준 경로로 이전 |
| `data/inputs/` | 원본 입력 | ⚠️ 분산 | 통합 |
| `processes/` | 도메인 스크립트 | ⚠️ `.claude/processes/` | 표준 경로로 이전 |
| `knowledge/` | Wiki | ⚠️ `WorkWiki/` | 표준 경로로 이전 |
| `memory/` | 단기 맥락 | ⚠️ `.claude/modules/M2-data/` | 표준 경로로 이전 |
| `.claude/agents/*.md` | 도메인 에이전트 | ✅ | 유지 |

---

## WorkOS 고유 Task 확장 필드

Universal Task Schema v1.0 기반 + WorkOS 전용 필드:

```
domain          string    업무 도메인 (gtm, solution-design, delivery 등)
hitl_required   boolean   사람 승인 필요 여부
source          string    태스크 출처 (고객 요청, 내부 이니셔티브 등)
related_artifacts array   관련 산출물 링크 목록
```

---

## Bridge 설정 (WorkOS → LifeOS)

```yaml
# processes/bridge/config.yaml
process_type: bridge
direction: outbound        # WorkOS는 쓰기만
target: GDrive/bridge/workos_tasks.json
schema: universal_task_v1
trigger: PostToolUse       # task_manager.py 완료 시 자동
stale_threshold_hours: 24  # LifeOS가 이 시간 이상 미읽으면 경고
```

---

## 마이그레이션 Gap (Plan C에서 처리)

| Gap | 현재 상태 | 목표 |
|-----|---------|------|
| `system/identity.md` | ❌ 없음 | Vocation 정의 작성 |
| 디렉토리 구조 | `WorkWiki/`, `Artifacts/` 등 비표준 | Universal OS 규약으로 이전 |
| Task schema | `axis`, `skill_tags`, `complexity` 없음 | Universal Task Schema v1.0 |
| Bridge export hook | ❌ 없음 | PostToolUse hook + BridgeProtocol |
| `processes/{domain}/config.yaml` | ❌ 없음 | 프로세스 config 외부화 |
