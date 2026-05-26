# 순환기 중간고사 RPG 학습 대시보드 — 구현 계획서

> **목적**: 27개 강의에 대한 5가지 학습 활동의 달성도를 게임처럼 시각화·추적하는 단일 페이지 HTML 대시보드를 만든다.
> **시험일**: 2026-05-29 (금)
> **오늘**: 2026-05-26 (화) — D-3
> **결과물**: `D:\조나단\일회성 프로젝트\공부 진도 체크\dashboard.html` (단일 파일, localStorage 영속화)
> **구현 방식**: 아래 § 8 오케스트레이션 구조에 따라 모듈을 병렬로 작성한 뒤 단일 HTML로 번들.

---

## 0. 결정 요약 (Single Source of Truth)

| 항목 | 결정값 |
|---|---|
| 출력 포맷 | 단일 `dashboard.html` (CSS·JS 인라인, localStorage 영속화) |
| 강의 수 | 27강 |
| 강의당 기본 활동 | ① 족보 ② 플래시 ③ 퀴즈 첫도전 ④ 퀴즈 재도전 ⑤ 회독 1회 (총 5종, 모두 boolean) |
| 보너스 활동 | ⑥ 보너스 퀴즈 (counter, 무제한) · ⑦ 보너스 회독 (counter, 무제한) |
| 게임 테마 | 하이브리드 — 구조: RPG 던전 공략기(5장=5던전, 보스전), 칭호: 의학사 위인 계승 |
| 그룹핑 (표시) | 날짜순 강의 카드, 단 각 카드에 소속 「장(던전)」 태그·색상 |
| 일일 목표량 | **없음** (한 판으로 쭉 이어가는 게임) |
| XP 가중치 | 더 도전적 (회독·퀴즈 폭등 + 보너스 대폭) — § 3 참조 |
| 뱃지 종류 | 분량 가광 / 장 정복 / 활동별 장인 / 하루 테마 (최대한 다양) — § 4 참조 |
| 비주얼 | 다크 판타지 RPG (검정 배경 + 황금·진홍 액센트) |
| 추가 기능 | 포모도로 타이머 내장 + 다음 추천 활동 스마트 제안 |

---

## 1. 강의 ↔ 장(던전) 매핑 (27강 전수)

### 1장 「해부의 성 (Anatomy Citadel)」 — 5강 · 보스 〈베살리우스〉
| ID | 일시 | 교시 | 주제 | 교수 |
|---|---|---|---|---|
| L01 | 5/18 | 2 | 심장의 구조 | 김태영 |
| L02 | 5/18 | 3 | 심장의 구조 | 김태영 |
| L03 | 5/18 | 4 | 심장의 혈관 | 김태영 |
| L04 | 5/19 | 1 | 순환계통 조직I | 김태영 |
| L05 | 5/19 | 2 | 순환계통 조직II | 김태영 |

### 2장 「생리의 탑 (Physiology Spire)」 — 1강 · 보스 〈윌리엄 하비〉
| ID | 일시 | 교시 | 주제 | 교수 |
|---|---|---|---|---|
| L06 | 5/19 | 3 | 심박출량의 조절 | 송대규 |

### 3장 「영상의 미궁 (Imaging Labyrinth)」 — 8강 · 보스 〈르네 라에네크〉
| ID | 일시 | 교시 | 주제 | 교수 |
|---|---|---|---|---|
| L07 | 5/21 | 2 | 심초음파 1 — 판막성 심질환 | 김인철 |
| L08 | 5/21 | 3 | 심초음파 2 — 판막성 심질환 | 김인철 |
| L09 | 5/21 | 4 | 심초음파 3 — 판막성 심질환 | 김인철 |
| L10 | 5/22 | 1 | 심혈관질환의 영상진단(1) | 홍정희 |
| L11 | 5/22 | 2 | 심혈관질환의 영상진단(2) | 홍정희 |
| L12 | 5/26 | 1 | 심초음파 1 — 개론 | 김인철 |
| L13 | 5/26 | 2 | 심초음파 2 — 개론 | 김인철 |
| L14 | 5/26 | 3 | 심장핵의학 | 원경숙 |

