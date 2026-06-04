# Plan A: Universal OS Package Implementation

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the `universal_os` Python package — shared schema, process loop contracts, config loading, exception handling, ontology registration, and CLI — installable via `pip install git+https://github.com/Jintheexplorer/universal-os.git`.

**Architecture:** This is the deterministic backbone of the AI-Native Loop. It provides the *standard library* that LifeOS and WorkOS import: shared dataclasses (Task, JournalEntry, AssetSnapshot), the 4-stage loop contract (`BaseProcess`), config YAML loading, exception policy enforcement, and the `universalos` CLI. The probabilistic layer (Claude Code SKILL.md) orchestrates above this; this package only handles the deterministic layer. Scope: Phase 1 implementation — ontology graph traversal and config reprocessor are Phase 2.

**Tech Stack:** Python 3.11+, dataclasses, PyYAML, pytest, Click, packaging (pyproject.toml)

---

## User Journey (Jinwan's Perspective)

이 패키지가 실제로 어떻게 쓰이는지 — 완성 후 Jinwan의 일상 흐름.

### Journey 1: 새 WorkOS 태스크 완료 → LifeOS로 Professional Capital 전파

```
[WorkOS 환경 — SAP MacBook]
  1. Jinwan이 GTM 전략 발표 태스크 완료
     → WorkOS task_manager.py done --id "workos-101"

  2. PostToolUse hook 트리거
     → BridgeProtocol.write(tasks, GDrive/bridge/workos_tasks.json, source_os="workos")
     → Task에 axis="build", skill_tags=["gtm-strategy"], complexity="L" 포함

[GDrive — 공유 채널]
  3. workos_tasks.json 갱신됨 (Universal Task Schema v1.0)

[LifeOS 환경 — 개인 MacBook]
  4. Jinwan이 /daily 실행 → bridge_loader.py
     → BridgeProtocol.read(GDrive/bridge/workos_tasks.json)
     → stale 체크: exported_at 24h 이내 → OK
     → task.axis == "build" → Build 버킷에 집계
     → task.skill_tags → professional.json Craft 레벨 업데이트
```

**이 Journey가 가능해지려면:** Task 2 (schema), Task 6 (bridge) 완성 필요.

---

### Journey 2: 월 결산 실행 — Finance R2R 프로세스

```
[LifeOS — 월말]
  1. Jinwan: "6월 결산해줘"
     → Claude Code: finance.md skill 로드
     → ProcessConfig 로드: load_process_config("processes/finance/config.yaml")

  2. PERCEIVE 단계 (deterministic)
     → check_missing: INCOME + CASH + ASSET 있는지 확인
     → load_journal: universal_journal.csv 읽기
     → 이상치 감지 (probabilistic → Claude Code가 판단)

  3. ACT 단계 (deterministic)
     → calculate: config.yaml의 cf_classification으로 P&L / B/S / CF 계산
       (하드코딩 제거! _OPERATING_TYPES는 이제 config.yaml에 있음)
     → store_snapshot: economic.json 갱신

  4. 예외 발생 시
     → apply_policy(event, cfg.exception_policy) → PolicyAction
     → ExceptionLedger.append(event)
     → hitl: required → Claude Code가 Jinwan에게 확인 요청

  5. REFLECT 단계
     → statement_2026-06.md 생성
     → insights 추출 (Claude Code)
```

**이 Journey가 가능해지려면:** Task 4 (config), Task 7 (exception), Task 5 (BaseProcess) + Plan B (LifeOS migration).

---

### Journey 3: 새 OS 인스턴스 초기화

```
[미래 — WorkOS 또는 새 환경 세팅]
  1. universalos init workos ./my_workos
     → system/, data/snapshots/, processes/, knowledge/, memory/ 생성
     → system/principles.md 플레이스홀더 생성

  2. 도메인 config 작성
     → processes/tasks/config.yaml 작성 (process_type: log_entry, ...)

  3. universalos validate processes/tasks/config.yaml
     → ✅ Config valid

  4. universalos register .
     → knowledge/ontology/registry.yaml 생성
     → Task, JournalEntry 등 Object Type 자동 등록
```

**이 Journey가 가능해지려면:** Task 9 (CLI) 완성 필요. 완전 독립 실행 가능.

---

### Journey 4: Config 변경 시 — 새 지출 카테고리 추가

```
  1. Jinwan: "배달음식 지출을 needs→wants로 재분류하고 싶어"

  2. processes/finance/config.yaml 수정
     expense_categories.wants에 "배달음식" 추가
     config_history에 v1.2 additive 변경 기록

  3. universalos validate processes/finance/config.yaml
     → ✅ additive change, no reprocess needed

  4. 다음 달 결산부터 새 분류 적용됨
     (raw journal data는 그대로 — immutable)
```

**이 Journey가 가능해지려면:** Task 4 (config) + config_history 검증 로직.

---

### Journey 5: 설계가 어긋났을 때 — Exception Ledger 확인

```
  1. 6월 결산 중 연금 불일치 발생
     → reconcile_gap = 45,000 KRW (tolerance 10,000 초과)
     → apply_policy → PolicyAction.HALT
     → ExceptionLedger.append(event, period="2026-06")

  2. Claude Code → Jinwan에게 알림:
     "pension_check.gap_krw = 45,000원. tolerance(10,000원) 초과. 확인해줘."

  3. Jinwan: 원인 파악 후 → "이건 연금 입금 지연이야, 그냥 진행해"
     → Claude Code: business exception으로 기록 + --force 플래그로 계속

  4. exceptions.jsonl에 기록 남음 (영구 감사 추적)
```

**이 Journey가 가능해지려면:** Task 7 (exception) 완성 필요.

---

---

## Scope: What This Plan Builds

```
universal_os/
  schema/
    __init__.py
    task.py              ← Task v1.0 (axis + all universal fields)
    journal.py           ← JournalEntry (extracted from LifeOS finance/schema.py)
    asset.py             ← AssetSnapshot + Account (same extraction)
  config/
    __init__.py
    loader.py            ← load_process_config(path) → ProcessConfig dataclass
    validator.py         ← validate_config(cfg, installed_version) → ValidationResult
  process_types/
    __init__.py
    base.py              ← BaseProcess abstract class: PERCEIVE/PLAN/ACT/REFLECT hooks
    record_to_report.py  ← R2R concrete implementation of BaseProcess
    log_entry.py         ← LogEntry concrete implementation
    bridge.py            ← Bridge process: serialize + write/read bridge file
  exception/
    __init__.py
    policy.py            ← load_policy(config) + apply_policy(event) → PolicyAction
    ledger.py            ← append-only ExceptionLedger (JSONL)
  ontology/
    __init__.py
    registry.py          ← scan configs → generate knowledge/ontology/registry.yaml
  cli/
    __init__.py
    main.py              ← Click CLI: universalos validate | init | register
  __init__.py            ← package version

tests/
  test_schema_task.py
  test_schema_journal.py
  test_schema_asset.py
  test_config_loader.py
  test_config_validator.py
  test_process_base.py
  test_process_r2r.py
  test_process_log_entry.py
  test_process_bridge.py
  test_exception_policy.py
  test_exception_ledger.py
  test_ontology_registry.py
  test_cli.py

pyproject.toml           ← package metadata, dependencies, entry_points
```

**What is NOT in this plan (Phase 2):**
- `ontology/graph.py` — link traversal queries (requires graph DB choice)
- `exception/reprocessor.py` — re-run RECORD stage from a period
- `cli/` commands: `reprocess`, `diff-config` — depend on reprocessor
- `capabilities/` module — migrated from LifeOS in Plan B

---

## Task 1: Repository scaffold and pyproject.toml

**Files:**
- Create: `pyproject.toml`
- Create: `universal_os/__init__.py`
- Create: `tests/__init__.py`

- [ ] **Step 1: Write the failing test** (package importable)

```python
# tests/test_package.py
def test_package_imports():
    import universal_os
    assert hasattr(universal_os, "__version__")

def test_version_format():
    import universal_os
    parts = universal_os.__version__.split(".")
    assert len(parts) == 3
    assert all(p.isdigit() for p in parts)
```

- [ ] **Step 2: Run test to verify it fails**

```bash
cd "/Users/I340352/Library/CloudStorage/GoogleDrive-jinwan.hyun@gmail.com/My Drive/UniversalOS"
python -m pytest tests/test_package.py -v
```
Expected: `FAILED — ModuleNotFoundError: No module named 'universal_os'`

- [ ] **Step 3: Create pyproject.toml**

