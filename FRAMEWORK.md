# Universal OS Framework

> A shared philosophical foundation for building AI-native personal operating systems.

Version: DRAFT v7
Date: 2026-06-05
Status: Design phase — pre-implementation

---

## What Is This

Most productivity systems answer "how do you get things done."
This framework answers something harder: **"who are you, and what are you building — and how does that shape how you act every day?"**

Universal OS Framework is a **layered architecture** for building AI-native personal operating systems — software that manages your life and work not as a task list, but as a coherent expression of who you are and what you are building toward.

It defines five abstraction layers (L1–L5). Each layer answers one question, builds on the layer above, and narrows down to concrete execution.

**Scope rule:** This document defines the system-level spec only — universal abstractions that any OS instance must implement. Personal data, instance values, and OS-specific configurations live in each OS's own `system/` directory. Variant tables in this document show the *structure* each instance must define; example values are explicitly labeled as such.

Two systems currently run on this framework:
- **LifeOS** — personal operating system for life capital management
- **WorkOS** — professional operating system for career and work execution

New OS instances extend the framework by adding a Variant column. No framework sections need to change.

---

## The Core Equation

```
SSOT  +  Process & Architecture  +  Closed Learning Loop
  =  System of Context + Action
```

An AI agent grounded in all three doesn't just execute — it understands context, follows defined processes, and improves over time. Remove any one and the system degrades:

| Missing | What breaks |
|---------|-------------|
| **SSOT** | Inconsistent facts; agents act on stale or hallucinated state |
| **Process & Architecture** | Arbitrary execution; no reproducibility, no composability |
| **Closed Learning Loop** | Repeated mistakes; no compounding; system stays static |

**SSOT (Single Source of Truth)**
Every entity has one authoritative source. No duplicate truth. Agents read from snapshots; they do not reconstruct state from conversation history.

**Process & Architecture**
Work is structured as defined processes, not ad-hoc commands. Architecture creates the stable skeleton inside which processes run reliably.

**Closed Learning Loop**
Every cycle must close: `plan → act → record → distill → improve → next plan`. A system that does not close its loop cannot compound.

**System of Context + Action**
The output. An AI-native OS that acts on grounded understanding, not assumption. Context = who you are + what matters now + what has happened. Action = what to do next, and why.

---

## The Five Layers

```
L1  Principle      Why does this system exist?          (Operating contract — most stable)
L2  Identity       Who is the person running it?        (Self-definition — stable)
L3  Purpose        Where are they going?                (Vision + Mission — changes at life transitions)
L4  Capital        What are they building?              (Resource types + measurement — evolves slowly)
L5  Execution      How does it run, learn, improve?     (Process + data + loop — changes often)
```

