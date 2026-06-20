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

New OS instances extend the framework by adding a Variant column. No framework sections need to change.

---

## 핵심 공식

```
SSOT  +  Process & Architecture  +  Closed Learning Loop
  =  System of Context + Action
```
---

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

---

## L2 — Identity

**System:** A structured definition of who the person is — values, strengths, and orientation. Defined as **vocation** (calling), not occupation (job title).

**Structure of an Identity definition:**
- **Driving question:** "What am I here to do?"
- **Core values:** The principles that guide decisions under uncertainty
- **Core beliefs:** What you hold to be true about the world
- **Development stage:** Current phase of professional identity evolution

---

## L3 — Purpose

**System:** Where the person is going — their long-range direction. Purpose is the bridge between Identity (who you are) and Execution (what you do daily). It defines the *destination*; Execution defines the *path*.

**Structure of a Purpose definition:**
- **Vision:** The world you are trying to create or contribute to
- **Mission:** How you specifically contribute — your unique method
- **Roadmap:** The progression of phases through which you pursue the mission

**Rule:** Purpose is stable across years. It changes at genuine life transitions — career pivots, major value reassessments — not at weekly planning cycles.

---

## L4 — Capital

**System:** Capital is anything that compounds. Invested wisely, it grows. Neglected, it decays. Every OS defines:
1. Which capital types it manages
2. How accumulation is measured
3. Where the SSOT snapshot lives

**Measurement principle:** Self-assessment is noise. Evidence is signal. Capital is measured by artifacts — things you made, delivered, or had recognized externally.

---

## L5 — Execution

**System:** Execution is how capital is deployed toward Purpose — the layer where identity, vision, and resources become concrete action. It is the most volatile layer: processes, tools, and rhythms evolve continuously as the OS learns from its own output.

Execution has three structural components: **Strategy** (where to direct resources), **Performance** (how to measure output), and **Systems** (how action becomes self-sustaining).

**Structure of Execution:**

- **Strategy:** The logic of resource allocation. Every OS defines how it identifies opportunities — the intersection of what it can do well, what the world needs, and what feels authentic to its identity. Resources (time, energy, capital) are finite; the OS must distinguish investment from expense.

- **Risk & Opportunity (IRO):** Risk is not eliminated — it is understood and designed for. The OS maintains a living map of risks and opportunities across each capital domain, with defined response postures for each.

- **Performance management:** Output is defined before it is measured. The OS specifies what success looks like — quantitatively or qualitatively — then tracks movement over time. Goals are decomposed to the smallest achievable unit to maintain focus and momentum.

- **Systems:** Execution is sustained by two types of systems. *Habit* encodes recurring behavior into the environment itself — reducing the cost of action through scripts, triggers, and friction design. *Flow* protects high-energy, high-focus work by placing it where cognitive resources are strongest.

- **Digital Brain & AI:** The OS externalizes memory and cognition into structured tools — Capture → Memorize → Organize. AI operates as an execution multiplier; the human's role is to supply context, intent, and judgment.

**Rule:** Execution is the only layer that changes frequently. Changes to strategy, tools, or rhythms do not require revisiting L1–L4. Changes that *feel like* they require revising Purpose or Identity are a signal to pause — not to restructure L5.

**Self-reinforcing loop:** Identity → Purpose → Capital → Execution → more authentic identity