```toml
[build-system]
requires = ["setuptools>=68", "wheel"]
build-backend = "setuptools.backends.legacy:build"

[project]
name = "universal-os"
version = "0.1.0"
description = "AI-native personal OS framework — shared schema, process loop, and CLI"
requires-python = ">=3.11"
dependencies = [
    "PyYAML>=6.0",
    "click>=8.1",
]

[project.scripts]
universalos = "universal_os.cli.main:cli"

[tool.setuptools.packages.find]
where = ["."]
include = ["universal_os*"]

[tool.pytest.ini_options]
testpaths = ["tests"]
```

- [ ] **Step 4: Create `universal_os/__init__.py`**

```python
__version__ = "0.1.0"
```

- [ ] **Step 5: Create `tests/__init__.py`** (empty file, makes tests a package)

- [ ] **Step 6: Install in editable mode**

```bash
cd "/Users/I340352/Library/CloudStorage/GoogleDrive-jinwan.hyun@gmail.com/My Drive/UniversalOS"
pip install -e .
```
Expected: `Successfully installed universal-os-0.1.0`

- [ ] **Step 7: Run test to verify it passes**

```bash
python -m pytest tests/test_package.py -v
```
Expected: `PASSED`

- [ ] **Step 8: Commit**

```bash
git add pyproject.toml universal_os/__init__.py tests/
git commit -m "feat: scaffold universal-os package (pyproject.toml, editable install)"
```

---

## Task 2: Task schema (universal_os/schema/task.py)

The `Task` dataclass is the most critical shared contract — it's missing `axis`, `priority`, `owner`, `created_at` from both OSes. This task establishes the canonical v1.0 schema.

**Files:**
- Create: `universal_os/schema/__init__.py`
- Create: `universal_os/schema/task.py`
- Create: `tests/test_schema_task.py`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_schema_task.py
import pytest
from universal_os.schema.task import Task, TaskStatus, TaskPriority, TaskAxis


def test_task_minimal_required_fields():
    """All required fields must be present to construct a Task."""
    t = Task(
        id="lifeos-001",
        title="Write quarterly review",
        status=TaskStatus.TODO,
        priority=TaskPriority.P2,
        axis=TaskAxis.RUN,
        owner="jinwan",
        created_at="2026-06-05",
    )
    assert t.id == "lifeos-001"
    assert t.axis == TaskAxis.RUN
    assert t.status == TaskStatus.TODO


def test_task_missing_required_field_raises():
    with pytest.raises(TypeError):
        Task(id="lifeos-001", title="No axis")  # missing axis etc.


def test_task_optional_fields_default():
    t = Task(
        id="lifeos-002",
        title="Optional field test",
        status=TaskStatus.TODO,
        priority=TaskPriority.P3,
        axis=TaskAxis.BUILD,
        owner="jinwan",
        created_at="2026-06-05",
    )
    assert t.due_date is None
    assert t.skill_tags == []
    assert t.complexity is None
    assert t.goal_id is None


def test_task_professional_capital_fields():
    """WorkOS task with Professional Capital fields."""
    t = Task(
        id="workos-101",
        title="GTM strategy deck",
        status=TaskStatus.IN_PROGRESS,
        priority=TaskPriority.P1,
        axis=TaskAxis.BUILD,
        owner="jinwan",
        created_at="2026-06-01",
        skill_tags=["gtm-strategy", "solution-design"],
        skill_domain="business",
        complexity="L",
        audience="SAP executives",
        delivery_channel="presentation",
    )
    assert t.skill_tags == ["gtm-strategy", "solution-design"]
    assert t.complexity == "L"


def test_task_invalid_status_raises():
    with pytest.raises(ValueError, match="status"):
        Task(
            id="lifeos-003",
            title="Bad status",
            status="invalid_status",
            priority=TaskPriority.P2,
            axis=TaskAxis.RUN,
            owner="jinwan",
            created_at="2026-06-05",
        )


def test_task_invalid_axis_raises():
    with pytest.raises(ValueError, match="axis"):
        Task(
            id="lifeos-004",
            title="Bad axis",
            status=TaskStatus.TODO,
            priority=TaskPriority.P2,
            axis="sideways",
            owner="jinwan",
            created_at="2026-06-05",
        )


def test_task_to_dict_round_trip():
    """Serialize to dict and reconstruct."""
    t = Task(
        id="lifeos-005",
        title="Round trip",
        status=TaskStatus.COMPLETED,
        priority=TaskPriority.P1,
        axis=TaskAxis.RUN,
        owner="jinwan",
        created_at="2026-06-05",
        completed_at="2026-06-05",
        output_link="https://example.com/output",
    )
    d = t.to_dict()
    t2 = Task.from_dict(d)
    assert t2.id == t.id
    assert t2.status == t.status
    assert t2.output_link == t.output_link
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_schema_task.py -v
```
Expected: `FAILED — ImportError: cannot import name 'Task'`

- [ ] **Step 3: Create `universal_os/schema/__init__.py`** (empty)

- [ ] **Step 4: Create `universal_os/schema/task.py`**

```python
"""Task v1.0 — Universal OS shared task schema."""
from __future__ import annotations
from dataclasses import dataclass, field
from enum import Enum
from typing import List, Optional


class TaskStatus(str, Enum):
    TODO = "todo"
    IN_PROGRESS = "in_progress"
    BLOCKED = "blocked"
    COMPLETED = "completed"
    CANCELLED = "cancelled"


class TaskPriority(str, Enum):
    P1 = "P1"
    P2 = "P2"
    P3 = "P3"


class TaskAxis(str, Enum):
    RUN = "run"
    BUILD = "build"


@dataclass
class Task:
    # --- Required (all tasks) ---
    id: str
    title: str
    status: TaskStatus | str
    priority: TaskPriority | str
    axis: TaskAxis | str
    owner: str
    created_at: str  # ISO 8601 date string

    # --- Standard optional ---
    due_date: Optional[str] = None
    started_at: Optional[str] = None
    completed_at: Optional[str] = None
    estimate: Optional[str] = None          # "2h", "1d"
    blocked_by: List[str] = field(default_factory=list)
    tags: List[str] = field(default_factory=list)
    output_link: Optional[str] = None
    comment: Optional[str] = None

    # --- Professional Capital (WorkOS-primary, LifeOS-readable) ---
    skill_tags: List[str] = field(default_factory=list)
    skill_domain: Optional[str] = None     # "technical" | "business"
    complexity: Optional[str] = None       # "S" | "M" | "L" | "XL"
    audience: Optional[str] = None
    delivery_channel: Optional[str] = None

    # --- LifeOS-specific ---
    goal_id: Optional[str] = None
    project_id: Optional[str] = None
    milestone_id: Optional[str] = None
    capital: Optional[str] = None          # "economic" | "professional" | "humanistic" | "wellness"

    # --- WorkOS-specific ---
    domain: Optional[str] = None
    hitl_required: Optional[bool] = None
    source: Optional[str] = None
    related_artifacts: List[str] = field(default_factory=list)

    def __post_init__(self) -> None:
        # Coerce string → enum for status, priority, axis
        if isinstance(self.status, str):
            try:
                self.status = TaskStatus(self.status)
            except ValueError:
                raise ValueError(
                    f"status must be one of {[e.value for e in TaskStatus]}, got: {self.status!r}"
                )
        if isinstance(self.priority, str):
            try:
                self.priority = TaskPriority(self.priority)
            except ValueError:
                raise ValueError(
                    f"priority must be one of {[e.value for e in TaskPriority]}, got: {self.priority!r}"
                )
        if isinstance(self.axis, str):
            try:
                self.axis = TaskAxis(self.axis)
            except ValueError:
                raise ValueError(
                    f"axis must be one of {[e.value for e in TaskAxis]}, got: {self.axis!r}"
                )

    def to_dict(self) -> dict:
        import dataclasses
        d = dataclasses.asdict(self)
        # Serialize enums to their string values
        for key in ("status", "priority", "axis"):
            if isinstance(d[key], Enum):
                d[key] = d[key].value
        return d

    @classmethod
    def from_dict(cls, data: dict) -> "Task":
        return cls(**{k: v for k, v in data.items() if k in cls.__dataclass_fields__})