Each layer answers one question. Each layer is defined at two levels: a **system spec** (universal, abstract, in this document) and an **instance variant** (OS-specific concrete values, in each OS's `system/` directory).

**Why this order matters:**
- L1 is the most stable: if two decisions conflict, L1 wins.
- L5 changes most often: tooling, processes, and data evolve continuously.
- L3 is the destination; L5 is the path. Confusing the two produces a system that changes its direction every week.

---

## L1 — Principle

**System:** The operating principle of the OS — its reason for existing. Everything flows from this. Stable enough to resolve conflicts between lower layers.

**Rule:** Only humans change this layer. No automated modification.

**Conflict resolution proxy:** When two decisions conflict, ask:
1. Does this generate or protect capital?
2. Does it serve the Purpose defined at L3?
3. Does it preserve SSOT independence for each OS?

### Variants

| Variant dimension | LifeOS | WorkOS |
|-------------------|--------|--------|
| Operating principle | → `system/principles.md` | → `system/principles.md` |

---

## L2 — Identity

**System:** A structured definition of who the person is — values, strengths, and orientation. Defined as **vocation** (calling), not occupation (job title).

**Structure of an Identity definition:**
- **Driving question:** "What am I here to do?"
- **Core values:** The principles that guide decisions under uncertainty
- **Core beliefs:** What you hold to be true about the world
- **Development stage:** Current phase of professional identity evolution

**Rule:** Stored in each OS's `system/identity.md`. Propose-and-confirm only. AI cannot modify unilaterally.

**Relationship between instances:**
```
Whole-person Identity (LifeOS)
  └── Professional Identity (WorkOS) — how the same self shows up at work
```

### Variants

| Variant dimension | LifeOS | WorkOS |
|-------------------|--------|--------|
| Scope | Whole person | Professional expression |
| File | `system/identity.md` | `system/identity.md` |
| Status | ✅ written | ❌ not yet written |

---

## L3 — Purpose

**System:** Where the person is going — their long-range direction. Purpose is the bridge between Identity (who you are) and Execution (what you do daily). It defines the *destination*; Execution defines the *path*.

**Structure of a Purpose definition:**
- **Vision:** The world you are trying to create or contribute to
- **Mission:** How you specifically contribute — your unique method
- **Roadmap:** The progression of phases through which you pursue the mission

**Rule:** Purpose is stable across years. It changes at genuine life transitions — career pivots, major value reassessments — not at weekly planning cycles.

### Variants

| Variant dimension | LifeOS | WorkOS |
|-------------------|--------|--------|
| Vision | → `system/purpose.md` | → `system/purpose.md` |
| Mission | → `system/purpose.md` | → `system/purpose.md` |
| Roadmap | → `system/purpose.md` | → `system/purpose.md` |

**Roadmap structure (instance-defined):**
Each OS instance defines a roadmap as an ordered progression of stages. Each stage specifies what you focus on and what you build toward. The number of stages and their names are instance-specific — defined in `system/purpose.md`, not in this document.

---

## L4 — Capital

**System:** Capital is anything that compounds. Invested wisely, it grows. Neglected, it decays. Every OS defines:
1. Which capital types it manages
2. How accumulation is measured
3. Where the SSOT snapshot lives

**Measurement principle:** Self-assessment is noise. Evidence is signal. Capital is measured by artifacts — things you made, delivered, or had recognized externally.

### Capital Configuration (Variant)

Each OS instance defines its capital portfolio in `system/capitals/`. Capital types are extensible — any OS can define additional capitals by adding a variant entry.

| Variant dimension | LifeOS | WorkOS |
|-------------------|--------|--------|
| Capital portfolio | Economic, Professional, Humanistic, Wellness | Professional (execution environment only) |
| Snapshot files | `data/snapshots/{capital}.json` per capital | `data/snapshots/professional.json` |
| Capital philosophy | `system/capitals/{name}.md` per capital | `system/capitals/professional.md` |

**WorkOS and Professional Capital:**
WorkOS is a Professional Capital engine. It generates raw material — completed work, applied skill, delivered artifacts — that LifeOS aggregates into the Professional Capital snapshot. Other capitals (Economic, Humanistic, Wellness) are LifeOS-managed.

---

### Professional Capital: The Three Career Assets

Professional Capital is built from three compounding asset types. This spec applies universally — any OS that manages Professional Capital implements these three assets.

#### 1. Skill — *"What can I do?"*

Discrete, demonstrable abilities — what you can actually perform, not merely understand.

**Skill ≠ Knowledge.** Skill is executable capacity. Knowledge is conceptual understanding. Both are required; they compound differently and are tracked separately.

**Measurement:** 4-level proficiency scale

| Level | Label | What it looks like |
|-------|-------|-------------------|
| 1 | **Learning** | Understands the concept; executes with supervision |
| 2 | **Applying** | Executes independently in standard situations |
| 3 | **Leading** | Handles complexity; can guide and develop others |
| 4 | **Defining** | Sets direction and standards; externally recognized |

**Domains:** Technical (tools, engineering, data, systems, AI) · Business (strategy, GTM, finance, operations, product)

**Decay:** Technical ~2–3 years · Business ~5–7 years · Always validate with evidence.

---

#### 2. Experience — *"What have I done?"*

A portfolio of verifiable evidence. Where Skill was applied at scale under real conditions.

**Measurement:** Complexity × Impact matrix

|  | Low Complexity | High Complexity |
|--|---------------|-----------------|
| **High Impact** | Leverage (more with less) | Peak work |
| **Low Impact** | Learning | Demonstrated capability |

**Complexity:** S (solo, clear scope) / M (cross-functional) / L (strategic, org-level) / XL (greenfield)
**Impact:** people affected, business value, scope of change

---

#### 3. Influence — *"Who knows me for what?"*

**Thought Leadership** — the degree to which your perspective shapes how others in a domain think and act.

**Measurement:** 3R model

| R | Signal | How to measure |
|---|--------|----------------|
| **Reach** | How many people your thinking touches | Audiences, article views, network size |
| **Recognition** | How often you're called on as the expert | Inbound requests, citations, invitations |
| **Reputation** | Whether a domain is associated with your name | "When I think of X, I think of you" |

**Career stage:** Evangelist (Reach) → Architect (Recognition) → Entrepreneur (Reputation)

---

### How the Three Assets Connect (70-20-10)

```
Skill          ← 10% formal learning  +  70% applied practice
Experience     ← 70% doing (every completed task is evidence)
Influence      ← 20% relationships, community, and public output
```

Every completed task generates signals: `skill_tags` + `skill_domain` → Skill · `output_link` + `complexity` → Experience · `audience` + `delivery_channel` → Influence.

---

## L5 — Execution

### 5a. The AI-Native Operating Loop

**System:** How the OS runs, cycle to cycle.

**The fundamental structure is two pillars:**

| Pillar | Nature | What it does |
|--------|--------|-------------|
| **Deterministic** | Python runtime | Validate, calculate, store, enforce schema. Reproducible, auditable, exact. |
| **Probabilistic** | AI (Claude Code Runtime) | Predict, simulate, reason, generate, improve. Context-sensitive, generative, uncertain. |

Traditional PDCA was almost entirely deterministic — humans supplied the probabilistic judgment manually. AI-Native adds a probabilistic layer that runs alongside and accelerates each stage: pattern detection without fatigue, multi-variable simulation before committing, systematic learning from every outcome.

**The AI-Native loop is not a replacement for PDCA. It is PDCA accelerated and extended.**

---

#### Four-Stage Macro Loop

The loop has four macro stages. Every process maps to this structure — the weight of each stage varies by process type.

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
│ anomalies,  │    │ scenarios,   │    │ exceptions,  │    │ insights,    │
│ forecast    │    │ recommend    │    │ handle edge  │    │ SkillOpt     │
│             │    │ allocation   │    │ cases        │    │              │
└──────────────┘    └──────────────┘    └──────────────┘    └──────────────┘
       │                   ▲                                        │
       └───────────────────┴────────────────────────────────────────┘
                              closed loop
```

**Stage definitions:**

**① PERCEIVE** — Collect and validate context before deciding anything.
- Deterministic: load SSOT files, validate schema, run `check_missing`, compute opening state
- Probabilistic: detect anomalies, flag unusual patterns, forecast missing data

**② PLAN** — Decide what to do, at two time horizons.
- *Strategic* (중장기): scenario modeling, capital growth simulation, long-range allocation — "If savings rate stays at 52%, when does net worth reach X?"
- *Tactical/Operational* (단기): prioritize tasks, allocate this week's Run/Build budget, schedule based on signals from PERCEIVE — "Given current backlog and signals, what runs today?"
- Deterministic: compute priorities (CR/WSPT), apply constraints from `system/principles.md`
- Probabilistic: simulate scenarios, recommend decisions, model tradeoffs; human confirms before committing (`hitl: required` for high-stakes plans)

**③ ACT** — Execute and record with precision.
- Deterministic: execute calculation, store to SSOT, enforce period lock, apply exception policy
- Probabilistic: resolve ambiguous exceptions, handle edge cases outside declared config

**④ REFLECT** — Extract meaning and improve the system.
- Deterministic: update history files, generate structured reports
- Probabilistic: extract narrative insights, identify patterns across periods, SkillOpt

**PDCA correspondence:**

| PDCA | AI-Native stage | What AI adds |
|------|-----------------|-------------|
| Plan | PERCEIVE + PLAN | Anomaly detection, scenario simulation, multi-variable optimization |
| Do | ACT | Exception reasoning, edge-case resolution |
| Check | REFLECT (det) | Automated reconciliation, structured report generation |
| Act-on-system | REFLECT (prob) | SkillOpt: the system improves its own processes |

---

#### Stage Weight by Process Type

The four stages are always present in the structure. Their weight varies — a process declares how much of its logic lives in each stage:

| Process type | PERCEIVE | PLAN | ACT | REFLECT |
|-------------|----------|------|-----|---------|
| `record_to_report` (월 결산) | heavy | minimal | heavy | medium |
| `weekly_plan` (주간 계획) | medium | heavy | light | medium |
| `log_entry` (이벤트 로그) | light | none | heavy | none |
| `financial_simulation` (중장기 시뮬) | medium | heavy | none | medium |
| `distill` (주간 루프 닫기) | light | none | light | heavy |

A `log_entry` process activates PERCEIVE + ACT only — PLAN and REFLECT are disabled. A `financial_simulation` process activates PERCEIVE + PLAN + REFLECT — there is no ACT because simulation doesn't write to SSOT.

---

#### Runtime: Claude Code

**Claude Code is the Runtime and Orchestrator.** This is a fixed premise.

Claude Code reads `universalos.yaml`, routes to each stage's components in sequence, calls Python scripts as deterministic tools (Bash/Read/Write), and executes SKILL.md directives as probabilistic nodes. The four-stage loop runs inside a Claude Code session — not on a separate server, not via an external graph execution engine.

This means:
- **State** = SSOT files (`data/snapshots/*.json`). Single source of truth. No second state layer.
- **Conditional routing** = declared in config via `condition:` fields; Claude Code evaluates and routes.
- **Human-in-the-loop** = Claude Code's natural conversation. `hitl: required` means Claude Code pauses and asks before proceeding — no interrupt API needed.
- **Error recovery** = `on_error:` policy per component. Claude Code applies the policy and reports.

**Why not LangGraph:** LangGraph adoption requires replacing Claude Code as Runtime — LangGraph becomes the graph executor and Claude becomes one API-called LLM node inside it. This breaks the SKILL.md-based probabilistic layer, eliminates built-in tools (Bash, Read, Write), and introduces a second state layer that conflicts with file-based SSOT. The architectural tradeoff is Runtime replacement, not a simple addition.

---

#### Component Model

Each stage is composed of **components** — discrete units with their own config. Two types:

| Type | Provider | Ontology auto-register | Use when |
|------|----------|----------------------|----------|
| `component:` | Universal OS standard library | ✅ automatic | Standard logic covered by Universal OS |
| `script:` | Each OS (escape hatch) | ⚠️ manual declaration | Domain-specific logic, custom calculations |

**Standard components** are provided by Universal OS (`universal_os/process_types/`). They accept a `config:` block to parameterize their behavior — no hardcoding inside the component.

**Script escape hatch** allows any Python script to be a loop node. The OS owns and maintains it. Scripts that appear in `universalos.yaml` must declare their ontology entry manually via `universalos register --script`.

Both types support `on_error:` policy: `halt | warn_continue | skip | flag_for_review`.

---

#### Loop Configuration

```yaml
# Example: record_to_report process (월 결산)
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

      - component: load_assets
        config:
          source: data/inputs/finance/assets.csv

    probabilistic:
      - component: detect_anomalies
        config:
          skill: finance.md#perceive
          threshold_sigma: 2.0
          on_anomaly: warn_and_continue

  plan:
    weight: minimal
    deterministic:
      - component: validate_period
        config:
          format: YYYY-MM
          on_error: halt

    probabilistic:
      enabled: false            # 월 결산 — 계획 단계 thin

  act:
    weight: heavy
    deterministic:
      - component: calculate
        config:
          aggregations: [pl, bs, cf, reconcile]
          cf_classification:
            operating: [PAYROLL_IN, CARD_OUT, INSURANCE_OUT, FIXED_OUT, TAX_OUT]
            investing:  [INVEST_BUY_OUT, INVEST_SELL_IN, PENSION_OUT, SAVINGS_OUT]
            financing:  [LOAN_IN, LOAN_REPAY_OUT, INTEREST_OUT]
          expense_categories:
            wants: [프리미엄 선호, 일상 선호]
            needs: [생필품, 고정비, 교통]
          kpi_targets:
            savings_rate: 0.50
            reconcile_gap_tolerance_krw: 10000

      - script:
          path: processes/finance/pension_reconcile.py
          args: [--period, "{period}"]
          on_error: warn_continue
          output_key: pension_check   # Claude Code가 결과를 state에 보관

      - component: store_snapshot
        config:
          target: data/snapshots/economic.json
          merge_strategy: overwrite

      - component: period_lock
        config:
          on_flag: --lock
          condition: "pension_check.gap_krw < 10000"
          on_error: halt

    probabilistic:
      - component: resolve_exceptions
        config:
          skill: finance.md#act
          hitl: required            # lock 전 Claude Code가 사용자 확인
          condition: "act.has_exceptions == true"

  reflect:
    weight: medium
    deterministic:
      - component: update_history
        config:
          targets: [income, expense, asset, cashflow]
          target_dir: data/outputs/finance/

      - component: generate_report
        config:
          format: markdown
          sections: [pl, bs, cf, kpi]
          output: data/outputs/finance/statement_{period}.md

    probabilistic:
      - component: extract_insights
        config:
          skill: finance.md#reflect
          output: knowledge/insights/finance-{period}.md
          skillopt: true            # REFLECT 후 finance.md SkillOpt 허용
```

```yaml
# Example: financial_simulation (중장기 시뮬 — ACT 없음, SSOT 변경 없음)
loop:
  perceive:
    weight: medium
    deterministic:
      - component: load_snapshots
        config:
          capitals: [economic]
      - component: load_history
        config:
          targets: [income, asset]

  plan:
    weight: heavy
    probabilistic:
      - component: simulate_scenarios
        config:
          skill: finance.md#simulate
          scenarios:
            savings_rate_range: [0.45, 0.50, 0.55, 0.65]
            horizon_years: [3, 5, 10]
          output: data/outputs/finance/simulation_{date}.md
          hitl: required

  act:
    enabled: false              # 시뮬은 SSOT를 변경하지 않음

  reflect:
    weight: medium
    probabilistic:
      - component: extract_insights
        config:
          skill: finance.md#reflect
          output: knowledge/insights/finance-sim-{date}.md
```

```yaml
# Example: log_entry (이벤트 로그 — PLAN·REFLECT 없음)
loop:
  perceive:
    weight: light
    deterministic:
      - component: validate_input
        config:
          schema: JournalEntry
          on_error: halt

  plan:
    enabled: false

  act:
    weight: heavy
    deterministic:
      - component: append_to_ssot
        config:
          target: data/inputs/finance/universal_journal.csv
          dedup_key: doc_id

  reflect:
    enabled: false
```

---

#### Run / Build Axis (L5 operational mode)

| Mode | Definition | Orientation |
|------|------------|-------------|
| **Run** | Realizing current capital. Operating at known competence. | Exploit — reliable execution |
| **Build** | Creating future capability. Exploring new domains. | Explore — experimental, uncertain |

`axis: "run" | "build"` is a first-class field on every task. Resource allocation between Run and Build is declared in `system/principles.md` and reviewed weekly — a *tactical operating parameter*, not a life direction.

---

#### SkillOpt — The Self-Improvement Mechanism

Triggered at the end of REFLECT stage when `skillopt: true`:
1. Scan `transcript.jsonl` for repeated failures or inefficiencies in this process
2. Identify the responsible SKILL.md
3. Rewrite with improved instructions
4. Archive the previous version before replacing

### Variants (L5a)

| Variant dimension | LifeOS | WorkOS |
|-------------------|--------|--------|
| Loop trigger | `/weekly-plan` (weekly), `/log` (event) | `/morning` (daily), task completion (event) |
| Perceive inputs | Capital snapshots, goals, calendar | Task queue, project signals |
| Act — Record target | `data/snapshots/` | `data/snapshots/tasks.json` |
| Reflect — Distill target | `knowledge/insights/` | `knowledge/` domain wiki |
| Probabilistic runtime | Claude Code | Claude Code |

---

### 5b. Exception Handling

**System:** Exceptions are inevitable — a mid-process reversal, an unrecognized transaction type, a reconcile gap. The framework defines how strictly to enforce rules and how to handle deviations without corrupting SSOT integrity.

**Core principle: Exceptions are first-class data, not error states.**

An exception is not discarded or silently bypassed — it is recorded with its reason, auditable, and resolvable. This preserves SSOT integrity while maintaining operational continuity.

---

#### Exception Levels

Three levels of exception, each with different handling:

| Level | Description | Example | Default action |
|-------|-------------|---------|---------------|
| **Schema exception** | Data doesn't match declared types or valid values | Unknown `module` value, missing required field | Reject with error; log to exception ledger |
| **Process exception** | Computation produces anomalous or unresolvable result | Reconcile gap > tolerance, missing module in required set | Configurable: halt \| warn-continue \| auto-resolve |
| **Business exception** | Intentional deviation from standard flow | Reversal entry, retroactive correction, out-of-period transaction | Explicit document status: `reversed` \| `exceptional` \| `pending` |

Business exceptions are already implemented in LifeOS Finance (`status: 'reversed'`, `status: 'pending'`). This framework formalizes the concept universally.

---

#### Exception Policy (Config-driven)

Each process declares its exception tolerance in `config.yaml`:

```yaml
exception_policy:

  # Schema exceptions — how strict is the type system?
  schema:
    unknown_enum_value:
      action: reject          # or: warn_and_bucket
      bucket_field: OTHER     # used when action=warn_and_bucket
    missing_optional_field:
      action: use_default
    missing_required_field:
      action: halt

  # Process exceptions — what constitutes a failure?
  process:
    reconcile_gap:
      tolerance_krw: 10000    # gaps within this range: warn only
      action_over_tolerance: halt   # or: warn_continue, flag_for_review
    missing_module:
      required: [INCOME, CASH]
      action: halt
    unknown_transaction_type:
      action: warn_and_bucket
      bucket: OTHER

  # Business exceptions — what document statuses are valid?
  business:
    allowed_statuses: [posted, pending, reversed, exceptional]
    retroactive_correction:
      allow: true
      requires_reason: true   # reason field must be populated
    cross_period_reversal:
      allow: true
      reprocess_trigger: true # triggers consistency reprocess when detected
```

---

#### Config Versioning and Historical Consistency

When process config changes — a new transaction type added, a reclassification of expense categories — historical records calculated under the old config may no longer match the new config. This is the consistency problem.

**Two types of config changes:**

| Change type | Historical impact | Action required |
|------------|------------------|-----------------|
| **Additive** (new type, new field) | Historical data is valid; new type just didn't exist before | No reprocess needed |
| **Reclassification** (category changes, threshold changes) | Historical KPIs calculated under old rules are now inconsistent | Reprocess required |

**Config version tracking:**

```yaml
# In process config.yaml
config_history:
  - version: "1.0"
    effective_from: "2025-01"
    change_type: initial

  - version: "1.1"
    effective_from: "2026-01"
    change_type: additive
    description: "Added CARD_SUBSCRIPTION_OUT to operating transaction types"
    reprocess_required: false

  - version: "1.2"
    effective_from: "2026-06"
    change_type: reclassification
    description: "Reclassified '일상 선호' from wants to needs"
    reprocess_required: true
    reprocess_from: "2025-01"
```

**CLI support:**

```bash
universalos validate --config-history   # detect version gaps
universalos reprocess --from 2025-01    # re-run RECORD stage from given period
universalos diff-config v1.1 v1.2       # show what changed between versions
```

**Invariant:** Raw input data (journal entries, asset snapshots) is never modified. Config changes only affect the calculation layer (RECORD stage). The original data is always reprocessable.

---

### 5c. Data Architecture

**System:** The data and knowledge infrastructure that grounds all agent actions. An AI agent without a grounded data architecture generates plausible-sounding but unreliable outputs.

**Design principles:**
The distinction between "data" and "knowledge" is collapsing. Databricks Lakehouse unifies raw data and AI-ready representations. Palantir Ontology maps raw records to semantic objects. SAP Business Data Cloud connects operational data to business semantics. The shared pattern: **raw state + semantic meaning + working context = one unified layer** — organized by *how current* and *how semantically rich*, not by format.

**Three tiers:**

```
DATA ARCHITECTURE
├── Snapshots     (operational state — current, structured, machine-readable)
│   What is true right now. Ground truth every agent reads before acting.
│   Overwritten on each update cycle. Never reconstructed from conversation.
│
├── Knowledge     (accumulated meaning — durable, semantic, human + machine)
│   What has been learned. Grows through REFLECT cycles.
│   Wiki + Patterns + Insights + Ontology.
│
└── Memory        (short-term context — session or week scope, fast-access)
    What is needed right now. Glossary, user model, active references.
    Scoped to a session or planning period.
```

---

#### Knowledge Ontology (Target Architecture)

The Knowledge tier currently operates as flat markdown — addressable by filename, not by entity relationship. The target architecture adopts Palantir's Ontology model: an object-oriented, graph-aware layer over raw data.

**Object Types:** Named entity types in the system.

```
Task        { id, title, status, axis, skill_tags, complexity }
Skill       { name, domain, level, last_validated }
Project     { id, title, capital, goal_id }
JournalEntry { doc_id, module, period, direction, amount_krw, status }
FinancialMetrics { period, net_income, savings_rate, capital, ... }
Insight     { content, period, topic, confidence }
Concept     { name, definition, domain }
Person      { name, relationship, last_interaction }
```

**Links (Relations):** Typed, directed edges.

```
Task          -[applies]→          Skill
Task          -[belongs_to]→       Project
Task          -[generates]→        Insight
JournalEntry  -[aggregates_to]→    FinancialMetrics
FinancialMetrics -[reported_by]→   Statement
Insight       -[about]→            Concept
Concept       -[related_to]→       Concept      ← graph layer: cross-domain synthesis
```

**Actions:** Operations triggered on state changes.

```
Task.complete()           → update Skill level; add Experience entry
JournalEntry.post()       → trigger PERCEIVE stage validation
FinancialMetrics.calculate() → RECORD stage: run process.py
Insight.distill()         → extract Concepts; create Links
Concept.relate()          → establish Concept-[related_to]-Concept edges
Skill.decay_check()       → flag if last_validated > decay_window
```

**Key insight — Actions map to Loop stages:**

| Loop stage | Action type | Example |
|-----------|-------------|---------|
| PERCEIVE (det) | Validation actions | `JournalEntry.validate()` |
| ACT (det) | Calculation actions | `FinancialMetrics.calculate()` |
| ACT (prob) | Simulation actions | `Scenario.simulate()` |
| REFLECT (det) | Report actions | `Statement.generate()` |
| REFLECT (prob) | Distillation actions | `Insight.distill()`, `Concept.relate()` |

**Ontology auto-registration:**

When config or schema changes, `universalos register` reads all `config.yaml` files and regenerates `knowledge/ontology/registry.yaml`:

```bash
universalos register          # scan configs → rebuild ontology registry
universalos register --diff   # show what changed since last registration
universalos graph             # output current ontology graph
```

This means: add a new process config → its Object Types, Links, and Actions automatically appear in the ontology. No manual ontology maintenance.

**Implementation status:** Object Types and Attributes exist (dataclasses in schema.py, JSON snapshot fields). Links and Actions are not yet built. Wikilinks (`[[link]]`) are a manual approximation of Links. Full graph traversal requires an ontology layer (SQLite graph or knowledge graph MCP tool). This is the architectural target, not the current state.

### Variants (Data Architecture)

| Tier | Path (both OSes) | LifeOS status | WorkOS status |
|------|-----------------|---------------|---------------|
| Snapshots | `data/snapshots/` | ✅ | ⚠️ `Tasks/` → migrate |
| Knowledge wiki | `knowledge/topics/` | ✅ | ⚠️ `WorkWiki/` → migrate |
| Knowledge patterns | `knowledge/patterns/` | ✅ | ⚠️ not structured |
| Knowledge insights | `knowledge/insights/` | ✅ | ⚠️ not structured |
| Knowledge ontology | `knowledge/ontology/` | ❌ not built | ❌ not built |
| Memory | `memory/` | ✅ | ⚠️ `.claude/modules/M2-data/` → migrate |
| Outputs | `data/outputs/` | ✅ | ⚠️ `Artifacts/` → migrate |

---

## System Architecture

### Shared Directory Contract

**Same functional role = same directory name + same file format.** This is the compatibility contract.

| Function | Path (both OSes) | LifeOS status | WorkOS status |
|----------|-----------------|---------------|---------------|
| L1 Principles | `system/principles.md` | ✅ | ⚠️ implicit only |
| L2 Identity | `system/identity.md` | ✅ | ❌ not written |
| L3 Purpose | `system/purpose.md` | ✅ | ✅ PRD §0 (inline) |
| L4 Capital philosophy | `system/capitals/{name}.md` | ✅ | ⚠️ professional.md only |
| L5 Goals | `system/goals/` | ✅ | ❌ not structured |
| L5 Process configs | `processes/{domain}/config.yaml` | ⚠️ not yet | ❌ not yet |
| L5 Execution processes | `processes/` | ✅ | ⚠️ `.claude/processes/` |
| Agents | `.claude/agents/*.md` | ✅ | ✅ |
| Skills | `skills/` | ✅ | ⚠️ `.claude/skills/` |
| Knowledge | `knowledge/` | ✅ | ⚠️ `WorkWiki/` → migrate |
| Memory | `memory/` | ✅ | ⚠️ `.claude/modules/M2-data/` → migrate |
| Snapshots | `data/snapshots/` | ✅ | ⚠️ `Tasks/` + `Artifacts/` → migrate |
| Outputs | `data/outputs/` | ✅ | ⚠️ `Artifacts/` → migrate |

---

### Universal OS Runtime

Universal OS is installed as a CLI + Python library. It provides the deterministic layer of the AI-Native Loop. Each OS configures what runs and how; Universal OS provides the runtime that executes it.

```
universal-os/
  schema/
    base.py              # ExtensibleSchema: base dataclass + config-driven extension
    task.py              # Task base schema
    journal.py           # JournalEntry base schema (currently in finance/schema.py)

  process_types/
    record_to_report.py  # R2R: PERCEIVE→ACT(P&L/B/S/CF/Reconcile)→REFLECT
    log_entry.py         # Log: PERCEIVE→ACT(append to SSOT)
    aggregate.py         # Aggregate: PERCEIVE→ACT(aggregate_physical_outputs)→REFLECT
    plan.py              # Plan: PERCEIVE→ACT(declare allocation)→REFLECT
    bridge.py            # Bridge: ACT(serialize + transmit to shared channel)

  capabilities/
    sense/
      check_missing.py   # validate required data exists
      snapshot_loader.py # load SSOT files
    act/
      ssot_writer.py     # write to SSOT files
      history_updater.py # upsert period to history JSON
      period_lock.py     # lock a period after close
    validate/
      schema_checker.py  # validate against declared schema
      config_validator.py # validate config.yaml against installed version

  exception/
    ledger.py            # append-only exception log
    policy.py            # load and apply exception_policy from config
    reprocessor.py       # re-run RECORD stage from a given period

  ontology/
    registry.py          # scan configs → generate registry.yaml
    graph.py             # link traversal queries

  cli/
    main.py              # universalos <command>
```

**CLI commands:**

```bash
universalos update                    # upgrade to latest version from GitHub
universalos init <os-type>            # scaffold directory structure
universalos validate                  # check schema + config compatibility
universalos validate --config-history # detect version gaps requiring reprocess
universalos register                  # rebuild ontology registry from configs
universalos register --diff           # show what changed
universalos graph                     # output ontology graph
universalos reprocess --from YYYY-MM  # re-run RECORD stage from given period
universalos diff-config v1.1 v1.2     # compare config versions
```

**Installation:**

```bash
pip install git+https://github.com/Jintheexplorer/universal-os.git
universalos update                    # self-update from same GitHub source
```

---

### Shared Task Schema

Tasks are the primary execution unit in both OSes. The schema is defined once at the framework level.

**Universal Task Schema v1.0:**

```
Required (all tasks):
  id            string      OS-prefixed (lifeos-*, workos-*)
  title         string      one-line description
  status        enum        todo | in_progress | blocked | completed | cancelled
  priority      enum        P1 | P2 | P3
  axis          enum        run | build
  owner         string
  created_at    ISO 8601

Optional (standard):
  due_date      ISO 8601 date
  started_at    ISO 8601 date
  completed_at  ISO 8601 date
  estimate      string      "2h", "1d"
  blocked_by    array       task IDs
  tags          array
  output_link   string
  comment       string

Professional Capital fields (WorkOS-primary, LifeOS-readable):
  skill_tags      array     ["gtm-strategy", "solution-design"]
  skill_domain    enum      technical | business
  complexity      enum      S | M | L | XL
  audience        string
  delivery_channel string

LifeOS-specific:
  goal_id, project_id, milestone_id
  capital         enum      economic | professional | humanistic | wellness

WorkOS-specific:
  domain, hitl_required, source, related_artifacts
```

---

### The Bridge: SSOT Transmission, Not Translation

```
WorkOS SSOT (tasks.json)
  → [PostToolUse hook on task-manager]
  → serialize using Universal Task Schema
  → write to GDrive/bridge/workos_tasks.json
  → [LifeOS bridge_loader.py reads on /daily]
  → merge into LifeOS task view + aggregate professional.json
```

Same schema = no translation layer. `axis` field makes Run/Build visible to LifeOS immediately. WorkOS is authoritative; LifeOS reads only. Stale threshold: 24-hour warning.

---

### Current Schema Gap Analysis

| Field | LifeOS | WorkOS | Universal Schema |
|-------|:------:|:------:|:----------------:|
| `id` | ✅ | ✅ | required |
| `title` | `description` | ✅ | → standardize |
| `status` | ✅ | ✅ | required |
| `priority` | ❌ | ✅ | required → add to LifeOS |
| `axis` | ❌ | ❌ | required → **add to both** |
| `owner` | ❌ | ✅ | required |
| `created_at` | ❌ | ✅ | required |
| `due_date` | ✅ | `due` | → standardize |
| `skill_tags` | ❌ | ❌ | Professional Capital → add to both |
| `skill_domain` | ❌ | ❌ | Professional Capital → add to both |
| `complexity` | ❌ | ❌ | Professional Capital → add to both |
| `goal_id` | ✅ | ❌ | LifeOS-specific |
| `hitl_required` | ❌ | ✅ | WorkOS-specific |
| `completed` | boolean | derived | → derive from status |

**Biggest gap:** `axis` missing from both. This is the Run/Build signal that makes cross-OS aggregation meaningful.

---

## Design Constraints

1. **Independence:** Each OS keeps its own SSOT. No merging.
2. **One-way bridge:** WorkOS transmits; LifeOS reads. Never the reverse.
3. **Human authority over identity:** L1 and L2 changes require explicit human approval.
4. **Shared directory contract:** Same functional layer = same directory name + same file format.
5. **Shared task schema:** Same field definitions. Own SSOTs. No translation layer at the bridge.
6. **System/Instance separation:** This document contains no personal data. Instance values live in each OS's `system/` directory.
7. **L3 ≠ L5:** Purpose defines destination. Execution defines operation. Run/Build belongs in L5.
8. **Variant extensibility:** A new OS adds a Variant column. No framework sections change.
9. **Exceptions are first-class data:** No silent failures. All deviations are logged to the exception ledger, resolvable, and auditable.
10. **Raw data is immutable:** Config changes affect the calculation layer only. Original SSOT data is always reprocessable.
11. **Ontology follows config:** Schema and process config changes automatically propagate to the ontology registry via `universalos register`. No manual ontology maintenance.

---

## Open Questions

1. **WorkOS `system/identity.md`** — Vocation definition not yet written. Highest-priority gap.
2. **WorkOS directory migration** — `WorkWiki/` → `knowledge/`, `Artifacts/` → `data/outputs/`. Scope evaluation needed.
3. **Task schema migration** — LifeOS: add `priority`, `axis`, `owner`, `created_at`, `skill_tags`, `skill_domain`, `complexity`. WorkOS: add `axis`, `skill_tags`, `skill_domain`, `complexity`. Requires `migrate_tasks.py` in both.
4. **Process config externalization** — LifeOS Finance hardcodes `_OPERATING_TYPES`, expense categories, KPI targets. These move to `processes/finance/config.yaml`. Migration requires reprocess validation.
5. **Ontology implementation** — Links and Actions not yet built. Evaluate: SQLite graph vs DuckDB vs knowledge graph MCP tool.
6. **Config versioning tooling** — `universalos reprocess` and `diff-config` not yet implemented.
7. **Exception ledger format** — Append-only log structure for business exceptions not yet standardized.

---

## Status

| Layer | System spec | LifeOS instance | WorkOS instance |
|-------|-------------|-----------------|-----------------|
| L1 Principle | ✅ | ✅ | ⚠️ implicit only |
| L2 Identity | ✅ | ✅ | ❌ not written |
| L3 Purpose | ✅ | ✅ | ✅ PRD §0 |
| L4 Capital config | ✅ | ✅ 4 snapshots | ⚠️ no measurement fields |
| L5 Loop (4-stage) | ✅ | ⚠️ PLAN+REFLECT prob not built | ⚠️ PLAN+REFLECT not built |
| L5 Exception handling | ✅ (spec) | ⚠️ partial (status field only) | ⚠️ not structured |
| L5 Config versioning | ✅ (spec) | ❌ not implemented | ❌ not implemented |
| Data Architecture — Snapshots | ✅ | ✅ | ⚠️ divergent naming |
| Data Architecture — Knowledge | ✅ | ✅ | ⚠️ `WorkWiki/` → migrate |
| Data Architecture — Ontology | ✅ (spec) | ❌ not built | ❌ not built |
| Data Architecture — Memory | ✅ | ✅ | ⚠️ divergent naming |
| Task schema | ✅ v1.0 | ⚠️ missing 5 fields | ⚠️ missing 3 fields |
| Bridge | ✅ | ✅ bridge_loader.py | ⚠️ export hook pending |
| Universal OS Runtime | ✅ (spec) | ❌ not yet built | ❌ not yet built |
| Process configs (YAML) | ✅ (spec) | ❌ not yet externalized | ❌ not yet |