### 4장 「검사의 광산 (Diagnostic Mines)」 — 6강 · 보스 〈빌렘 아인트호벤〉
| ID | 일시 | 교시 | 주제 | 교수 |
|---|---|---|---|---|
| L15 | 5/27 | 1 | 심혈관질환의 진단검사의학 | 강혜인 |
| L16 | 5/27 | 2 | 심전도 1 | 박형섭 |
| L17 | 5/27 | 3 | 심전도 2 | 박형섭 |
| L18 | 5/27 | 7 | 운동부하검사 / 관상동맥 조영술 | 조윤경 |
| L19 | 5/27 | 8 | 설문조사 및 질의 응답 수업 1 | 조윤경 |
| L20 | 5/28 | 1 | 심전도 3 | 정태완 |

### 5장 「임상의 쇼르·도 (Clinical Citadel)」 — 7강 · 보스 〈현대 심장학의 거장〉
| ID | 일시 | 교시 | 주제 | 교수 |
|---|---|---|---|---|
| L21 | 5/21 | 7 | 심장학 서론 | 장우성 |
| L22 | 5/21 | 8 | 심장수술 총론 | 김윤석 |
| L23 | 5/28 | 2 | 빈맥 1 | 정태완 |
| L24 | 5/28 | 3 | 빈맥 2 | 정태완 |
| L25 | 5/28 | 4 | 서맥 | 황종민 |
| L26 | 5/28 | 5 | 심장진찰 1 | 황종민 |
| L27 | 5/28 | 6 | 심장진찰 2 | 황종민 |

**합계 검증**: 5 + 1 + 8 + 6 + 7 = **27강** ✓

> 화면 정렬은 **날짜·교시 오름차순(L01→L27)** 으로 하되, 각 강의 카드 좌측 색띠와 「장 N」 배지로 소속 던전 표시. 우측 「장 진척도 패널」에 5장 각각의 진행률 바를 항상 노출.

---

## 2. 활동·진행 데이터 모델

각 강의 1개당 5개 boolean + 2개 counter:

```js
// localStorage key: "circ-rpg-state-v1"
{
  version: 1,
  lectures: {
    "L01": {
      jokbo:        false,   // 족보 훑어보기 1회 (10 XP)
      flashcard:    false,   // NotebookLM 플래시 1세트 (15 XP)
      quizFirst:    false,   // 퀴즈 첫 도전 (40 XP)
      quizRetry:    false,   // 퀴즈 재도전 1회 (25 XP)
      review:       false,   // 강의록 회독 1회 (60 XP)
      bonusQuiz:    0,       // 추가 퀴즈 횟수 (+20 XP/회)
      bonusReview:  0,       // 추가 회독 횟수 (+30 XP/회)
      memo:         ""       // 자유 메모
    },
    // ... L02 ~ L27 동일 스키마
  },
  pomodoro: {
    totalSessions: 0,        // 누적 세션 (+5 XP/세션)
    currentMode: "focus25",  // focus25 | focus50
    lastSessionAt: null
  },
  badges: [],                // 획득 뱃지 ID 배열
  bossDefeated: {            // 보스전 승리 여부
    ch1: false, ch2: false, ch3: false, ch4: false, ch5: false
  },
  firstLaunchAt: null,       // 초회 실행 시각
  totalXp: 0                 // 캐싱된 총 XP (검증용)
}
```

**불변 규칙**:
- 모든 boolean 토글 시 즉시 `localStorage` flush.
- 카운터(`bonusQuiz`, `bonusReview`)는 +1/-1 버튼 두 개.
- `totalXp`는 항상 함수로 재계산(§3 공식)하여 캐싱과 비교 → 불일치 시 재계산 우선.

---

## 3. XP·레벨 시스템

### 3.1 XP 공식