```

- [ ] **Step 5: Run tests to verify they pass**

```bash
python -m pytest tests/test_schema_task.py -v
```
Expected: All 7 tests PASS

- [ ] **Step 6: Commit**

```bash
git add universal_os/schema/ tests/test_schema_task.py
git commit -m "feat(schema): Task v1.0 — axis, priority, Professional Capital fields"
```

---

## Task 3: Journal and Asset schemas (extracted from LifeOS)

Extract `JournalEntry`, `AssetSnapshot`, `Account` from LifeOS `processes/operations/finance/schema.py` into the universal package. The LifeOS implementation will later import from here.

**Files:**
- Create: `universal_os/schema/journal.py`
- Create: `universal_os/schema/asset.py`
- Create: `tests/test_schema_journal.py`
- Create: `tests/test_schema_asset.py`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_schema_journal.py
import pytest
from universal_os.schema.journal import JournalEntry


def test_journal_entry_valid_income():
    e = JournalEntry(
        doc_id="INC-2026-001",
        module="INCOME",
        document_date="2026-06-01",
        posting_date="2026-06-01",
        period="2026-06",
        transaction_type="PAYROLL_IN",
        direction="in",
        amount_krw=5000000,
    )
    assert e.amount_krw == 5000000
    assert e.status == "posted"


def test_journal_entry_invalid_module():
    with pytest.raises(ValueError, match="module"):
        JournalEntry(
            doc_id="BAD-001",
            module="INVALID",
            document_date="2026-06-01",
            posting_date="2026-06-01",
            period="2026-06",
            transaction_type="PAYROLL_IN",
            direction="in",
            amount_krw=1000,
        )


def test_journal_entry_reversed_requires_reversal_of():
    with pytest.raises(ValueError, match="reversal_of"):
        JournalEntry(
            doc_id="REV-001",
            module="EXPENSE",
            document_date="2026-06-01",
            posting_date="2026-06-01",
            period="2026-06",
            transaction_type="CARD_OUT",
            direction="out",
            amount_krw=50000,
            status="reversed",
            # reversal_of missing → should raise
        )


def test_journal_entry_amount_string_coercion():
    """Accept comma-formatted strings like '8,280,000'."""
    e = JournalEntry(
        doc_id="INC-002",
        module="INCOME",
        document_date="2026-06-01",
        posting_date="2026-06-01",
        period="2026-06",
        transaction_type="PAYROLL_IN",
        direction="in",
        amount_krw="8,280,000",
    )
    assert e.amount_krw == 8280000
```

```python
# tests/test_schema_asset.py
import pytest
from universal_os.schema.asset import AssetSnapshot, Account


def test_asset_snapshot_valid():
    a = AssetSnapshot(
        doc_id="ASSET-001",
        period="2026-06",
        account_id=1001,
        balance_krw=10000000,
        posted_at="2026-06-30",
    )
    assert a.balance_krw == 10000000
    assert a.status == "posted"


def test_asset_snapshot_invalid_status():
    with pytest.raises(ValueError, match="status"):
        AssetSnapshot(
            doc_id="ASSET-002",
            period="2026-06",
            account_id=1001,
            balance_krw=10000000,
            posted_at="2026-06-30",
            status="active",  # invalid
        )


def test_account_is_active_coercion():
    """'true' string should coerce to True."""
    acc = Account(
        account_id=1001,
        account_name="IBK 통장",
        account_type="bank",
        liquidity="high",
        category="operating",
        institution="IBK",
        currency="KRW",
        is_active="true",
        cf_scope="operating",
    )
    assert acc.is_active is True
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_schema_journal.py tests/test_schema_asset.py -v
```
Expected: `FAILED — ImportError`

- [ ] **Step 3: Create `universal_os/schema/journal.py`**

```python
"""JournalEntry schema — extracted from LifeOS finance/schema.py."""
from __future__ import annotations
from dataclasses import dataclass
from typing import Optional


def _safe_int(v) -> int:
    if isinstance(v, int):
        return v
    return int(str(v).replace(',', '').replace(' ', ''))


_VALID_MODULES = {"INCOME", "EXPENSE", "CASH"}
_VALID_DIRECTIONS = {"in", "out"}
_VALID_STATUSES = {"posted", "pending", "reversed", "matched"}


@dataclass
class JournalEntry:
    # Required
    doc_id: str
    module: str
    document_date: str
    posting_date: str
    period: str
    transaction_type: str
    direction: str
    amount_krw: int

    # Optional common
    amount_orig: Optional[float] = None
    currency_orig: str = "KRW"
    fx_rate: float = 1.0
    status: str = "posted"
    reversal_of: str = ""
    matched_doc: str = ""

    # INCOME fields
    item: str = ""
    income_type: str = ""
    is_deduction: str = ""

    # EXPENSE fields
    category: str = ""
    area: str = ""

    # CASH fields
    channel: str = ""
    tax_deductible: str = ""

    def __post_init__(self) -> None:
        self.amount_krw = _safe_int(self.amount_krw)
        self.amount_orig = float(self.amount_orig) if self.amount_orig is not None else float(self.amount_krw)
        self.fx_rate = float(self.fx_rate)

        if self.module not in _VALID_MODULES:
            raise ValueError(f"module must be one of {_VALID_MODULES}, got: {self.module!r}")
        if self.direction not in _VALID_DIRECTIONS:
            raise ValueError(f"direction must be one of {_VALID_DIRECTIONS}, got: {self.direction!r}")
        if self.status not in _VALID_STATUSES:
            raise ValueError(f"status must be one of {_VALID_STATUSES}, got: {self.status!r}")
        if self.status == "reversed" and not self.reversal_of:
            raise ValueError("status='reversed' requires reversal_of to be set")
        if self.status == "matched" and not self.matched_doc:
            raise ValueError("status='matched' requires matched_doc to be set")
```

- [ ] **Step 4: Create `universal_os/schema/asset.py`**

```python
"""AssetSnapshot and Account schemas — extracted from LifeOS finance/schema.py."""
from __future__ import annotations
from dataclasses import dataclass
from typing import Optional


def _safe_int(v) -> int:
    if isinstance(v, int):
        return v
    return int(str(v).replace(',', '').replace(' ', ''))


def _coerce_bool(value) -> bool:
    if isinstance(value, bool):
        return value
    return str(value).strip().lower() in {"true", "1", "yes"}


@dataclass
class AssetSnapshot:
    doc_id: str
    period: str
    account_id: int
    balance_krw: int
    posted_at: str

    balance_orig: Optional[float] = None
    currency_orig: str = "KRW"
    fx_rate: float = 1.0
    status: str = "posted"
    reversal_of: str = ""

    def __post_init__(self) -> None:
        self.account_id = _safe_int(self.account_id)
        self.balance_krw = _safe_int(self.balance_krw)
        self.balance_orig = float(self.balance_orig) if self.balance_orig is not None else float(self.balance_krw)
        self.fx_rate = float(self.fx_rate)
        if self.status not in {"posted", "reversed"}:
            raise ValueError(f"status must be 'posted' or 'reversed', got: {self.status!r}")


@dataclass
class Account:
    account_id: int
    account_name: str
    account_type: str
    liquidity: str
    category: str
    institution: str
    currency: str
    is_active: bool
    cf_scope: str

    target_weight: float = 0.0
    opened_date: str = ""
    closed_date: str = ""

    def __post_init__(self) -> None:
        self.account_id = _safe_int(self.account_id)
        self.target_weight = float(self.target_weight)
        self.is_active = _coerce_bool(self.is_active)
```

- [ ] **Step 5: Run tests to verify they pass**

```bash
python -m pytest tests/test_schema_journal.py tests/test_schema_asset.py -v
```
Expected: All 7 tests PASS

- [ ] **Step 6: Commit**

```bash
git add universal_os/schema/journal.py universal_os/schema/asset.py \
    tests/test_schema_journal.py tests/test_schema_asset.py
git commit -m "feat(schema): JournalEntry + AssetSnapshot extracted from LifeOS"
```

---

## Task 4: Config loader and validator (universal_os/config/)

Process configs are currently hardcoded in LifeOS `process.py` (`_OPERATING_TYPES`, etc.). This task creates the loading infrastructure so any OS can externalize them.

**Files:**
- Create: `universal_os/config/__init__.py`
- Create: `universal_os/config/loader.py`
- Create: `universal_os/config/validator.py`
- Create: `tests/test_config_loader.py`
- Create: `tests/test_config_validator.py`
- Create: `tests/fixtures/valid_process_config.yaml`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_config_loader.py
import pytest
from pathlib import Path
from universal_os.config.loader import load_process_config, ProcessConfig

FIXTURES = Path(__file__).parent / "fixtures"


def test_load_valid_config():
    cfg = load_process_config(FIXTURES / "valid_process_config.yaml")
    assert isinstance(cfg, ProcessConfig)
    assert cfg.process_type == "record_to_report"
    assert cfg.version == "1.0"


def test_config_has_loop_stages():
    cfg = load_process_config(FIXTURES / "valid_process_config.yaml")
    assert "perceive" in cfg.loop
    assert "act" in cfg.loop
    assert "reflect" in cfg.loop


def test_config_has_exception_policy():
    cfg = load_process_config(FIXTURES / "valid_process_config.yaml")
    assert cfg.exception_policy is not None
    assert "schema" in cfg.exception_policy
    assert "process" in cfg.exception_policy


def test_load_nonexistent_file_raises():
    with pytest.raises(FileNotFoundError):
        load_process_config(Path("/tmp/does_not_exist.yaml"))


