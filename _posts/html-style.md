# HTML 시각화 스타일 정밀 분석

## 1. 디자인 컨셉: "레트로 게이밍 테크 매거진"

**테마:** 사이버펑크 + 아케이드 게임 UI
**매거진 이름:** "PLAYER ONE TECH"
**이슈 포맷:** `ISSUE #2025.12 // AI MEMORY WARS`

---

## 2. 색상 시스템 (CSS Variables)

| 변수명 | 색상코드 | 용도 |
|--------|----------|------|
| `--neon-cyan` | `#00f5ff` | **주요 강조**, 타이틀, 링크 hover |
| `--neon-magenta` | `#ff00ff` | **보조 강조**, 하이라이트, 인용구 |
| `--neon-yellow` | `#ffff00` | 경고, 우선순위 표시, 레이블 |
| `--neon-green` | `#39ff14` | 체크마크, 터미널/코드, 액션 아이템 |
| `--neon-orange` | `#ff6600` | 경고 박스 |
| `--deep-purple` | `#0a0015` | 배경 기본 |
| `--mid-purple` | `#1a0030` | 카드 배경 |

---

## 3. 폰트 시스템

| 폰트 | 용도 |
|------|------|
| **Black Han Sans** | 한글 대형 타이틀 |
| **Orbitron** | 영문 타이틀, 숫자, 섹션 번호, 레이블 |
| **Rajdhani** | 영문 본문, 서브텍스트 |
| **Press Start 2P** | 픽셀 폰트 - 이슈 정보, 소형 레이블 |
| **Noto Sans KR** | 한글 본문 |

---

## 4. 시각 효과

| 효과 | 구현 방식 |
|------|-----------|
| **Scanline overlay** | `body::before`로 CRT 모니터 느낌의 수평선 |
| **Grid background** | 고정 배경에 사이언/마젠타 격자선 |
| **Floating particles** | 10개 파티클이 아래→위로 떠오르는 애니메이션 |
| **Glitch effect** | 타이틀에 `@keyframes glitch`로 흔들림 |
| **Glow effects** | `text-shadow`로 네온 발광 효과 |
| **Clip-path polygons** | 카드/버튼에 각진 SF 느낌의 모서리 |
| **Breathing animation** | Hero 배경 원형 그라디언트가 확대/축소 |

---

## 5. 컴포넌트 구조

### A. Hero Section
```
┌─────────────────────────────────────────┐
│  PLAYER ONE TECH    ISSUE #2025.12      │  ← 헤더
├─────────────────────────────────────────┤
│                                         │
│      제미나이는 왜                        │  ← 글리치 타이틀
│      당신을 잊는가  (마젠타)              │
│                                         │
│  저장된 정보와 젬스의 숨겨진 한계...       │  ← 서브타이틀
│                                         │
│  ┌────────┐ ┌────────┐ ┌────────┐       │
│  │1M TOKEN│ │10~75   │ │~1,500  │       │  ← Stat Boxes
│  └────────┘ └────────┘ └────────┘       │
└─────────────────────────────────────────┘
```

### B. Summary Section ("MISSION BRIEFING")
- 마젠타 테두리 카드
- 헥사곤 아이콘 + 번호 (01, 02, 03...)
- 항목별 hover시 오른쪽으로 이동

### C. Content Section
```
┌─────────────────────────────────────────┐
│  01  서론                               │
│      INTRODUCTION  (영문 서브타이틀)     │
├─────────────────────────────────────────┤
│  [그라디언트 구분선]                     │
└─────────────────────────────────────────┘
```

### D. 특수 컴포넌트들

| 컴포넌트 | 클래스 | 용도 |
|----------|--------|------|
| **Info Box** | `.info-box` | 일반 정보 (사이언 왼쪽 테두리) |
| **Warning Box** | `.info-box.warning` | 경고 (오렌지 왼쪽 테두리) |
| **Code Block** | `.code-block` | 터미널 스타일 (그린, "SYSTEM.PROMPT" 라벨) |
| **Trigger Box** | `.trigger-box` | 명령어 입력 스타일 (점선 테두리) |
| **Callout** | `.callout` | 인용구 (큰 따옴표 + 출처) |
| **Achievement** | `.achievement` | 게임 업적 잠금해제 스타일 |
| **Comparison Bar** | `.comparison-bar` | 게임 스탯 바 (프로그레스 바) |
| **Flowchart** | `.flowchart` | 문제해결 단계별 플로우 |
| **Checklist** | `.checklist` | 체크박스 액션 아이템 |
| **Table** | `.table-container` | 게임 스탯 테이블 |

---

## 6. MD → HTML 변환 규칙

| MD 요소 | HTML 변환 |
|---------|-----------|
| `# 제목` | Hero Section (글리치 타이틀 + stat boxes) |
| `## 섹션` | `.content-section` + 번호 + 한/영 타이틀 |
| `### 소제목` | `<h3>` (Orbitron 폰트, 시안 컬러) |
| `**강조**` | `<strong>` (시안 컬러) |
| `[Link](url)` | `<a>` (마젠타, hover시 시안, ↗ 아이콘) |
| 표 | `.table-container` + 스타일드 테이블 |
| 코드블록 | `.code-block` (터미널 스타일) |
| 인용구 | `.callout` (마젠타 따옴표) |
| 핵심 요약 | `.summary-section` + 번호 아이콘 리스트 |
| 경고/참고 | `.info-box` 또는 `.info-box.warning` |

---

## 7. 인터랙션 요소

- **Scroll Progress Bar**: 상단 고정, 3색 그라디언트
- **Back to Top**: 우하단 헥사곤 버튼 (스크롤시 나타남)
- **Hover Effects**: 카드 위로 부상 + 그림자/글로우 강화
- **Fade-in Animation**: 스크롤시 섹션별 페이드인
- **Easter Egg**: 좌하단 숨겨진 텍스트

---

## 8. 동일 스타일로 다른 MD 변환시 체크리스트

1. **Hero 구성**: 핵심 숫자 3개를 stat-box로 추출
2. **핵심 요약 5개**: MISSION BRIEFING 섹션으로 변환
3. **섹션 번호링**: 01, 02, 03... 순서대로 영문 서브타이틀 추가
4. **표 → 게임 스탯 테이블**: ✓/✗/△ 기호는 `.check`/`.cross`/`.partial`
5. **인용구 → Callout**: 출처 명시
6. **코드 → 터미널 블록**: 라벨 텍스트 지정 (예: "SYSTEM.PROMPT")
7. **비교 데이터 → Comparison Bar**: 시각적 바 차트로
8. **액션 아이템 → Checklist**: 우선순위 + 경로 표시
9. **참고자료 → References Grid**: 카테고리별 분류