| 활동 | XP | 비고 |
|---|---:|---|
| 족보 훑어보기 | 10 | 1회 |
| NotebookLM 플래시 1세트 완주 | 15 | 1회 |
| 퀴즈 첫 도전 | 40 | 1회 |
| 퀴즈 재도전 1회 (baseline) | 25 | 1회 |
| 강의록 회독 1회 (baseline) | 60 | 1회 |
| **기본 5개 합계** | **150 / 강의** | |
| 보너스 퀴즈 (추가 1회) | +20 | counter, 무제한 |
| 보너스 회독 (추가 1회) | +30 | counter, 무제한 |
| 포모도로 세션 완료 | +5 | 25분 또는 50분 |

**기본치 완주 시 총 XP** = 27강 × 150 = **4,050 XP**

### 3.2 레벨 임계값 & 의학사 위인 칭호

| Lv | 최소 XP | 칭호 | 부제 | 위인 한 줄 트리비아 |
|---:|---:|---|---|---|
| 1 | 0 | 히포크라테스의 제자 | 체액론 너머의 길을 찾아서 | "기원전 5세기, 의학을 미신에서 분리한 자" |
| 2 | 500 | 갈레노스의 도제 | 해부도를 펼치며 | "1500년간 서구 의학을 지배한 로마 황실 의사" |
| 3 | 1,100 | 베살리우스의 제자 | 근대 해부학의 빛 | "갈레노스의 오류를 시체로 직접 검증한 1543년의 혁명가" |
| 4 | 1,800 | 윌리엄 하비의 후예 | 혈액순환의 비밀을 풀다 | "심장이 펌프임을 1628년에 증명, 본 과정의 진짜 영웅" |
| 5 | 2,600 | 르네 라에네크의 계승자 | 청진기의 음을 듣는 자 | "1816년 청진기를 발명, 심음 진단의 시작" |
| 6 | 3,300 | 빌렘 아인트호벤의 후예 | 심전도의 파장을 읽다 | "1903년 string galvanometer로 ECG 창안, 1924년 노벨상" |
| 7 | 4,050 | 현대 심장학의 거장 | 심장의 모든 비밀에 가까이 | "이 시험을 정복한 너, 그 자체" |

> 4,050 XP를 넘어선 뒤 1,000 XP마다 「★ Prestige」 별 1개 추가(상한 없음). 별 5개 모이면 「★★★★★ Heart Sage」 비밀 호칭.

### 3.3 진행률 정의
- **강의 진행률** = (해당 강의의 5개 boolean 중 true 개수) / 5
- **장 진행률** = (소속 강의들의 boolean true 총합) / (소속 강의 수 × 5)
- **전체 진행률** = (전체 boolean true 합계) / 135
- **XP 진행률** = totalXp / 4,050 (보너스 포함이면 100% 초과 가능, 캡 없음)

---

## 4. 뱃지 카탈로그 (26종)

> ID는 코드 키, 한글명은 표시용. 획득 조건이 false→true가 되는 순간 토스트 알림 + 갤러리 추가.

### 4-A. 분량 가광 (Volume) — 6종
| ID | 한글명 | 조건 |
|---|---|---|
| `vol_first` | 첫 발자국 | 어떤 활동이든 처음 1개 완료 |
| `vol_10` | 워밍업 완료 | 누적 활동 10개 |
| `vol_30` | 추진력 점화 | 누적 활동 30개 |
| `vol_70` | 절반의 의지 | 누적 활동 70개 (≈52%) |
| `vol_120` | 막바지 진격 | 누적 활동 120개 (≈89%) |
| `vol_135` | 완전 정복 | 누적 기본 활동 135개 (100%) |

### 4-B. 장 정복 (Boss Slayer) — 5종
| ID | 한글명 | 조건 |
|---|---|---|
| `boss_ch1` | 해부의 성 정복자 | 1장 모든 강의 5/5 완료 |
| `boss_ch2` | 생리의 탑 등반자 | 2장 모든 강의 5/5 완료 |
| `boss_ch3` | 영상의 미궁 탈출자 | 3장 모든 강의 5/5 완료 |
| `boss_ch4` | 검사의 광산 채굴자 | 4장 모든 강의 5/5 완료 |
| `boss_ch5` | 임상의 쇼르·도 정복자 | 5장 모든 강의 5/5 완료 |