def test_load_missing_required_key_raises():
    from pathlib import Path
    import tempfile, yaml
    bad = {"version": "1.0"}  # missing process_type and loop
    with tempfile.NamedTemporaryFile(suffix=".yaml", mode="w", delete=False) as f:
        yaml.dump(bad, f)
        tmp_path = Path(f.name)
    with pytest.raises(KeyError, match="process_type"):
        load_process_config(tmp_path)
```

```python
# tests/test_config_validator.py
import pytest
from pathlib import Path
from universal_os.config.loader import load_process_config
from universal_os.config.validator import validate_config, ValidationResult

FIXTURES = Path(__file__).parent / "fixtures"


def test_valid_config_passes():
    cfg = load_process_config(FIXTURES / "valid_process_config.yaml")
    result = validate_config(cfg, installed_version="0.1.0")
    assert isinstance(result, ValidationResult)
    assert result.valid is True
    assert result.errors == []


def test_config_version_history_missing_initial_warns():
    cfg = load_process_config(FIXTURES / "valid_process_config.yaml")
    # Simulate missing config_history
    cfg.config_history = []
    result = validate_config(cfg, installed_version="0.1.0")
    # Should pass but warn (no config_history is non-fatal)
    assert result.valid is True


def test_validate_returns_errors_on_unknown_stage():
    cfg = load_process_config(FIXTURES / "valid_process_config.yaml")
    cfg.loop["unknown_stage"] = {}
    result = validate_config(cfg, installed_version="0.1.0")
    assert result.valid is False
    assert any("unknown_stage" in e for e in result.errors)
```

- [ ] **Step 2: Create test fixture `tests/fixtures/valid_process_config.yaml`**

```yaml
process_type: record_to_report
version: "1.0"

config_history:
  - version: "1.0"
    effective_from: "2026-01"
    change_type: initial

exception_policy:
  schema:
    unknown_enum_value:
      action: reject
    missing_required_field:
      action: halt
  process:
    reconcile_gap:
      tolerance_krw: 10000
      action_over_tolerance: halt
    missing_module:
      required: [INCOME, CASH]
      action: halt

loop:
  perceive:
    weight: heavy
    deterministic:
      - component: check_missing
        config:
          required_modules: [INCOME, CASH, ASSET]
          on_missing: halt
      - component: load_journal
        config:
          source: data/inputs/finance/universal_journal.csv
    probabilistic:
      - component: detect_anomalies
        config:
          skill: finance.md#perceive
          threshold_sigma: 2.0

  plan:
    weight: minimal
    probabilistic:
      enabled: false

  act:
    weight: heavy
    deterministic:
      - component: calculate
        config:
          aggregations: [pl, bs, cf, reconcile]
          cf_classification:
            operating: [PAYROLL_IN, CARD_OUT, INSURANCE_OUT, FIXED_OUT, TAX_OUT]
            investing: [INVEST_BUY_OUT, INVEST_SELL_IN, PENSION_OUT, SAVINGS_OUT]
            financing: [LOAN_IN, LOAN_REPAY_OUT, INTEREST_OUT]
          expense_categories:
            wants: [프리미엄 선호, 일상 선호]
            needs: [생필품, 고정비, 교통]
          kpi_targets:
            savings_rate: 0.50
            reconcile_gap_tolerance_krw: 10000
      - component: store_snapshot
        config:
          target: data/snapshots/economic.json
          merge_strategy: overwrite

  reflect:
    weight: medium
    deterministic:
      - component: generate_report
        config:
          format: markdown
          sections: [pl, bs, cf, kpi]
          output: data/outputs/finance/statement_{period}.md
    probabilistic:
      - component: extract_insights
        config:
          skill: finance.md#reflect
          skillopt: true
```

- [ ] **Step 3: Run tests to verify they fail**

```bash
python -m pytest tests/test_config_loader.py tests/test_config_validator.py -v
```
Expected: `FAILED — ImportError`

- [ ] **Step 4: Create `universal_os/config/__init__.py`** (empty)

- [ ] **Step 5: Create `universal_os/config/loader.py`**

```python
"""Process config YAML loader."""
from __future__ import annotations
from dataclasses import dataclass, field
from pathlib import Path
from typing import Any, Dict, List, Optional

import yaml


_VALID_LOOP_STAGES = {"perceive", "plan", "act", "reflect"}
_REQUIRED_KEYS = ("process_type", "loop")


@dataclass
class ProcessConfig:
    process_type: str
    version: str
    loop: Dict[str, Any]
    exception_policy: Optional[Dict[str, Any]] = None
    config_history: List[Dict[str, Any]] = field(default_factory=list)
    raw: Dict[str, Any] = field(default_factory=dict, repr=False)


def load_process_config(path: Path | str) -> ProcessConfig:
    path = Path(path)
    if not path.exists():
        raise FileNotFoundError(f"Process config not found: {path}")

    with open(path, "r", encoding="utf-8") as f:
        raw = yaml.safe_load(f)

    for key in _REQUIRED_KEYS:
        if key not in raw:
            raise KeyError(f"Process config missing required key: '{key}' in {path}")

    return ProcessConfig(
        process_type=raw["process_type"],
        version=str(raw.get("version", "1.0")),
        loop=raw["loop"],
        exception_policy=raw.get("exception_policy"),
        config_history=raw.get("config_history", []),
        raw=raw,
    )
```

- [ ] **Step 6: Create `universal_os/config/validator.py`**

```python
"""Process config validator."""
from __future__ import annotations
from dataclasses import dataclass, field
from typing import List

from universal_os.config.loader import ProcessConfig


_VALID_LOOP_STAGES = {"perceive", "plan", "act", "reflect"}


@dataclass
class ValidationResult:
    valid: bool
    errors: List[str] = field(default_factory=list)
    warnings: List[str] = field(default_factory=list)


def validate_config(cfg: ProcessConfig, installed_version: str) -> ValidationResult:
    errors: List[str] = []
    warnings: List[str] = []

    # Check for unknown loop stages
    for stage_name in cfg.loop:
        if stage_name not in _VALID_LOOP_STAGES:
            errors.append(
                f"Unknown loop stage '{stage_name}'. "
                f"Valid stages: {sorted(_VALID_LOOP_STAGES)}"
            )

    # Warn if no config_history (non-fatal)
    if not cfg.config_history:
        warnings.append(
            "config_history is empty. Add a version entry for auditing and reprocess support."
        )

    return ValidationResult(
        valid=len(errors) == 0,
        errors=errors,
        warnings=warnings,
    )
```

- [ ] **Step 7: Run tests to verify they pass**

```bash
python -m pytest tests/test_config_loader.py tests/test_config_validator.py -v
```
Expected: All 8 tests PASS

- [ ] **Step 8: Commit**

```bash
git add universal_os/config/ tests/test_config_loader.py \
    tests/test_config_validator.py tests/fixtures/
git commit -m "feat(config): ProcessConfig loader + validator with YAML fixture"
```

---

## Task 5: BaseProcess — 4-stage loop contract (universal_os/process_types/base.py)

All concrete processes (R2R, LogEntry, Bridge) inherit from `BaseProcess`. This defines the PERCEIVE→PLAN→ACT→REFLECT contract.

**Files:**
- Create: `universal_os/process_types/__init__.py`
- Create: `universal_os/process_types/base.py`
- Create: `tests/test_process_base.py`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_process_base.py
import pytest
from universal_os.process_types.base import BaseProcess, ProcessContext, StageResult


class ConcreteProcess(BaseProcess):
    """Minimal concrete implementation for testing the contract."""

    def perceive(self, ctx: ProcessContext) -> StageResult:
        ctx.state["perceived"] = True
        return StageResult(ok=True, data={"perceived": True})

    def act(self, ctx: ProcessContext) -> StageResult:
        ctx.state["acted"] = True
        return StageResult(ok=True, data={"acted": True})


def test_base_process_run_calls_all_active_stages():
    proc = ConcreteProcess()
    ctx = ProcessContext(period="2026-06", config=None)
    result = proc.run(ctx)
    assert ctx.state.get("perceived") is True
    assert ctx.state.get("acted") is True
    assert result.ok is True


def test_base_process_plan_and_reflect_default_noop():
    """plan() and reflect() default to no-op if not overridden."""
    proc = ConcreteProcess()
    ctx = ProcessContext(period="2026-06", config=None)
    r_plan = proc.plan(ctx)
    r_reflect = proc.reflect(ctx)
    assert r_plan.ok is True
    assert r_reflect.ok is True


def test_base_process_halts_on_perceive_failure():
    """If perceive() returns ok=False with on_error=halt, run() stops."""
    class FailingProcess(BaseProcess):
        def perceive(self, ctx: ProcessContext) -> StageResult:
            return StageResult(ok=False, error="missing data")

        def act(self, ctx: ProcessContext) -> StageResult:
            ctx.state["act_ran"] = True
            return StageResult(ok=True)

    proc = FailingProcess()
    ctx = ProcessContext(period="2026-06", config=None)
    result = proc.run(ctx)
    assert result.ok is False
    assert "missing data" in result.error
    assert ctx.state.get("act_ran") is None  # act did NOT run


def test_process_context_state_persists_across_stages():
    """State dict is shared across all stage calls."""
    class StatefulProcess(BaseProcess):
        def perceive(self, ctx: ProcessContext) -> StageResult:
            ctx.state["key"] = "from_perceive"
            return StageResult(ok=True)

        def act(self, ctx: ProcessContext) -> StageResult:
            assert ctx.state["key"] == "from_perceive"
            ctx.state["key"] = "modified_in_act"
            return StageResult(ok=True)

    proc = StatefulProcess()
    ctx = ProcessContext(period="2026-06", config=None)
    proc.run(ctx)
    assert ctx.state["key"] == "modified_in_act"
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_process_base.py -v
```
Expected: `FAILED — ImportError`

