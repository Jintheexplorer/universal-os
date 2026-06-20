# Universal OS

AI-native personal operating system framework — 대화를 통해 삶과 업무를 운영하는 구조화된 시스템.

LifeOS와 WorkOS 두 인스턴스를 하나의 공유 프레임워크(`universal-os` Python 패키지)로 운영한다.

---

## 문서 구조

| 문서 | 질문 | 독자 |
|------|------|------|
| [`docs/philosophy/01-vision.md`](docs/philosophy/01-vision.md) | Why — 왜 이 시스템을 만드는가? | 처음 접하는 사람 |
| [`docs/requirements.md`](docs/requirements.md) | What — 시스템이 무엇을 하는가? (요구사항 + Backlog) | 기능 범위 파악 |
| [`docs/architecture.md`](docs/architecture.md) | How — 어떻게 구조화됐는가? (5-layer, Loop, Schema) | 기술 설계 이해 |
| [`docs/lifeos-architecture.md`](docs/lifeos-architecture.md) | LifeOS 인스턴스 구현 상세 | LifeOS 개발/운영 |
| [`docs/workos-architecture.md`](docs/workos-architecture.md) | WorkOS 인스턴스 구현 상세 | WorkOS 개발/운영 |
| [`docs/constitution/`](docs/constitution/) | 불변 설계 원칙 4개 (Data, Workflow, Interaction, Governance) | 아키텍처 결정 |

---

## 핵심 개념

```
SSOT  +  Process & Architecture  +  Closed Learning Loop
  =  System of Context + Action
```

- **5계층:** Principle → Identity → Purpose → Capital → Execution
- **두 기둥:** Python(결정론적) + Claude Code(확률론적)
- **4단계 루프:** PERCEIVE → PLAN → ACT → REFLECT
- **Bridge:** WorkOS → GDrive → LifeOS (단방향, Universal Task Schema)

---

## universal-os 패키지

```bash
pip install git+https://github.com/Jintheexplorer/universal-os.git

universalos init <os-type> <path>   # 디렉토리 scaffold
universalos validate <config>       # schema + config 검증
universalos register <os-root>      # ontology registry 재생성
```

---

## 인스턴스 저장소

| 인스턴스 | 저장소 | 역할 |
|---------|-------|------|
| LifeOS | `Jintheexplorer/LifeOS` | 4개 자본 통합 관리 |
| WorkOS | `Jintheexplorer/WorkOS` (예정) | Professional Capital 실행 |