### 4-C. 활동별 장인 (Professional) — 5종
| ID | 한글명 | 조건 |
|---|---|---|
| `pro_jokbo` | 족보 수집가 | 27강 전체 족보 |
| `pro_flash` | 플래시 타니육사 | 27강 전체 플래시 |
| `pro_quiz1` | 퀴즈 첫 돌격대장 | 27강 전체 퀴즈 첫 도전 |
| `pro_quiz2` | 퀴즈 재도전 거장 | 27강 전체 퀴즈 재도전 |
| `pro_review` | 회독의 달인 | 27강 전체 회독 1회 |

### 4-D. 하루 테마 (Daily Theme) — 6종
| ID | 한글명 | 조건 |
|---|---|---|
| `day_dawn` | 새벽 강행군 | 06:00~09:00 사이 5개 활동 완료 |
| `day_noon` | 정오의 빛 | 11:00~14:00 사이 5개 활동 완료 |
| `day_night` | 자정 너머 | 22:00 이후 3개 활동 완료 |
| `day_30` | 양익한 결의 | 하루 활동 30개 |
| `day_perfect` | 완벽주의자 | 한 강의의 5개 기본 활동 모두 완료(같은 날) |
| `day_full` | 풀세트 챔피언 | 한 강의 5개 기본 + 보너스 1개 이상(누적) |

### 4-E. 기타 다양성 (Misc) — 4종
| ID | 한글명 | 조건 |
|---|---|---|
| `misc_bonus` | 보너스 헌터 | 보너스 활동 5개 (퀴즈/회독 카운터 합산) |
| `misc_pomo1` | 포모도로 입문 | 포모도로 1세션 완료 |
| `misc_pomo10` | 포모도로 마라토너 | 포모도로 10세션 누적 |
| `misc_grand` | ★ Grand Slam ★ | 위 25개 뱃지 모두 획득 (메타 뱃지) |

> **시간 판정**: 활동 완료 클릭 시각 = 뱃지 판정 시각. `new Date()` 사용.
> **누적 판정**: localStorage에 일별 활동 이력 별도 저장(아래 § 7-D 참조).

---

## 5. 보스전 시스템 (5번)

### 5.1 트리거
한 장의 모든 강의가 기본 5개 활동을 전부 완료한 순간 `bossDefeated.chN = true` 직전, 풀스크린 모달 발사.

### 5.2 모달 구성
```
┌─────────────────────────────────────┐
│  ⚔  BOSS DEFEATED  ⚔                │
│                                     │
│    [장 보스 SVG 일러스트]            │
│                                     │
│  「베살리우스」                       │
│  근대 해부학의 아버지                  │
│                                     │
│  "Galenum corrigendum est"          │
│  ─ 갈레노스를 고쳐야 한다              │
│                                     │
│  📜 트리비아                          │
│  1543년 『De humani corporis        │
│  fabrica』를 출간, 갈레노스가         │
│  본 적 없던 인체를 직접 해부했다.       │
│                                     │
│  +200 XP 보너스                      │
│  뱃지 획득: 해부의 성 정복자           │
│                                     │
│  [ 계속 진행 ]                        │
└─────────────────────────────────────┘
```