- [ ] **Step 3: Create `universal_os/process_types/__init__.py`** (empty)

- [ ] **Step 4: Create `universal_os/process_types/base.py`**

```python
"""BaseProcess — 4-stage loop contract: PERCEIVE → PLAN → ACT → REFLECT."""
from __future__ import annotations
from abc import ABC, abstractmethod
from dataclasses import dataclass, field
from typing import Any, Dict, Optional


@dataclass
class StageResult:
    ok: bool
    data: Dict[str, Any] = field(default_factory=dict)
    error: Optional[str] = None


@dataclass
class ProcessContext:
    period: str
    config: Any                                # ProcessConfig or None
    state: Dict[str, Any] = field(default_factory=dict)
    exceptions: list = field(default_factory=list)


class BaseProcess(ABC):
    """
    Abstract base for all Universal OS processes.

    Subclasses MUST implement perceive() and act().
    plan() and reflect() are optional — they default to no-op.

    run() calls all four stages in order. If any stage returns ok=False,
    execution halts immediately and the failed StageResult is returned.
    """

    @abstractmethod
    def perceive(self, ctx: ProcessContext) -> StageResult:
        ...

    def plan(self, ctx: ProcessContext) -> StageResult:
        return StageResult(ok=True)

    @abstractmethod
    def act(self, ctx: ProcessContext) -> StageResult:
        ...

    def reflect(self, ctx: ProcessContext) -> StageResult:
        return StageResult(ok=True)

    def run(self, ctx: ProcessContext) -> StageResult:
        for stage_fn in (self.perceive, self.plan, self.act, self.reflect):
            result = stage_fn(ctx)
            if not result.ok:
                return result
        return StageResult(ok=True, data=ctx.state)
```

- [ ] **Step 5: Run tests to verify they pass**

```bash
python -m pytest tests/test_process_base.py -v
```
Expected: All 4 tests PASS

- [ ] **Step 6: Commit**

```bash
git add universal_os/process_types/ tests/test_process_base.py
git commit -m "feat(process_types): BaseProcess 4-stage loop contract (PERCEIVE/PLAN/ACT/REFLECT)"
```

---

## Task 6: Bridge process (universal_os/process_types/bridge.py)

Bridge is the WorkOS→LifeOS transmission protocol. It serializes tasks using the Universal Task Schema and writes/reads a JSON file at the shared GDrive path.

**Files:**
- Create: `universal_os/process_types/bridge.py`
- Create: `tests/test_process_bridge.py`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_process_bridge.py
import json
import tempfile
from pathlib import Path

import pytest
from universal_os.process_types.bridge import BridgeProtocol, BridgePayload
from universal_os.schema.task import Task, TaskStatus, TaskPriority, TaskAxis


def _make_task(tid: str, axis: TaskAxis) -> Task:
    return Task(
        id=tid,
        title=f"Task {tid}",
        status=TaskStatus.TODO,
        priority=TaskPriority.P2,
        axis=axis,
        owner="jinwan",
        created_at="2026-06-05",
    )


def test_bridge_write_produces_valid_json():
    tasks = [_make_task("workos-001", TaskAxis.RUN), _make_task("workos-002", TaskAxis.BUILD)]
    with tempfile.TemporaryDirectory() as tmp:
        bridge_path = Path(tmp) / "workos_tasks.json"
        BridgeProtocol.write(tasks, bridge_path, source_os="workos")
        assert bridge_path.exists()
        payload = json.loads(bridge_path.read_text())
        assert payload["source_os"] == "workos"
        assert len(payload["tasks"]) == 2


def test_bridge_write_includes_exported_at():
    tasks = [_make_task("workos-003", TaskAxis.RUN)]
    with tempfile.TemporaryDirectory() as tmp:
        bridge_path = Path(tmp) / "workos_tasks.json"
        BridgeProtocol.write(tasks, bridge_path, source_os="workos")
        payload = json.loads(bridge_path.read_text())
        assert "exported_at" in payload
        assert payload["exported_at"]  # non-empty


def test_bridge_read_round_trip():
    tasks = [_make_task("workos-004", TaskAxis.BUILD)]
    with tempfile.TemporaryDirectory() as tmp:
        bridge_path = Path(tmp) / "workos_tasks.json"
        BridgeProtocol.write(tasks, bridge_path, source_os="workos")
        payload = BridgeProtocol.read(bridge_path)
        assert isinstance(payload, BridgePayload)
        assert len(payload.tasks) == 1
        assert payload.tasks[0].id == "workos-004"
        assert payload.tasks[0].axis == TaskAxis.BUILD


def test_bridge_read_missing_file_raises():
    with pytest.raises(FileNotFoundError):
        BridgeProtocol.read(Path("/tmp/no_such_bridge.json"))


def test_bridge_stale_detection():
    """is_stale() returns True if exported_at is older than threshold_hours."""
    tasks = [_make_task("workos-005", TaskAxis.RUN)]
    with tempfile.TemporaryDirectory() as tmp:
        bridge_path = Path(tmp) / "workos_tasks.json"
        BridgeProtocol.write(tasks, bridge_path, source_os="workos")
        # Freshly written — not stale
        payload = BridgeProtocol.read(bridge_path)
        assert payload.is_stale(threshold_hours=24) is False
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_process_bridge.py -v
```
Expected: `FAILED — ImportError`

- [ ] **Step 3: Create `universal_os/process_types/bridge.py`**

```python
"""Bridge process — WorkOS → LifeOS SSOT transmission."""
from __future__ import annotations
import json
from dataclasses import dataclass, field
from datetime import datetime, timezone
from pathlib import Path
from typing import List

from universal_os.schema.task import Task


@dataclass
class BridgePayload:
    source_os: str
    exported_at: str           # ISO 8601 UTC
    tasks: List[Task] = field(default_factory=list)

    def is_stale(self, threshold_hours: int = 24) -> bool:
        exported = datetime.fromisoformat(self.exported_at.replace("Z", "+00:00"))
        now = datetime.now(tz=timezone.utc)
        delta = (now - exported).total_seconds() / 3600
        return delta > threshold_hours


class BridgeProtocol:
    """Serialize and deserialize the bridge file. No computation — data only."""

    @staticmethod
    def write(tasks: List[Task], path: Path, source_os: str) -> None:
        path = Path(path)
        path.parent.mkdir(parents=True, exist_ok=True)
        payload = {
            "source_os": source_os,
            "exported_at": datetime.now(tz=timezone.utc).isoformat(),
            "tasks": [t.to_dict() for t in tasks],
        }
        path.write_text(json.dumps(payload, ensure_ascii=False, indent=2), encoding="utf-8")

    @staticmethod
    def read(path: Path) -> BridgePayload:
        path = Path(path)
        if not path.exists():
            raise FileNotFoundError(f"Bridge file not found: {path}")
        raw = json.loads(path.read_text(encoding="utf-8"))
        tasks = [Task.from_dict(t) for t in raw.get("tasks", [])]
        return BridgePayload(
            source_os=raw["source_os"],
            exported_at=raw["exported_at"],
            tasks=tasks,
        )
