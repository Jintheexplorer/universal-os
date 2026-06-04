# Universal OS — Vision

> **Why does this system exist?**
> 나다운 삶을 살고 싶다. 나는 개인을 하나의 자율형 기업(Autonomous Enterprise)으로 경영하며,
> 네 가지 자본(경제·직업·인문·신체)을 복리로 키우면서 목표에 가까워지는 삶을 원한다.
> 이 시스템은 그 삶을 지탱하는 AI-Native 운영체제다.

---

## 핵심 공식

```
SSOT  +  Process & Architecture  +  Closed Learning Loop
  =  System of Context + Action
```

AI 에이전트가 grounded하게 행동하려면 세 가지가 모두 있어야 한다.

| 누락 | 무너지는 것 |
|------|------------|
| **SSOT 없음** | 에이전트가 오래된 정보나 추론에 기반해 행동함 |
| **Process 없음** | 임의 실행 — 재현 불가, 조합 불가 |
| **Closed Loop 없음** | 같은 실수 반복, 복리 없음, 시스템이 성장하지 않음 |

---

## 두 환경에서 사는 삶

```
[개인 MacBook — LifeOS]         [SAP MacBook — WorkOS]
  · 경제, 신체, 인문 자본 관리       · 업무 실행 환경
  · 주간/월간 계획 + 리뷰           · GTM, 솔루션 설계, 고객 대응
  · 지식 축적 + 자기 성찰           · Professional Capital 생산
        ↑                                ↓
        └────── Google Drive Bridge ──────┘
               (단방향: WorkOS → LifeOS)
```

두 OS는 독립된 SSOT를 가지며 합쳐지지 않는다.
WorkOS는 Professional Capital의 원료를 생산하고, LifeOS가 집계·분석·성장시킨다.

---

## 자본이 복리로 쌓이는 구조

```
매일: 작은 기록 (카디오 1회, 저널 1건, 태스크 1완료)
  ↓
매주: 패턴 발견 + 다음 주 계획
  ↓
매월: 4개 자본 스냅샷 갱신 + 인사이트 축적
  ↓
분기/연간: Craft 레벨 성장 + Track Record 포트폴리오 + 목표 재조정
  ↓
커리어 전환: Evangelist → Architect → Entrepreneur
```

오늘의 "발표 완료" 1건이 어떻게 쌓이는가:
- **오늘**: WorkOS 태스크 완료 기록
- **이번 주**: Bridge → LifeOS Professional Capital 신호
- **이번 달**: professional.json Track Record 집계
- **올해**: gtm-strategy Craft Level 증거 누적
- **5년 후**: Architect 단계 진입의 복리 증거

---

## Universal OS가 없으면 일어나는 일

| 상황 | 시스템 없이 | Universal OS와 함께 |
|------|-----------|-------------------|
| 태스크 완료 | 그냥 끝남 | Craft/Track Record/Influence 신호 자동 기록 |
| 운동 | 앱에만 남음 | 신체자본 snapshot 집계, 목표 달성률 추적 |
| 카드 지출 | 모름 | wants/needs 분류, 저축률 실시간 업데이트 |
| 발표 잘 함 | 기억에만 남음 | complexity=L, audience=CIO → Track Record 영구 기록 |
| 설계가 바뀜 | 모든 코드 수정 | config.yaml만 변경, raw 데이터 불변 |
| WorkOS 완료 태스크 | LifeOS 모름 | Bridge 통해 24h 내 자동 전파 |

---

## 운영 원칙

1. **SSOT 절대성** — 모든 자본의 진실은 단 하나의 파일에만 존재한다.
2. **대화로 진화** — 사용자가 코드를 작성하지 않는다. 대화로 AI가 Config을 수정하고 시스템을 확장한다.
3. **OS 독립성** — LifeOS와 WorkOS는 합쳐지지 않는다. 연결은 Bridge를 통해서만.
4. **인간 권위** — L1 Principle, L2 Identity 변경은 사람만 결정한다. AI 자동 수정 금지.
5. **불변 원본** — 원본 데이터는 절대 수정되지 않는다. Config 변경은 계산 레이어에만 영향을 준다.

---

*이 문서는 구현의 목적을 기억하기 위한 것이다. 코드는 이 삶을 지탱하는 인프라다.*