### 5.3 5명의 보스
| 장 | 보스 | 명언 | 트리비아 (모달에 표시) |
|---|---|---|---|
| 1 | 베살리우스 (Vesalius, 1514-1564) | "Galenum corrigendum est" | 1543년 『De humani corporis fabrica』로 갈레노스 200곳 오류 정정. 28세 파도바 대학 교수. |
| 2 | 윌리엄 하비 (Harvey, 1578-1657) | "Cor est principium vitae" | 1628년 『De motu cordis』로 혈액순환 증명. 심박출량 = 심박수 × 1회박출량의 개념적 기원. |
| 3 | 르네 라에네크 (Laennec, 1781-1826) | "Auscultation reveals what the eye cannot see" | 1816년 청진기 발명. 환자 가슴에 직접 귀를 대는 게 어색해서 종이를 말아 사용한 것이 시작. |
| 4 | 빌렘 아인트호벤 (Einthoven, 1860-1927) | "Each wave tells a story" | 1903년 String galvanometer로 ECG 창안. P-Q-R-S-T 명명자. 1924 노벨 생리의학상. |
| 5 | 현대 심장학의 거장 (Forssmann·Cournand·Richards) | "The heart yields to those who listen" | 1929년 Forssmann 자기 자신에게 심도자술 시행, 1956년 노벨상. 현대 중재시술의 시작. |

### 5.4 보너스
보스전 승리 시 **+200 XP** 일괄 지급(레벨업 가속).

---

## 6. 포모도로 타이머

### 6.1 UI
우측 사이드바 상단 카드. 표시: `25:00` 같은 모노스페이스 카운트다운 + [시작][정지][리셋][모드 토글] 버튼. 모드: `focus25` (25+5) | `focus50` (50+10).

### 6.2 로직
- 시작 → 카운트다운 → 0:00 도달 시 알림음(브라우저 Audio API) + 시각 강조 + **+5 XP**.
- 4세션마다 긴 휴식 15분(자동 전환).
- 완료된 세션은 `pomodoro.totalSessions++` → 뱃지 판정.
- 페이지 새로고침해도 진행 중 세션은 `localStorage`의 `targetEpoch`로 복구.

### 6.3 시각 표시
타이머 진행 시 페이지 헤더에 작은 ⏱ 아이콘 + 남은 시간. 다른 탭에서 작업해도 한눈에.

---

## 7. 스마트 추천 엔진

### 7-A. 메인 추천 (다음 1개 활동)
우선순위:
1. **보스전 임박** — 어떤 장이 95% 이상이면 그 장의 남은 활동을 최우선.
2. **자연 순서** — 위 조건 없으면 L01부터 순회, 첫 미완료 강의의 첫 미완료 활동.
3. **활동 순서** 내부: 족보 → 플래시 → 퀴즈 첫도전 → 회독 → 퀴즈 재도전.

### 7-B. 보조 추천 (사이드바 카드)
- **"오늘 가장 많이 한 활동 유형"** 시각화 (작은 도넛).
- **"가장 오래 안 건드린 강의 Top 3"** — 마지막 활동 시각 기준.
- **"보스전 거리"** — 5장 각각 "보스전까지 N개 남음" 한 줄.

### 7-C. 응원 메시지 (XP 진척에 따라 변화)
- 0~20%: "혈관에 첫 박동을. 시작이 절반이야."
- 20~50%: "심실이 차오르고 있어. 이대로 박출하자."
- 50~80%: "관상동맥에 피가 도네. 흐름을 끊지 마."
- 80~100%: "Sinus rhythm. 시험장 직전, 정점에 가까워."
- 100%+: "AV node를 넘어선 자, 시험을 두려워 마라."

### 7-D. 이력 저장 스키마 (시간 판정용)
```js
// localStorage key: "circ-rpg-history-v1"
{
  events: [
    { t: 1748246400000, type: "toggle", lectureId: "L01", field: "jokbo", value: true },
    { t: 1748246460000, type: "pomodoro", mode: "focus25" },
    // ...
  ]
}
```
이벤트는 append-only. 시간대별 뱃지(`day_dawn`, `day_noon` 등) 판정에 사용. 1,000개 초과 시 오래된 항목부터 트림(메모리 보호).

---

## 8. 오케스트레이션 구조 (병렬 구현 모듈)

> 각 모듈은 **독립 입력·출력**을 가지며 서로 알 필요 없음. 각 모듈을 별 agent에 위임 가능. 모듈 Z(Integration)만 마지막에 직렬로 수행.