```

- [ ] **Step 4: Run tests to verify they pass**

```bash
python -m pytest tests/test_process_bridge.py -v
```
Expected: All 5 tests PASS

- [ ] **Step 5: Commit**

```bash
git add universal_os/process_types/bridge.py tests/test_process_bridge.py
git commit -m "feat(bridge): BridgeProtocol write/read + stale detection"
```

---

## Task 7: Exception policy and ledger (universal_os/exception/)

**Files:**
- Create: `universal_os/exception/__init__.py`
- Create: `universal_os/exception/policy.py`
- Create: `universal_os/exception/ledger.py`
- Create: `tests/test_exception_policy.py`
- Create: `tests/test_exception_ledger.py`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_exception_policy.py
import pytest
from universal_os.exception.policy import (
    ExceptionEvent,
    ExceptionLevel,
    PolicyAction,
    apply_policy,
)

SAMPLE_POLICY = {
    "schema": {
        "unknown_enum_value": {"action": "reject"},
        "missing_required_field": {"action": "halt"},
    },
    "process": {
        "reconcile_gap": {
            "tolerance_krw": 10000,
            "action_over_tolerance": "halt",
        },
        "missing_module": {
            "required": ["INCOME", "CASH"],
            "action": "halt",
        },
    },
    "business": {
        "allowed_statuses": ["posted", "pending", "reversed", "exceptional"],
        "retroactive_correction": {"allow": True, "requires_reason": True},
    },
}


def test_schema_exception_reject():
    event = ExceptionEvent(
        level=ExceptionLevel.SCHEMA,
        rule="unknown_enum_value",
        detail="module='UNKNOWN'",
    )
    action = apply_policy(event, SAMPLE_POLICY)
    assert action == PolicyAction.REJECT


def test_process_exception_halt_on_exceed():
    event = ExceptionEvent(
        level=ExceptionLevel.PROCESS,
        rule="reconcile_gap",
        detail="gap=50000 krw",
        value=50000,
    )
    action = apply_policy(event, SAMPLE_POLICY)
    assert action == PolicyAction.HALT


def test_process_exception_warn_within_tolerance():
    """Gap within tolerance → warn only, not halt."""
    event = ExceptionEvent(
        level=ExceptionLevel.PROCESS,
        rule="reconcile_gap",
        detail="gap=5000 krw",
        value=5000,
    )
    action = apply_policy(event, SAMPLE_POLICY)
    assert action == PolicyAction.WARN


def test_unknown_rule_returns_flag_for_review():
    """Rules not declared in policy → conservative flag_for_review."""
    event = ExceptionEvent(
        level=ExceptionLevel.PROCESS,
        rule="undeclared_rule",
        detail="something unusual",
    )
    action = apply_policy(event, SAMPLE_POLICY)
    assert action == PolicyAction.FLAG_FOR_REVIEW
```

```python
# tests/test_exception_ledger.py
import json
import tempfile
from pathlib import Path

from universal_os.exception.ledger import ExceptionLedger
from universal_os.exception.policy import ExceptionEvent, ExceptionLevel


def test_ledger_appends_events():
    with tempfile.TemporaryDirectory() as tmp:
        ledger_path = Path(tmp) / "exceptions.jsonl"
        ledger = ExceptionLedger(ledger_path)
        event = ExceptionEvent(
            level=ExceptionLevel.BUSINESS,
            rule="retroactive_correction",
            detail="Correcting 2026-05 CARD_OUT amount",
            period="2026-05",
        )
        ledger.append(event)
        lines = ledger_path.read_text().strip().splitlines()
        assert len(lines) == 1
        record = json.loads(lines[0])
        assert record["rule"] == "retroactive_correction"
        assert record["period"] == "2026-05"


def test_ledger_creates_parent_dirs():
    with tempfile.TemporaryDirectory() as tmp:
        ledger_path = Path(tmp) / "nested" / "dir" / "exceptions.jsonl"
        ledger = ExceptionLedger(ledger_path)
        event = ExceptionEvent(
            level=ExceptionLevel.SCHEMA,
            rule="unknown_enum_value",
            detail="test",
        )
        ledger.append(event)
        assert ledger_path.exists()


def test_ledger_is_append_only():
    with tempfile.TemporaryDirectory() as tmp:
        ledger_path = Path(tmp) / "exceptions.jsonl"
        ledger = ExceptionLedger(ledger_path)
        for i in range(3):
            ledger.append(ExceptionEvent(
                level=ExceptionLevel.SCHEMA,
                rule="test",
                detail=f"event {i}",
            ))
        lines = ledger_path.read_text().strip().splitlines()
        assert len(lines) == 3
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_exception_policy.py tests/test_exception_ledger.py -v
```
Expected: `FAILED — ImportError`

- [ ] **Step 3: Create `universal_os/exception/__init__.py`** (empty)

- [ ] **Step 4: Create `universal_os/exception/policy.py`**

```python
"""Exception policy: load and apply per-process exception configuration."""
from __future__ import annotations
from dataclasses import dataclass, field
from enum import Enum
from typing import Any, Dict, Optional


class ExceptionLevel(str, Enum):
    SCHEMA = "schema"
    PROCESS = "process"
    BUSINESS = "business"


class PolicyAction(str, Enum):
    HALT = "halt"
    WARN = "warn"
    REJECT = "reject"
    SKIP = "skip"
    FLAG_FOR_REVIEW = "flag_for_review"
    WARN_AND_CONTINUE = "warn_continue"


@dataclass
class ExceptionEvent:
    level: ExceptionLevel
    rule: str
    detail: str
    period: Optional[str] = None
    value: Optional[Any] = None


def apply_policy(event: ExceptionEvent, policy: Dict[str, Any]) -> PolicyAction:
    """
    Determine the PolicyAction for an exception event given a policy dict.
    Returns FLAG_FOR_REVIEW for undeclared rules (conservative default).
    """
    level_policy = policy.get(event.level.value, {})
    rule_config = level_policy.get(event.rule)

    if rule_config is None:
        return PolicyAction.FLAG_FOR_REVIEW

    # Special handling: reconcile_gap uses tolerance + separate over-tolerance action
    if event.rule == "reconcile_gap" and event.value is not None:
        tolerance = rule_config.get("tolerance_krw", 0)
        if event.value <= tolerance:
            return PolicyAction.WARN
        action_str = rule_config.get("action_over_tolerance", "halt")
    else:
        action_str = rule_config.get("action", "flag_for_review")

    try:
        return PolicyAction(action_str)
    except ValueError:
        return PolicyAction.FLAG_FOR_REVIEW
```

- [ ] **Step 5: Create `universal_os/exception/ledger.py`**

```python
"""ExceptionLedger — append-only JSONL log for all exception events."""
from __future__ import annotations
import json
from datetime import datetime, timezone
from pathlib import Path

from universal_os.exception.policy import ExceptionEvent


class ExceptionLedger:
    def __init__(self, path: Path) -> None:
        self._path = Path(path)
        self._path.parent.mkdir(parents=True, exist_ok=True)

    def append(self, event: ExceptionEvent) -> None:
        record = {
            "timestamp": datetime.now(tz=timezone.utc).isoformat(),
            "level": event.level.value,
            "rule": event.rule,
            "detail": event.detail,
            "period": event.period,
            "value": event.value,
        }
        with open(self._path, "a", encoding="utf-8") as f:
            f.write(json.dumps(record, ensure_ascii=False) + "\n")
```

- [ ] **Step 6: Run tests to verify they pass**

```bash
python -m pytest tests/test_exception_policy.py tests/test_exception_ledger.py -v
```
Expected: All 7 tests PASS

- [ ] **Step 7: Commit**

```bash
git add universal_os/exception/ tests/test_exception_policy.py tests/test_exception_ledger.py
git commit -m "feat(exception): policy engine + append-only ledger"
```

---

## Task 8: Ontology registry auto-registration (universal_os/ontology/registry.py)

Scans all process config YAML files in an OS directory and generates `knowledge/ontology/registry.yaml`. This is the "Phase 1" ontology — Object Types and their attributes only. Links and Actions are Phase 2.

**Files:**
- Create: `universal_os/ontology/__init__.py`
- Create: `universal_os/ontology/registry.py`
- Create: `tests/test_ontology_registry.py`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_ontology_registry.py
import tempfile
import shutil
from pathlib import Path

import pytest
import yaml
from universal_os.ontology.registry import OntologyRegistry, scan_and_register

FIXTURES = Path(__file__).parent / "fixtures"


def test_scan_finds_process_configs(tmp_path):
    """Registry scan finds all config.yaml files under processes/."""
    # Arrange: create a processes/ structure with a config
    processes_dir = tmp_path / "processes" / "finance"
    processes_dir.mkdir(parents=True)
    shutil.copy(FIXTURES / "valid_process_config.yaml", processes_dir / "config.yaml")

    registry = OntologyRegistry()
    registry.scan(tmp_path)
    assert "record_to_report" in registry.process_types


def test_scan_generates_registry_yaml(tmp_path):
    """scan_and_register writes knowledge/ontology/registry.yaml."""
    processes_dir = tmp_path / "processes" / "finance"
    processes_dir.mkdir(parents=True)
    shutil.copy(FIXTURES / "valid_process_config.yaml", processes_dir / "config.yaml")

    output_path = tmp_path / "knowledge" / "ontology" / "registry.yaml"
    scan_and_register(tmp_path, output_path)

    assert output_path.exists()
    data = yaml.safe_load(output_path.read_text())
    assert "process_types" in data
    assert "record_to_report" in data["process_types"]


def test_registry_includes_object_types(tmp_path):
    """registry.yaml should declare the object types used by each process."""
    processes_dir = tmp_path / "processes" / "finance"
    processes_dir.mkdir(parents=True)
    shutil.copy(FIXTURES / "valid_process_config.yaml", processes_dir / "config.yaml")

    output_path = tmp_path / "knowledge" / "ontology" / "registry.yaml"
    scan_and_register(tmp_path, output_path)

    data = yaml.safe_load(output_path.read_text())
    # record_to_report works with JournalEntry and FinancialMetrics
    object_types = [o["name"] for o in data.get("object_types", [])]
    assert "JournalEntry" in object_types
    assert "FinancialMetrics" in object_types


def test_registry_diff_detects_new_process(tmp_path):
    """Registry diff shows additions."""
    processes_dir = tmp_path / "processes" / "finance"
    processes_dir.mkdir(parents=True)
    shutil.copy(FIXTURES / "valid_process_config.yaml", processes_dir / "config.yaml")

    output_path = tmp_path / "knowledge" / "ontology" / "registry.yaml"
    scan_and_register(tmp_path, output_path)

    # Write initial registry
    old_data = yaml.safe_load(output_path.read_text())

    # Add a second process
    processes_dir2 = tmp_path / "processes" / "wellness"
    processes_dir2.mkdir(parents=True)
    wellness_config = {
        "process_type": "aggregate",
        "version": "1.0",
        "loop": {"perceive": {}, "act": {}, "reflect": {}},
    }
    (processes_dir2 / "config.yaml").write_text(yaml.dump(wellness_config))

    scan_and_register(tmp_path, output_path)
    new_data = yaml.safe_load(output_path.read_text())

    old_types = set(old_data["process_types"])
    new_types = set(new_data["process_types"])
    assert "aggregate" in new_types - old_types
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_ontology_registry.py -v
```
Expected: `FAILED — ImportError`

- [ ] **Step 3: Create `universal_os/ontology/__init__.py`** (empty)

- [ ] **Step 4: Create `universal_os/ontology/registry.py`**

```python
"""
Ontology Registry — Phase 1.
Scans process config YAML files and generates knowledge/ontology/registry.yaml.
Object Types and their attributes only. Links and Actions are Phase 2.
"""
from __future__ import annotations
from dataclasses import dataclass, field
from pathlib import Path
from typing import Any, Dict, List

import yaml

# Fixed mapping: process_type → object types it produces/consumes
# New process types extend this dict or declare via universalos register --script
_PROCESS_TYPE_OBJECTS: Dict[str, List[str]] = {
    "record_to_report": ["JournalEntry", "AssetSnapshot", "FinancialMetrics", "Statement"],
    "log_entry": ["JournalEntry"],
    "aggregate": ["WorkoutLog", "WellnessMetrics"],
    "plan": ["Task", "WeeklyPlan"],
    "bridge": ["Task"],
}

_OBJECT_TYPE_ATTRS: Dict[str, List[str]] = {
    "Task": ["id", "title", "status", "priority", "axis", "owner", "created_at", "skill_tags"],
    "JournalEntry": ["doc_id", "module", "period", "direction", "amount_krw", "status"],
    "AssetSnapshot": ["doc_id", "period", "account_id", "balance_krw"],
    "FinancialMetrics": ["period", "net_income", "savings_rate", "capital"],
    "Statement": ["period", "format", "path"],
    "WorkoutLog": ["date", "exercise", "sets", "reps"],
    "WellnessMetrics": ["period", "total_volume", "body_weight"],
    "WeeklyPlan": ["week", "run_budget_h", "build_budget_h"],
}


@dataclass
class OntologyRegistry:
    process_types: Dict[str, Any] = field(default_factory=dict)
    object_types: List[Dict[str, Any]] = field(default_factory=list)

    def scan(self, os_root: Path) -> None:
        """Scan all processes/*/config.yaml files under os_root."""
        config_files = list((os_root / "processes").glob("*/config.yaml"))
        for cfg_path in config_files:
            try:
                raw = yaml.safe_load(cfg_path.read_text(encoding="utf-8"))
                ptype = raw.get("process_type")
                if ptype:
                    self.process_types[ptype] = {
                        "version": str(raw.get("version", "1.0")),
                        "config_path": str(cfg_path.relative_to(os_root)),
                        "object_types": _PROCESS_TYPE_OBJECTS.get(ptype, []),
                    }
            except Exception:
                continue

        # Build de-duplicated object_types list
        seen = set()
        for pinfo in self.process_types.values():
            for obj_name in pinfo["object_types"]:
                if obj_name not in seen:
                    seen.add(obj_name)
                    self.object_types.append({
                        "name": obj_name,
                        "attributes": _OBJECT_TYPE_ATTRS.get(obj_name, []),
                    })


def scan_and_register(os_root: Path, output_path: Path) -> None:
    """Scan configs and write registry.yaml to output_path."""
    registry = OntologyRegistry()
    registry.scan(os_root)

    output_path.parent.mkdir(parents=True, exist_ok=True)
    data = {
        "generated_by": "universalos register",
        "process_types": registry.process_types,
        "object_types": registry.object_types,
    }
    output_path.write_text(
        yaml.dump(data, allow_unicode=True, sort_keys=False),
        encoding="utf-8",
    )
```

- [ ] **Step 5: Run tests to verify they pass**

```bash
python -m pytest tests/test_ontology_registry.py -v
```
Expected: All 4 tests PASS

- [ ] **Step 6: Commit**

```bash
git add universal_os/ontology/ tests/test_ontology_registry.py
git commit -m "feat(ontology): registry scanner — scan configs → registry.yaml (Phase 1)"
```

---

## Task 9: CLI — `universalos validate | init | register`

**Files:**
- Create: `universal_os/cli/__init__.py`
- Create: `universal_os/cli/main.py`
- Create: `tests/test_cli.py`

- [ ] **Step 1: Write the failing tests**

```python
# tests/test_cli.py
import tempfile
import shutil
from pathlib import Path

import pytest
from click.testing import CliRunner
from universal_os.cli.main import cli

FIXTURES = Path(__file__).parent / "fixtures"


def test_cli_validate_passes_on_valid_config(tmp_path):
    """universalos validate <config> exits 0 on valid config."""
    shutil.copy(FIXTURES / "valid_process_config.yaml", tmp_path / "config.yaml")
    runner = CliRunner()
    result = runner.invoke(cli, ["validate", str(tmp_path / "config.yaml")])
    assert result.exit_code == 0
    assert "valid" in result.output.lower()


def test_cli_validate_fails_on_missing_file():
    runner = CliRunner()
    result = runner.invoke(cli, ["validate", "/tmp/no_such_file.yaml"])
    assert result.exit_code != 0


def test_cli_init_creates_directory_structure(tmp_path):
    """universalos init lifeos <path> scaffolds the expected directories."""
    runner = CliRunner()
    result = runner.invoke(cli, ["init", "lifeos", str(tmp_path / "my_lifeos")])
    assert result.exit_code == 0
    target = tmp_path / "my_lifeos"
    for expected_dir in ["system", "data/snapshots", "data/inputs", "processes", "knowledge", "memory"]:
        assert (target / expected_dir).exists(), f"Missing: {expected_dir}"


def test_cli_register_generates_registry(tmp_path):
    """universalos register <os_root> generates knowledge/ontology/registry.yaml."""
    processes_dir = tmp_path / "processes" / "finance"
    processes_dir.mkdir(parents=True)
    shutil.copy(FIXTURES / "valid_process_config.yaml", processes_dir / "config.yaml")

    runner = CliRunner()
    result = runner.invoke(cli, ["register", str(tmp_path)])
    assert result.exit_code == 0
    assert (tmp_path / "knowledge" / "ontology" / "registry.yaml").exists()


def test_cli_help_shows_commands():
    runner = CliRunner()
    result = runner.invoke(cli, ["--help"])
    assert result.exit_code == 0
    assert "validate" in result.output
    assert "init" in result.output
    assert "register" in result.output
```

- [ ] **Step 2: Run tests to verify they fail**

```bash
python -m pytest tests/test_cli.py -v
```
Expected: `FAILED — ImportError`

- [ ] **Step 3: Create `universal_os/cli/__init__.py`** (empty)

- [ ] **Step 4: Create `universal_os/cli/main.py`**

```python
"""universalos CLI — validate | init | register."""
from __future__ import annotations
from pathlib import Path

import click

from universal_os.config.loader import load_process_config
from universal_os.config.validator import validate_config
from universal_os.ontology.registry import scan_and_register

_LIFEOS_DIRS = [
    "system/capitals",
    "system/goals",
    "data/snapshots",
    "data/inputs",
    "data/outputs",
    "processes",
    "knowledge/topics",
    "knowledge/insights",
    "knowledge/ontology",
    "memory",
    ".claude/agents",
]