### 8.1 모듈 의존 그래프
```
A (Data) ──┬─→ B (State Engine) ──┬─→ E (Boss Battle)
           │                       ├─→ G (Suggestion)
           │                       ├─→ H (Badge Engine)
           │                       └─→ F (Pomodoro)
           ├─→ C (HTML Skeleton) ──┐
           └─→ I (Boss Trivia) ────┤
                                   │
              D (Dark Theme CSS) ──┤
                                   │
                                   └→ Z (Final Bundle)
```

병렬 가능 그룹:
- **Wave 1 (병렬)**: A, I, D
- **Wave 2 (병렬, A 완료 후)**: B, C
- **Wave 3 (병렬, B·C 완료 후)**: E, F, G, H
- **Wave 4 (직렬)**: Z

### 8.2 모듈별 책임 & 인터페이스

#### Module A — Data Definitions
- **산출물**: `data.js` 내 export 객체들
  - `LECTURES`: § 1의 27개 객체 배열 (id, date, period, title, professor, chapterId)
  - `CHAPTERS`: § 1의 5개 객체 (id, name, bossId, color)
  - `XP_CONFIG`: § 3.1 표 그대로
  - `LEVELS`: § 3.2 표 그대로 (Lv, minXp, title, subtitle, trivia)
  - `BADGES`: § 4의 26개 객체 (id, name, category, condition 설명문)
- **테스트**: `LECTURES.length === 27`, 각 강의의 `chapterId`가 `CHAPTERS` 내 존재.

#### Module I — Boss Trivia Data
- **산출물**: `bosses.js` — § 5.3 표를 객체로(인물 이름, 생몰년, 명언, 트리비아 문단, 비주얼 힌트).
- **테스트**: 5개 보스, 각 객체에 모든 필드 존재.

#### Module D — Dark Theme CSS
- **산출물**: `<style>` 블록
- **팔레트**: 배경 `#0a0a14`, 카드 `#161624`, 액센트 황금 `#d4af37`, 진홍 `#8b0000`, 텍스트 `#e8e6d8`, 비활성 `#5a5a6e`.
- **폰트**: 제목 `'Cinzel', 'Noto Serif KR', serif`, 본문 `'Noto Sans KR', system-ui`.
- **장별 색띠**: 1장 진홍, 2장 청록 `#0d7377`, 3장 보라 `#5d3a9b`, 4장 황록 `#7a8c2a`, 5장 황금 `#c9a227`.
- **필수 클래스**: `.app`, `.header`, `.xp-bar`, `.chapter-grid`, `.lecture-card`, `.checkbox`, `.counter`, `.badge`, `.boss-modal`, `.timer`, `.suggestion`, `.toast`.
- **애니메이션**: 체크 토글 시 0.3s scale up, 보스 모달 등장 0.6s fade + scale, 뱃지 토스트 슬라이드인.

#### Module C — HTML Skeleton
- **산출물**: `<body>` 내부 정적 구조 (데이터는 비어둠, JS가 채움).
- **레이아웃 (CSS Grid)**:
  ```
  ┌───────────── HEADER ─────────────┐
  │ 레벨/칭호 │ XP 바 │ D-3 표시 │
  ├──────────────┬───────────────────┤
  │  CHAPTER     │  SIDEBAR          │
  │  GRID        │  ┌─ 포모도로     │
  │  (5장 카드 + │  ├─ 추천 활동    │
  │   강의 목록) │  ├─ 장 진척도    │
  │              │  └─ 응원 메시지  │
  ├──────────────┴───────────────────┤
  │             BADGE GALLERY        │
  └──────────────────────────────────┘
  ```