_WORKOS_DIRS = [
    "system",
    "data/snapshots",
    "data/inputs",
    "data/outputs",
    "processes",
    "knowledge",
    "memory",
    ".claude/agents",
]

_OS_SCAFFOLD: dict = {
    "lifeos": _LIFEOS_DIRS,
    "workos": _WORKOS_DIRS,
}


@click.group()
def cli():
    """Universal OS CLI — shared schema, config validation, and ontology tooling."""
    pass


@cli.command()
@click.argument("config_path", type=click.Path())
def validate(config_path: str):
    """Validate a process config YAML file."""
    try:
        cfg = load_process_config(Path(config_path))
        result = validate_config(cfg, installed_version="0.1.0")
        if result.valid:
            click.echo(f"✅ Config valid: {config_path}")
            if result.warnings:
                for w in result.warnings:
                    click.echo(f"  ⚠️  {w}")
        else:
            click.echo(f"❌ Config invalid: {config_path}")
            for e in result.errors:
                click.echo(f"  error: {e}")
            raise SystemExit(1)
    except FileNotFoundError as exc:
        click.echo(f"Error: {exc}", err=True)
        raise SystemExit(1)


@cli.command()
@click.argument("os_type", type=click.Choice(["lifeos", "workos"]))
@click.argument("target_path", type=click.Path())
def init(os_type: str, target_path: str):
    """Scaffold a new OS directory structure."""
    target = Path(target_path)
    dirs = _OS_SCAFFOLD[os_type]
    for d in dirs:
        (target / d).mkdir(parents=True, exist_ok=True)
    # Write a minimal system/principles.md placeholder
    principles = target / "system" / "principles.md"
    if not principles.exists():
        principles.write_text(
            f"# Principles\n\n> Define the operating principle for this {os_type} instance.\n",
            encoding="utf-8",
        )
    click.echo(f"✅ Initialized {os_type} structure at: {target}")
    for d in dirs:
        click.echo(f"  created: {d}/")


@cli.command()
@click.argument("os_root", type=click.Path())
def register(os_root: str):
    """Scan process configs and regenerate the ontology registry."""
    root = Path(os_root)
    output = root / "knowledge" / "ontology" / "registry.yaml"
    scan_and_register(root, output)
    click.echo(f"✅ Ontology registry written to: {output}")
```

- [ ] **Step 5: Run tests to verify they pass**

```bash
python -m pytest tests/test_cli.py -v
```
Expected: All 5 tests PASS

- [ ] **Step 6: Verify CLI entry point works**

```bash
universalos --help
```
Expected:
```
Usage: universalos [OPTIONS] COMMAND [ARGS]...

  Universal OS CLI — shared schema, config validation, and ontology tooling.

Options:
  --help  Show this message and exit.

Commands:
  init      Scaffold a new OS directory structure.
  register  Scan process configs and regenerate the ontology registry.
  validate  Validate a process config YAML file.
```

- [ ] **Step 7: Commit**

```bash
git add universal_os/cli/ tests/test_cli.py
git commit -m "feat(cli): universalos validate | init | register"
```

---

## Task 10: Full test suite run + README + push

- [ ] **Step 1: Run full test suite**

```bash
cd "/Users/I340352/Library/CloudStorage/GoogleDrive-jinwan.hyun@gmail.com/My Drive/UniversalOS"
python -m pytest tests/ -v --tb=short
```
Expected: All tests PASS. If any fail, fix them before continuing.

- [ ] **Step 2: Verify pip install from GitHub (end-to-end check)**

```bash
# In a separate temp venv to verify clean install
python3 -m venv /tmp/uos_test_env
source /tmp/uos_test_env/bin/activate
pip install "git+https://github.com/Jintheexplorer/universal-os.git"
universalos --help
python -c "from universal_os.schema.task import Task, TaskAxis; print('✅ import ok')"
deactivate
rm -rf /tmp/uos_test_env
```
Expected: Install succeeds, `universalos --help` prints usage, import succeeds.

- [ ] **Step 3: Create README.md**

```markdown
# Universal OS Framework

AI-native personal operating system framework. Shared schema, process loop contracts, and CLI for LifeOS and WorkOS instances.

## Install

```bash
pip install git+https://github.com/Jintheexplorer/universal-os.git
```

## What's in this package

| Module | Purpose |
|--------|---------|
| `universal_os.schema.task` | `Task` v1.0 — axis, priority, Professional Capital fields |
| `universal_os.schema.journal` | `JournalEntry` — universal journal schema |
| `universal_os.schema.asset` | `AssetSnapshot`, `Account` |
| `universal_os.config` | `load_process_config()`, `validate_config()` |
| `universal_os.process_types.base` | `BaseProcess` — PERCEIVE/PLAN/ACT/REFLECT contract |
| `universal_os.process_types.bridge` | `BridgeProtocol` — WorkOS→LifeOS transmission |
| `universal_os.exception` | `apply_policy()`, `ExceptionLedger` |
| `universal_os.ontology.registry` | `scan_and_register()` — auto-generate ontology YAML |

## CLI

```bash
universalos validate processes/finance/config.yaml   # validate process config
universalos init lifeos ./my_lifeos                  # scaffold directory structure
universalos register .                               # regenerate ontology registry
```

## Design

See [FRAMEWORK.md](FRAMEWORK.md) for the full Universal OS Framework specification (DRAFT v7).

**Architecture:** 5-layer system (L1 Principle → L5 Execution) with an AI-Native 4-stage loop (PERCEIVE → PLAN → ACT → REFLECT). Two pillars: deterministic (this Python package) + probabilistic (Claude Code SKILL.md). See FRAMEWORK.md §L5 for the full spec.

**Two OS instances:**
- **LifeOS** — personal capital management (Economic, Professional, Humanistic, Wellness)
- **WorkOS** — professional execution environment (Professional Capital only)

**Bridge:** WorkOS → GDrive → LifeOS (one-way, Universal Task Schema, no translation layer).
```

- [ ] **Step 4: Commit README and final push**

```bash
git add README.md
git commit -m "docs: README — install, modules, CLI reference, architecture overview"
git push origin main
```

- [ ] **Step 5: Verify on GitHub**

Visit `https://github.com/Jintheexplorer/universal-os` and confirm:
- README renders correctly
- All 10 task commits visible in history
- `pyproject.toml` present (enables `pip install git+...`)

---

## Self-Review

### 1. Spec coverage check

| FRAMEWORK.md requirement | Covered in plan? |
|--------------------------|-----------------|
| Universal Task Schema v1.0 (axis, priority, skill_tags) | ✅ Task 2 |
| JournalEntry + AssetSnapshot schemas | ✅ Task 3 |
| Process config YAML loading | ✅ Task 4 |
| Config validator (unknown stages) | ✅ Task 4 |
| BaseProcess 4-stage loop (PERCEIVE/PLAN/ACT/REFLECT) | ✅ Task 5 |
| Stage halt on failure | ✅ Task 5 |
| Bridge protocol (serialize + write + read + stale) | ✅ Task 6 |
| Exception policy (3 levels + tolerance) | ✅ Task 7 |
| Exception ledger (append-only JSONL) | ✅ Task 7 |
| Ontology registry auto-scan | ✅ Task 8 |
| CLI: validate | ✅ Task 9 |
| CLI: init (directory scaffold) | ✅ Task 9 |
| CLI: register | ✅ Task 9 |
| pip install from GitHub | ✅ Task 1 + Task 10 |
| README | ✅ Task 10 |
| process_types/record_to_report.py (concrete) | ⚠️ Deferred to Plan B — LifeOS already has a working impl |
| config versioning (reprocess, diff-config) | ⚠️ Deferred to Phase 2 |
| ontology graph (Links, Actions) | ⚠️ Deferred to Phase 2 |

**Deferred items** are explicitly noted as Phase 2 in FRAMEWORK.md's Status table.

### 2. Placeholder scan

No TBDs, TODOs, or "similar to" references found. All code blocks are complete.

### 3. Type consistency

- `Task.to_dict()` uses `dataclasses.asdict()` → returns plain dict with enum values as objects. Serialized via `d[key] = d[key].value` for status/priority/axis. `from_dict()` reconstructs via `cls(**data)` → `__post_init__` coerces strings back to enums. ✅ Consistent.
- `BridgeProtocol.write()` calls `t.to_dict()` — confirmed Task.to_dict() exists in Task 2. ✅
- `validate_config()` takes `ProcessConfig` (defined in loader.py Task 4). ✅
- `OntologyRegistry.scan()` reads YAML directly — no dependency on `load_process_config`. ✅ (independent)
- CLI `validate` command calls `load_process_config` + `validate_config` — both defined in Task 4. ✅
- CLI `register` command calls `scan_and_register` — defined in Task 8. ✅