- **강의 카드 템플릿**:
  ```html
  <article class="lecture-card" data-id="L01" data-chapter="1">
    <header>
      <span class="chapter-tag">1장</span>
      <span class="date">5/18·2교시</span>
      <h3>심장의 구조</h3>
      <span class="prof">김태영</span>
    </header>
    <div class="activities">
      <label><input type="checkbox" data-act="jokbo">족보 (+10)</label>
      <label><input type="checkbox" data-act="flashcard">플래시 (+15)</label>
      <label><input type="checkbox" data-act="quizFirst">퀴즈 첫도전 (+40)</label>
      <label><input type="checkbox" data-act="quizRetry">퀴즈 재도전 (+25)</label>
      <label><input type="checkbox" data-act="review">회독 (+60)</label>
    </div>
    <div class="bonus">
      <div>보너스 퀴즈 <button data-bonus="quiz-">−</button> <span data-bonus-quiz>0</span> <button data-bonus="quiz+">+</button></div>
      <div>보너스 회독 <button data-bonus="review-">−</button> <span data-bonus-review>0</span> <button data-bonus="review+">+</button></div>
    </div>
    <progress class="lec-progress" max="5" value="0"></progress>
    <textarea class="memo" placeholder="메모..."></textarea>
  </article>
  ```

#### Module B — State Engine (JS Core)
- **산출물**: `state.js` 내 단일 글로벌 `App`.
- **공개 API**:
  ```js
  App.load()                              // localStorage → 메모리
  App.save()                              // 메모리 → localStorage
  App.toggleActivity(lecId, field)        // boolean 토글, 이벤트 emit
  App.incrementBonus(lecId, kind, delta)  // 보너스 카운터
  App.addPomodoroSession(mode)            // 세션 +1
  App.setMemo(lecId, text)
  App.computeXp()                         // 전체 재계산
  App.computeLevel(xp)                    // → {level, title, subtitle, nextLevelXp}
  App.computeLectureProgress(lecId)       // → 0~5
  App.computeChapterProgress(chId)        // → 0~1
  App.computeOverallProgress()            // → 0~1
  App.on(event, handler)                  // 'activity', 'levelup', 'badge', 'boss'
  ```
- **이벤트 발사**: `activity` (모든 토글/카운터 변화), `levelup` (레벨 변경 시), `badge` (새 뱃지), `boss` (장 100% 도달).
- **테스트**: 27강의 모든 boolean true 시 totalXp === 4,050.

#### Module E — Boss Battle Modal
- **입력**: Module B의 `boss` 이벤트 + Module I의 보스 데이터.
- **산출물**: `BossModal.show(chapterId)` 함수 + 모달 DOM.
- **로직**: 모달 표시 → "계속" 클릭 시 +200 XP 추가 → `bossDefeated.chN = true` 저장 → 뱃지 판정 트리거.

#### Module F — Pomodoro Timer
- **입력**: Module B의 `addPomodoroSession`.
- **산출물**: `Pomodoro.start() / pause() / reset() / toggleMode()` + 카운트다운 렌더.
- **세부**: 1초 인터벌, 종료 시 `<audio>` 짧은 알림(MIT-라이센스 무료 SFX 인라인 base64 또는 무음 비프), +5 XP, 누적++.

#### Module G — Suggestion Engine
- **입력**: Module B 상태.
- **산출물**: `Suggest.refresh()` 호출 시 사이드바 추천 카드 갱신.
- **알고리즘**: § 7-A 그대로.

#### Module H — Badge Engine
- **입력**: Module B의 모든 이벤트.
- **산출물**: 활동/세션 발생 시 모든 뱃지 조건 평가 → 새 뱃지 시 토스트 + 갤러리 추가.
- **테스트**: 26개 뱃지 각각의 조건 함수가 boolean을 반환.

#### Module Z — Final Bundle
- **입력**: 위 모든 모듈.
- **작업**:
  1. 단일 `dashboard.html` 파일에 CSS·JS·데이터 인라인.
  2. 페이지 로드 → `App.load()` → 27강 카드 렌더 → 모든 이벤트 핸들러 부착 → 추천 갱신.
  3. 최종 검증: 보드 클릭 → XP 정확, 모달 동작, 뱃지 토스트, 새로고침 후 상태 복원.

### 8.3 모듈 간 통신 규약
- 데이터는 모두 Module B 거쳐서 흐른다(단일 진실 원천).
- UI 모듈(E, F, G, H)은 Module B의 이벤트만 구독, 직접 localStorage를 읽지 않는다.
- 색·폰트는 모두 CSS 변수(`--bg`, `--accent-gold`, ...)로 정의해 Module D만 수정해도 테마 변경 가능.

---

## 9. 파일 결과물

### 9.1 최종 산출
- **`D:\조나단\일회성 프로젝트\공부 진도 체크\dashboard.html`** — 단일 HTML (CSS·JS·데이터 인라인). 더블클릭으로 열리며 localStorage에 진행도 저장.

### 9.2 (선택) 개발 단계 분리 파일
빠른 개발용으로 모듈별 임시 파일을 두었다가 마지막 Module Z에서 한 파일로 합쳐도 됨:
```
공부 진도 체크/
├── plan.md                  ← (이 문서)
├── dashboard.html           ← 최종 산출
└── _build/                  ← 개발 임시 (선택, 최종에는 삭제 가능)
    ├── data.js
    ├── bosses.js
    ├── theme.css
    ├── skeleton.html
    ├── state.js
    ├── boss.js
    ├── pomodoro.js
    ├── suggest.js
    └── badges.js
```

---

## 10. 최종 검증 체크리스트 (Module Z 끝나고)

- [ ] `dashboard.html` 더블클릭 시 27개 강의 카드 표시
- [ ] 5개 「장」 각각 색이 다르고 좌측 색띠 표시
- [ ] 임의 체크박스 토글 시 XP 즉시 증가·바 애니메이션
- [ ] 한 강의 5개 모두 체크 → 강의 카드 외곽선 황금색 강조
- [ ] 한 장의 모든 강의 5/5 → 보스 모달 발사, +200 XP, 뱃지 토스트
- [ ] 레벨 임계값(500, 1100, 1800, ...) 통과 시 칭호 변경 + 위인 트리비아 표시
- [ ] 새로고침해도 진행도/뱃지/포모도로 누적 보존
- [ ] 26개 뱃지 모두 조건 함수 통과 가능 (스모크 테스트)
- [ ] 포모도로 25분 → 0 도달 시 알림 + +5 XP
- [ ] 추천 카드: 첫 미완료 활동을 정확히 가리킴
- [ ] 모든 텍스트 한국어, 의학 용어 정확
- [ ] 모바일 320px 너비에서도 깨지지 않음(반응형)

---

## 11. 구현 위임 가이드 (Cowork 오케스트레이션)

추천 위임 순서:
1. **Wave 1 (병렬)**: Agent-A(데이터), Agent-I(보스 트리비아), Agent-D(CSS 테마) — 각각 단독으로 산출물 생성.
2. **Wave 2 (병렬)**: Agent-B(상태엔진), Agent-C(HTML 골격) — Wave 1의 데이터 형식만 알면 됨.
3. **Wave 3 (병렬)**: Agent-E(보스), Agent-F(포모도로), Agent-G(추천), Agent-H(뱃지) — B의 API에만 의존.
4. **Wave 4 (직렬)**: Agent-Z(최종 번들) — 모두 합쳐 단일 HTML 생성 + 검증 체크리스트 실행.

각 Agent에게는 본 문서의 해당 § 절만 발췌하여 컨텍스트로 전달.

---

## 부록 A. 기본 활동 총량 빠른 계산표

| 단위 | 수 |
|---|---:|
| 강의 수 | 27 |
| 강의당 기본 활동 | 5 |
| **기본 활동 총량** | **135** |
| 강의당 기본 XP | 150 |
| **기본 XP 총량 (100% 완주)** | **4,050** |
| 보스전 보너스 (장당 +200) | 5 × 200 = 1,000 |
| **이론적 최대 (기본 + 보스만)** | **5,050** |
| 보너스 퀴즈/회독, 포모도로 | (무제한) |

## 부록 B. 한 줄 동기부여

> "심장은 멈추면 끝이다. 진도도 그렇다. 3일, 27강, 135 체크박스 — 박출하라."

---

**END OF PLAN**
