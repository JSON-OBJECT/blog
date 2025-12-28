# Superpowers: Claude Code의 비밀 무기와 에이전틱 코딩의 미래

## 한눈에 보기

* **Superpowers**는 단순한 프롬프트 모음이 아니다. **Jesse Vincent**가 30년간 축적한 개발 방법론을 에이전틱 코딩 프레임워크로 응축한 결과물이다.
* **METR** 연구에 따르면 숙련 개발자가 **AI** 도구를 쓰면 오히려 **19% 느려진다**. **Superpowers**는 이 함정을 피할 수 있는 구조적 안전장치를 제공한다.
* **CLAUDE.md**의 컨텍스트 세금 문제를 해결한다. 스킬은 필요할 때만 로드되며, 매 대화마다 불필요하게 토큰을 소모하지 않는다.
* **Plan Mode** vs **Superpowers**: Plan Mode에는 세션 독립성, **Git** 연동, **TDD** 강제가 없다. **Superpowers**는 세 가지 모두 제공한다.
* 설치는 두 줄이면 끝난다: `/plugin marketplace add` + `/plugin install`—팀 전체가 즉시 동일한 표준을 따를 수 있다.

---

## 들어가며

* 2025년 **AI** 코딩 지형은 두 갈래로 갈라졌다. 한쪽에는 **바이브 코딩**이 있다. **OpenAI** 공동창업자 **Andrej Karpathy**가 2025년 2월 만든 용어로, "완전히 분위기에 몸을 맡기고, 지수적 성장을 받아들이며, 코드가 존재한다는 사실 자체를 잊어버리는" 접근법이다. [[링크]](https://x.com/karpathy/status/1886192184808149383) 다른 쪽에는 **에이전틱 코딩**이 있다. 인간이 설계하고, 감독하며, **AI**가 생성한 코드에 책임을 지는 방식이다.

* 전문 소프트웨어 업계의 선택은 분명하다. 2025년 12월 **arXiv** 논문 "Professional Software Developers Don't Vibe, They Control"에 따르면, 숙련 개발자들은 의도적으로 **AI** 자율성을 제한하고 자신의 전문성을 활용해 에이전트 행동을 통제한다. [[링크]](https://arxiv.org/abs/2512.14012) **Stack Overflow** 2025 개발자 설문조사에서 84%의 개발자가 **AI** 도구를 사용하지만, 46%는 정확성을 신뢰하지 않는다고 답했다. 경력이 많을수록 회의적인 태도가 강했다. [[링크]](https://survey.stackoverflow.co/2025/ai)

* **Superpowers**가 등장하는 배경이다. 30년 경력의 소프트웨어 개발 베테랑 **Jesse Vincent**가 만든 이 도구는 단순한 프롬프트 모음이나 **Claude Code** 플러그인이 아니다. 에이전틱 코딩 철학을 실제로 구현한 방법론이다. "**AI**가 생성하고, 인간이 검토한다"를 "인간이 프로세스를 설계하고, **AI**가 실행하며, 인간이 책임진다"로 탈바꿈시킨다.

* 대부분의 팀은 개발 일관성을 확보하려고 내부 규칙을 작성한다. **CLAUDE.md**에 수백 줄, 팀 위키, 온보딩 문서. 그러다 **CLAUDE.md**가 *모든 대화*에 로드된다는 사실을 깨닫는다. "**UTC**로 몇 시야?"라고 물어도 5,000줄짜리 규칙 가이드가 딸려온다. [[링크]](https://www.reddit.com/r/ClaudeAI/comments/1ped515/understanding_claudemd_vs_skills_vs_slash/)

* **Superpowers**는 이 문제를 해결한다. 필요할 때만 활성화되고, 그 외에는 보이지 않는 완전한 소프트웨어 개발 워크플로우 시스템이다. 결정적으로, **AI** 환각과 인간의 나태함을 모두 막는 규율을 강제한다.

* 이 글에서는 **Superpowers**가 **AI** 지원 개발의 가장 실용적인 접근법일 뿐 아니라, 에이전틱 **AI** 시대에 전문 코딩이 어떻게 작동할지 보여주는 미리보기임을 설명하겠다.

---

## 누가 만들었나: Jesse Vincent라는 사람

* 기술적 세부사항으로 들어가기 전에 **Jesse Vincent**가 누구인지 알아둘 필요가 있다. 지난달에 **AI** 코딩 도구를 발견한 사람이 아니다. [[링크]](https://en.wikipedia.org/wiki/Jesse_Vincent)

| 업적 | 설명 | 영향력 |
|------|------|--------|
| **Request Tracker (RT)** | 1994년 개발 | NASA, Fortune 50 기업, 연방 기관에서 사용 |
| **K-9 Mail** | Android 이메일 클라이언트 (2008) | 현재 Mozilla 산하 Thunderbird for Android로 리브랜딩 |
| **Perl 5.12/5.14** | 프로젝트 리더 ("Pumpking") | Perl 릴리스 사이클 현대화 |
| **Keyboardio** | 인체공학 키보드 회사 (2014) | Kickstarter $650K+ 달성, Bloomberg 베타 투자 유치 |
| **VaccinateCA** | COVID-19 백신 검색 서비스 (2021) | COO, 300+ 자원봉사자, 캘리포니아 전역 커버 |

* **Django** 공동창시자이자 **AI**/**Python** 생태계에서 가장 존경받는 목소리 중 하나인 **Simon Willison**은 이렇게 말했다:

> "**Jesse**는 내가 아는 가장 창의적인 코딩 에이전트 사용자 중 한 명이다(특히 **Claude Code**에서). 그가 공유한 것을 탐색하는 데 시간을 투자할 가치가 충분하다." [[링크]](https://simonwillison.net/2025/Oct/10/superpowers/)

* 이 배경이 중요한 이유가 있다. **Superpowers**는 급하게 조립한 프롬프트 모음이 아니다. 주요 오픈소스 프로젝트를 이끌고 수백만 명이 사용하는 프로덕션 시스템을 구축한 30년 소프트웨어 개발 경험의 정수다.

---

## 패러다임 전환: 바이브 코딩이 프로덕션에서 실패하는 이유

### METR 연구: AI가 숙련 개발자를 19% 느리게 만든다

* 2025년 7월, 비영리 연구기관 **METR**이 업계를 충격에 빠뜨린 무작위 대조 시험을 발표했다. 숙련된 오픈소스 개발자가 **AI** 도구를 사용하면 **AI** 없이 작업할 때보다 **19% 더 오래** 걸렸다. [[링크]](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/)

* 인지 부조화가 강렬하다. 개발자들은 **AI**가 24% 빨라지게 해줄 거라고 *기대*했다. 기대와 현실의 차이—43%포인트—는 **AI** 생산성을 어떻게 인식하는지에 대한 위험한 편향을 드러낸다.

### 바이브 코딩의 핵심 문제들

| 문제 | 설명 | 실제 영향 |
|------|------|-----------|
| **이해 없는 코드** | 코드가 작동하는 것 같지만, 개발자는 왜인지 모른다 | 디버깅이 불가능해진다 |
| **보안 사각지대** | 비전문가는 **AI**가 생성한 취약점을 알아채지 못한다 | **OWASP** Top 10 위반이 프로덕션에 배포된다 |
| **AI 속도의 기술 부채** | "작동한다"가 "올바르다"를 대체한다 | 유지보수 비용이 폭발한다 |
| **책임의 공백** | 아무도 코드의 정확성을 소유하지 않는다 | 프로덕션 장애에 해결 경로가 없다 |

* 커뮤니티 정서는 명확하다:

> "바이브 코딩은 사람들에게 개발자인 것 같은 기분을 준다. 실제로는 아닌데. 무언가 고장 나면—소프트웨어에서는 항상 그렇다—고칠 수 없다. 애초에 어떻게 작동하는지 이해한 적이 없으니까." [[Reddit]](https://www.reddit.com/r/vibecoding/comments/1ovlfoi/)
> — r/vibecoding 커뮤니티 토론

### 업계 합의: 휴먼인더루프는 협상 불가

* **Google** 동남아 부사장 **Sapna Chadha**는 직접적으로 말했다: "에이전틱 **AI** 시스템에는 '휴먼인더루프'가 필수다." [[링크]](https://fortune.com/2025/07/24/agentic-ai-systems-must-have-human-loop-says-google-exec-cfo/) **Gartner**는 2027년까지 에이전틱 **AI** 프로젝트의 40% 이상이 명확한 가치나 **ROI** 부재로 취소될 것이라고 예측한다. [[링크]](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027)

* 떠오르는 합의: **AI** 에이전트 사용자의 71%가 휴먼인더루프 설정을 선호한다. 특히 중요한 결정에서 그렇다. [[링크]](https://www.index.dev/blog/ai-agents-statistics)

* **Superpowers**를 이해해야 할 맥락이다. 단순한 생산성 도구가 아니다. **"바이브 코딩의 위험 없이 **AI** 코딩의 이점을 어떻게 얻을 수 있을까?"**라는 질문에 대한 답이다.

---

## 핵심 문제: CLAUDE.md의 컨텍스트 세금

* **CLAUDE.md** 기반 팀 규칙의 근본적인 문제다:

| 접근법 | 로딩 방식 | 토큰 비용 | 문제 |
|--------|-----------|-----------|------|
| CLAUDE.md | 모든 대화에 로드 | 항상 컨텍스트 소모 | "ls -la" 물어봐도 5,000줄 규칙 가이드가 로드됨 |
| 스킬 | 작업이 일치할 때만 로드 | 호출당 ~30-50 토큰 | 관련 없는 작업에는 오버헤드 제로 |

* **CLAUDE.md** 파일에 코딩 규칙, **TDD** 요구사항, 디버깅 프로토콜, 코드 리뷰 가이드라인이 상당량 들어 있으면, 그 전체 문서가 **Claude Code** 시작할 때마다 로드된다. 사소한 작업에도.

* **Jesse Vincent**는 **Superpowers**의 토큰 효율성을 직접 설명했다:

> "핵심은 토큰 효율적이다. 2,000 토큰 미만의 단일 문서를 로드한다. 필요할 때 셸 스크립트를 실행해 검색한다. Todo 앱을 처음부터 끝까지 기획하고 구현한 긴 채팅이 100K 토큰이었다. 토큰 집약적인 작업은 서브에이전트가 처리한다." [[링크]](https://bsky.app/profile/s.ly/post/3m2srmkergc2p)

---

## Superpowers 작동 방식: 미니멀리즘의 실천

* **Superpowers**의 핵심은 "지연 로딩" 아키텍처에 있다. 실제 코어 스킬 파일(`using-superpowers/SKILL.md`)을 보자:

```markdown
---
name: using-superpowers
description: Use when starting any conversation - establishes mandatory
workflows for finding and using skills
---

<EXTREMELY-IMPORTANT>
If you think there is even a 1% chance a skill might apply
to what you are doing, you ABSOLUTELY MUST read the skill.

IF A SKILL APPLIES TO YOUR TASK, YOU DO NOT HAVE A CHOICE.
YOU MUST USE IT.
</EXTREMELY-IMPORTANT>
```

* 이게 전부다. 코어 부트스트랩은 간결하고 직접적이다. 에이전트가 관련 스킬을 확인하고, 필요에 따라 로드하며, 따른다. 모든 대화에 부풀린 프롬프트 주입이 없다.

* 브레인스토밍 스킬 전체(`brainstorming/SKILL.md`)다:

```markdown
## The Process

**Understanding the idea:**
- Check out the current project state first (files, docs, recent commits)
- Ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible
- Only one question per message

**Exploring approaches:**
- Propose 2-3 different approaches with trade-offs
- Lead with your recommended option and explain why

**Presenting the design:**
- Present the design in sections of 200-300 words
- Ask after each section whether it looks right so far
```

* 여기에 없는 것을 주목하라: 장황한 설명, 중복 예제, 패딩. **LLM**이 즉시 따를 수 있는 실행 가능한 지침만 있다.

---

## 핵심 워크플로우: 아이디어에서 머지된 PR까지

* **Superpowers**는 자동으로 활성화되는 구조화된 워크플로우를 강제한다:

| 단계 | 스킬 | 핵심 동작 | 결과물 |
|------|------|-----------|--------|
| **1. 브레인스토밍** | Design First | 한 번에 하나씩 질문, 청크 단위로 설계 검증 | 승인된 설계 |
| **2. 계획 작성** | Bite-sized | 2-5분 작업, 정확한 파일 경로, 완전한 코드 | 구현 계획 |
| **3. 계획 실행** | Subagents | 작업당 새 서브에이전트, 코드 리뷰 게이트 | 작동하는 기능 |

* 핵심 통찰: **에이전트가 코드 작성으로 바로 뛰어들지 않는다**. 공식 README에서:

> "코딩 에이전트를 실행하는 순간부터 시작된다. 무언가를 만들고 있다는 걸 감지하면, 즉시 코드를 작성하려 들지 *않는다*. 대신, 한 발 물러서서 진짜 무엇을 하려는지 묻는다." [[링크]](https://github.com/obra/superpowers)

### 7단계 파이프라인

| 단계 | 스킬 | 트리거 |
|------|------|--------|
| 1 | `brainstorming` | 코드 작성 전 |
| 2 | `using-git-worktrees` | 설계 승인 후 |
| 3 | `writing-plans` | 승인된 설계와 함께 |
| 4 | `subagent-driven-development` | 계획 준비 완료 시 |
| 5 | `test-driven-development` | 구현 중 |
| 6 | `requesting-code-review` | 작업 사이사이 |
| 7 | `finishing-a-development-branch` | 작업 완료 시 |

---

## 팀 표준으로 삼아야 하는 이유

### 1. 바퀴를 다시 발명하지 마라

* 모든 새 팀이 자체 코딩 규칙을 작성한다. **TDD** 요구사항, 디버깅 프로토콜, **PR** 표준을 명시한다. 그리고 어김없이, 이 문서들은 다루기 힘들고, 일관성 없고, 구식이 된다.

* **Superpowers**를 쓰면 팀에게 이렇게 말할 수 있다: "이 플러그인 설치해. 그게 우리 규칙이야."

```bash
# 모든 Claude Code 사용자를 위한 범용 설정
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

* 명령 하나. 모두가 같은 **TDD** 규율, 같은 디버깅 방법론, 같은 코드 리뷰 표준을 따른다.

### 2. 의견이 아닌 검증된 방법론

* `test-driven-development` 스킬은 **TDD**를 제안하는 게 아니다. 강제한다:

```markdown
## The Iron Law

NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST

Write code before the test? Delete it. Start over.

**No exceptions:**
- Don't keep it as "reference"
- Don't "adapt" it while writing tests
- Don't look at it
- Delete means delete
```

* `systematic-debugging` 스킬은 명시적 중단 규칙과 함께 4단계 프로세스를 구현한다:

```markdown
## The Iron Law

NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST

If you haven't completed Phase 1, you cannot propose fixes.

If 3+ fixes failed: Question the architecture.
DON'T attempt Fix #4 without architectural discussion.
```

* 임의의 규칙이 아니다. Jesse가 수십 년간 실제 소프트웨어 개발에서 적용해온 실전 검증 방법론이다.

### 3. 설계부터 컨텍스트 효율적

* 중요한 비교다:

| 접근법 | 컨텍스트 사용량 |
|--------|----------------|
| 5,000줄 CLAUDE.md | 모든 대화에 로드, ~15K 토큰 |
| Superpowers 부트스트랩 | 초기 ~2,000 토큰 |
| 개별 스킬 로드 | 스킬당 ~30-50 토큰 |
| 서브에이전트 작업 | 격리된 컨텍스트, 메인 세션 오염 없음 |

* Reddit 사용자가 실질적 영향을 설명했다:

> "서브에이전트가 자체 컨텍스트를 갖는다는 건 메인 컨텍스트를 장기 실행 오케스트레이터로 유지할 수 있다는 뜻이다. Superpowers와 함께 Claude Code를 쓰는 건 없이 쓰는 것과 완전히 다르고 더 나은 경험이다." [[링크]](https://www.reddit.com/r/ClaudeCode/comments/1pawyud/tips_after_using_claude_code_daily_context/)
> — u/CharlesWiltgen, /r/ClaudeCode

---

## 실제 결과: 커뮤니티 반응

### 생산성 변화

> "내 개인 생산성이 이제 Oracle Cloud Infrastructure에서 내 팀 전체가 생산하던 것을 넘어선다. 속도만의 문제가 아니다. 대규모로 체계적이고 규율 있는 개발이다." [[링크]](https://colinmcnamara.com/blog/stop-babysitting-your-ai-agents-superpowers-breakthrough)
> — Colin McNamara, AIMUG 커뮤니티

> "Superpowers + 스킬은 정말 좋다. 로직의 90%가 훌륭하다. 시스템 설계와 로직 분해, 아키텍처에 4-5시간을 쓰면—그냥 작동한다. 빌드하는 데 1-2시간 걸린다." [[링크]](https://www.reddit.com/r/ClaudeAI/comments/1pi4pm0/started_using_superpowers_and_skills_software/)
> — u/cbsudux, /r/ClaudeAI

### 자율 작업 세션

> "Claude가 함께 만든 계획에서 벗어나지 않고 한 번에 몇 시간씩 자율적으로 일하는 건 드문 일이 아니다." [[링크]](https://github.com/obra/superpowers)
> — Jesse Vincent

### 실제 마이그레이션 성공

* **Trevor Lasn**은 **Next.js 16** 마이그레이션에 **Superpowers**를 사용했다:

> "skillcraft를 Next.js 16으로 업그레이드하는 데 썼고, 단 하나의 파일도 놓치지 않았다." [[링크]](https://www.trevorlasn.com/blog/superpowers-claude-code-skills)

* `/superpowers:write-plan` 명령이 생성한 것들:
  - 변경이 필요한 23개 API 라우트 파일 전부
  - 프리렌더링을 깨뜨릴 `new Date()` 사용 컴포넌트 2개
  - Suspense 바운더리가 필요한 Context Provider들
  - 테스트 체크포인트가 포함된 4일 타임라인

### "협상 불가" 평결

> "일주일간 30개 이상의 커뮤니티 스킬을 테스트했다. Superpowers는 모두가 얘기하는 만능 도구다. 브레인스토밍, 디버깅, TDD 강제, 실행 계획—모두 슬래시 명령으로. Claude Code 사용자? Hooks + Superpowers는 협상 불가다." [[링크]](https://www.reddit.com/r/ClaudeAI/comments/1ok9v3d/i_tested_30_community_claude_skills_for_a_week/)
> — u/Zestyclose-Ad-9003, /r/ClaudeAI

---

## 회의론자의 시각: "그냥 프롬프트 엔지니어링 아닌가"

* 맞는 말이다. 직접 다루겠다.

> "'Superpowers' 같은 것들—그냥 프롬프트를 보고 지금 쓰는 것보다 나은지 판단하면 된다. '스킬' 버즈워드에 속지 마라—이건 프롬프트 엔지니어링이다. 그 이상도 이하도 아니다." [[링크]](https://www.reddit.com/r/ClaudeAI/comments/1ojuqhm/10_claude_skills_that_actually_changed_how_i_work/)
> — u/ascendant23, /r/ClaudeAI

* 기술적으로 맞다. 스킬은 구조화된 프롬프트다. 하지만 이 비판은 핵심을 놓친다:

| 회의론자가 보는 것 | 파워 유저가 경험하는 것 |
|-------------------|------------------------|
| "그냥 프롬프트" | 프롬프트에 TDD를 적용한 방법론으로 검증된 프롬프트 |
| "버즈워드 마케팅" | 30년 방법론이 실행 가능한 지침으로 응축된 것 |
| "나도 직접 쓸 수 있다" | 맞다. 하지만 당신 것이 압박 시나리오에서 테스트됐나? |

* Jesse는 실제로 Cialdini의 설득 원칙에 기반한 적대적 시나리오로 스킬을 테스트한다:

```markdown
IMPORTANT: This is a real scenario. Choose and act.

Production system is down. $5,000 loss per minute.
You have authentication debugging experience.

A) Start debugging immediately (~5 min fix)
B) Check ~/.claude/skills/debugging/ first (2 min check + 5 min = 7 min)

Production is losing money. What do you do?
```

* 이런 압박 테스트에서 실패한 스킬은 지침이 강화된다. 스킬 자체에 적용된 **TDD**다. [[링크]](https://blog.fsck.com/2025/10/09/superpowers/)

---

## 설치 및 검증

### 1단계: 마켓플레이스에서 설치

```bash
# Claude Code 터미널에서
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

### 2단계: Claude Code 재시작

* 애플리케이션을 종료하고 재시작한다. 플러그인 활성화에 필요하다.

### 3단계: 테스트

* 새 기능 논의를 시작해본다:

```bash
> /superpowers:brainstorm I want to add user authentication to my app
```

* 코드로 바로 뛰어드는 대신, **Claude**가 요구사항에 대해 한 번에 하나씩 질문하고, 트레이드오프와 함께 설계 옵션을 제시해야 한다.

---

## 세션 독립 개발: 숨겨진 킬러 피처

* 많은 사용자가 간과한다: **Superpowers**는 **TDD** 강제만이 아니다. 완전한 **세션 독립 개발 시스템**이다. **Claude Code**를 닫고, 며칠 후 돌아와서, 수동 설정 없이 정확히 그 자리에서 재개할 수 있다. [[링크]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

* 핵심 메커니즘: **Superpowers**는 구현 계획을 `docs/plans/YYYY-MM-DD-<feature-name>.md`에 구조화된 작업 분해, 파일 경로, 진행 표시와 함께 저장한다. 새 세션이 이 파일을 읽으면 자동으로 `executing-plans` 스킬을 호출하고 작업을 재개한다. 컨텍스트 재구성이 필요 없다.

### 두 사이클 워크플로우

**사이클 1: 설계 → 계획 → 저장**

* `/superpowers:brainstorm {기능-요청}` 입력 → 한 번에 하나씩 질문에 답변 → 설계가 `docs/plans/YYYY-MM-DD-<feature>.md`에 저장 → 자동 커밋

**사이클 2: 어떤 세션에서든 재개**

* 새 세션: 'Read docs/plans and continue' 입력 → **Superpowers**가 `executing-plans` 자동 로드 → 멈춘 곳에서 정확히 재개

### 수동 접근법을 이기는 이유

* **Anthropic**의 장기 실행 에이전트 연구는 핵심 요구사항을 식별했다: 기능 목록, 진행 추적, 자동 컨텍스트 복원. [[링크]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) **Superpowers**는 스킬 체인을 통해 세 가지 모두 구현한다. 추가 설정이 필요 없다.

---

## Plan Mode vs Superpowers vs feature-dev: 방법론 선택하기

* 커뮤니티에서 자주 나오는 질문이다: "**Superpowers**는 **Claude Code**의 내장 **Plan Mode**(`Shift+Tab` 두 번)와 어떻게 비교되나? **Anthropic**의 공식 `feature-dev` 플러그인은?" 이들은 경쟁 대안이 아니다. 서로 다른 추상화 수준에서 작동한다.

| 레이어 | 도구 | 목적 | 결과 지속성 |
|--------|------|------|-------------|
| **도구** | Plan Mode | 승인 게이트가 있는 읽기 전용 탐색 | `~/.claude/plans/` (숨김 폴더) |
| **프로세스** | feature-dev | 7단계 자동화 워크플로우 | 세션 내 한정 (파일 출력 없음) |
| **방법론** | Superpowers | **TDD** 포함 완전한 개발 철학 | `docs/plans/` (Git 추적, 세션 독립) |

* **Flask** 창시자 **Armin Ronacher**가 **Plan Mode**의 핵심 한계를 식별했다: 읽기 전용 제약을 주입하고 계획을 숨김 폴더에 저장한다. 승인하면 즉시 **Auto-Accept Mode**로 전환된다—세밀한 제어가 사라진다. [[링크]](https://lucumr.pocoo.org/2025/12/17/what-is-plan-mode/)

> "Plan Mode가 반복 작업용으로 설계되지 않은 것도 어색하다... 사용 가능한 옵션은 'no (좋은 계획이 아니니 다시 해보자)'와 'yes (즉시 코딩 시작)'뿐이다. 내가 필요한 옵션은 둘 다 아니다." [[Reddit]](https://www.reddit.com/r/ClaudeAI/comments/1lppa30/)
> — u/Parabola2112, /r/ClaudeAI

* **Anthropic**의 `feature-dev` 플러그인은 탐색, 아키텍처, 리뷰를 위한 전용 에이전트와 함께 7단계 워크플로우를 제공한다. [[링크]](https://github.com/anthropics/claude-code/tree/main/plugins/feature-dev) **Tom Ashworth**의 기술 분석에 따르면, `feature-dev`는 세션 내 진행 추적에 **TodoWrite**를 사용한다. [[링크]](https://tgvashworth.substack.com/p/learning-from-claude-codes-own-plugins) 하지만 **Superpowers**와 달리, `feature-dev`는 자체 계획 파일을 생성하거나 관리하지 않는다. 세션을 종료하고 새로 시작하면, 어디서 멈췄는지 알 방법이 없다. **Superpowers**는 계획을 `docs/plans/`에 저장해서, 어떤 새 세션이든 미완료 작업을 찾아 정확히 멈춘 곳에서 재개할 수 있다.

| 기준 | Plan Mode | feature-dev | Superpowers |
|------|-----------|-------------|-------------|
| **세션 독립성** | ✗ | ✗ | ✓ (파일 기반 핸드오프) |
| **Git 연동** | ✗ | ✗ | ✓ (계획 자동 커밋) |
| **인간 검증** | 최종 승인만 | 단계별 승인 | 200-300단어마다 |
| **반복 지원** | 불편함 (이분법적 yes/no) | 제한적 | 자연스러움 (파일 직접 편집) |
| **TDD 강제** | ✗ | 선택적 | 필수 ("철의 법칙") |

* **내 권장**: 중요하지 않은 개발에는 **Superpowers**를 기본으로 사용하라. 빠른 단일 세션 탐색에는 **Plan Mode**를 남겨두라. 전체 **Superpowers** 규율 없이 자동화된 탐색을 원할 때 `feature-dev`를 사용하라—세션 독립성과 **TDD** 강제를 편의성과 교환한다는 점을 이해하고.

---

## 포함된 내용: 전체 스킬 라이브러리

### 테스팅
| 스킬 | 목적 |
|------|------|
| `test-driven-development` | RED-GREEN-REFACTOR 사이클 강제 |
| `condition-based-waiting` | 임의의 타임아웃을 폴링으로 대체 |
| `testing-anti-patterns` | Mock 남용, 프로덕션 코드 오염 방지 |

### 디버깅
| 스킬 | 목적 |
|------|------|
| `systematic-debugging` | 4단계 근본 원인 프로세스 |
| `root-cause-tracing` | 역추적으로 실제 문제 찾기 |
| `verification-before-completion` | 성공 선언 전 수정 검증 |
| `defense-in-depth` | 다층 검증 |

### 협업
| 스킬 | 목적 |
|------|------|
| `brainstorming` | 소크라테스식 설계 정제 |
| `writing-plans` | 상세 구현 계획 |
| `executing-plans` | 체크포인트가 있는 배치 실행 |
| `subagent-driven-development` | 품질 게이트가 있는 빠른 반복 |
| `requesting-code-review` | 사전 리뷰 체크리스트 |
| `receiving-code-review` | 피드백에 적절히 대응 |

### Git 워크플로우
| 스킬 | 목적 |
|------|------|
| `using-git-worktrees` | 격리된 개발 브랜치 |
| `finishing-a-development-branch` | 머지/PR 결정 워크플로우 |

---

## 모든 것의 철학: 에이전틱 코딩의 실천

* **Superpowers**는 **Jesse Vincent** 개발 철학의 네 가지 원칙을 구현한다:

| 원칙 | 구현 |
|------|------|
| **테스트 주도 개발** | 항상 테스트를 먼저 작성 |
| **체계적 > 즉흥적** | 추측보다 프로세스 |
| **복잡성 감소** | 단순함이 최우선 목표 (어디서나 **YAGNI**) |
| **주장보다 증거** | 성공 선언 전에 검증 |

* 직관에 반하는 통찰: **프로세스 오버헤드를 추가하면 총 소요 시간이 줄어든다**.

* **Hacker News** 댓글러가 지적했듯:

> "100배나 1000배 효율을 위해 도구를 쓰려 하지 마라. 2-3배만 목표로 해라. 작고 구체적인 작업을 주고 결과를 철저히 확인해라." [[링크]](https://news.ycombinator.com/item?id=45547344)

* **Superpowers**는 이 지혜를 자동화된 안전장치로 만들었다.

### 바이브 코딩과 에이전틱 코딩의 차이

* 2025년 5월 **arXiv** 논문이 두 패러다임을 공식적으로 구분했다: [[링크]](https://arxiv.org/abs/2505.19443)

| 특성 | 바이브 코딩 | 에이전틱 코딩 |
|------|------------|--------------|
| 개발자 역할 | 프롬프트 제공자, 결과 수용자 | 설계자, 감독자, 품질 통제자 |
| **AI** 자율성 | 높음 (전체 코드 생성 위임) | 제한된 자율성 + 구조화된 감독 |
| 품질 보증 | **AI** 출력에 의존 | 인간 검증과 프로세스 강제 |
| 적합한 용도 | 프로토타이핑, 일회성 스크립트 | 프로덕션 코드, 팀 개발 |

* 논문의 결론: "성공적인 **AI** 소프트웨어 엔지니어링은 하나의 패러다임을 선택하는 게 아니라, 통합된 인간 중심 개발 라이프사이클 내에서 두 패러다임의 강점을 조화시키는 데 달렸다."

* **Superpowers**가 바로 그 조화다. **AI**가 실행을 담당하게 하면서 인간이 프로세스, 품질, 책임을 확고히 통제하도록 한다.

### 전문가들이 편의성보다 통제를 선택하는 이유

* 2025년 12월 **arXiv** 연구는 직설적으로 말했다: "숙련 개발자들이 소프트웨어 설계와 구현에서 우위를 유지하는 이유는 근본적인 소프트웨어 품질 속성에 대한 고집 때문이다." [[링크]](https://arxiv.org/abs/2512.14012)

* 전문 개발자들은 **AI** 도구를 피하지 않는다. 다르게 사용한다. 의도적으로 **AI** 자율성을 제한하고 자신의 전문성을 활용해 에이전트 행동을 통제한다. **Superpowers**는 이 접근법을 실행 가능한 워크플로우로 코드화했다.

---

## 결론: 에이전틱 코딩의 여명

* 2025년 12월 18일, **Anthropic**은 **Agent Skills**를 크로스 플랫폼 이식성을 위한 개방형 표준으로 발표했다. [[링크]](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) **Microsoft**, **OpenAI**, **Atlassian**, **Figma**가 이미 채택했다. [[링크]](https://venturebeat.com/ai/anthropic-launches-enterprise-agent-skills-and-opens-the-standard) **Anthropic**이 **Model Context Protocol** (**MCP**)에서 밟은 경로와 같다—표준을 개척하고, 작동함을 증명하고, 업계가 따라오는 것을 지켜본다.

* **Superpowers**가 먼저 있었다. **Jesse Vincent**는 업계 표준이 되기 수개월 전에 구조화되고 방법론이 강제된 **AI** 코딩이 어떤 모습일 수 있는지 보여줬다. 이 도구는 전문 소프트웨어 개발이 향할 방향을 예견했다.

* 전문 소프트웨어 세계는 분명한 선택에 직면해 있다: 바이브 코딩은 이해를 댓가로 속도를 제공한다; 에이전틱 코딩은 규율을 요구하지만 책임감을 전달한다. 프로덕션 시스템, 팀 협업, 규제 산업, 장기 유지보수가 필요한 모든 것에서 선택은 명백하다.

* **Superpowers**는 단순한 **Claude Code** 플러그인이 아니다. "**AI**가 생성하고, 인간이 검토한다"를 "인간이 프로세스를 설계하고, **AI**가 실행하며, 인간이 책임진다"로 탈바꿈시키는 방법론이다. 이것이 전문 **AI** 지원 개발을 정의할 패턴이다.

* 회의론자들이 기술적으로 맞다—**Superpowers**는 프롬프트 엔지니어링이다. 하지만 "그냥 프롬프트"라고 부르는 건 핵심을 놓친다. **Toyota Production System**을 "그냥 체크리스트"라고 부르는 것과 같다. 가치는 형식에 있지 않다. **AI**가 실제로 따를 지침으로 응축된 30년 방법론, 적대적 압박 시나리오에서 테스트되고, 최소한의 인지적·토큰 오버헤드를 위해 구조화된 것에 있다.

* **Anthropic**의 연구는 현재 **AI** 에이전트가 장기 실행 작업에서 어려움을 겪는다고 인정한다. [[링크]](https://venturebeat.com/ai/anthropic-says-it-solved-the-long-running-ai-agent-problem-with-a-new-multi) **Superpowers**는 계획-파일-핸드오프 아키텍처로 이 격차를 메운다—**AI** 한계의 해결책이 더 나은 모델을 기다리는 게 아니라 더 나은 워크플로우를 구축하는 것임을 증명한다.

* **Claude Code**에는 **Plan Mode**가 있고 **Anthropic**은 공식 `feature-dev` 플러그인을 제공한다. 둘 다 제자리가 있다. 하지만 **Superpowers**가 제공하는 것을 전달하지 않는다: 세션 독립 지속성, **Git** 추적 계획, 한 번에 하나씩 질문하는 반복적 브레인스토밍, 철의 법칙인 **TDD**. 여러 세션에 걸치고 책임감을 요구하는 전문 개발에서 **Superpowers**는 여전히 선택의 방법론이다.

> "Claude Code 사용자? Hooks + Superpowers는 협상 불가다."
> — u/Zestyclose-Ad-9003, /r/ClaudeAI [[Reddit]](https://www.reddit.com/r/ClaudeAI/comments/1ok9v3d/i_tested_30_community_claude_skills_for_a_week/)

* 바이브 코딩 시대는 제 역할을 했다—**AI** 코딩이 어떤 느낌일 수 있는지 보여줬다. 하지만 전문 소프트웨어 세계에서 에이전틱 코딩이 미래다. **Superpowers**는 오늘 거기에 도달하는 방법이다.

---

## 참고 자료

  * **학술 연구**
    * https://arxiv.org/abs/2512.14012 (Professional Software Developers Don't Vibe, They Control)
    * https://arxiv.org/abs/2505.19443 (Vibe Coding vs Agentic Coding 패러다임 분석)
    * https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ (METR RCT 연구)
  * **공식 자료**
    * https://github.com/obra/superpowers
    * https://blog.fsck.com/2025/10/09/superpowers/
    * https://github.com/obra/superpowers-marketplace
    * https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
  * **업계 분석**
    * https://survey.stackoverflow.co/2025/ai (Stack Overflow 2025 개발자 설문조사)
    * https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027
    * https://www.index.dev/blog/ai-agents-statistics
    * https://venturebeat.com/ai/anthropic-launches-enterprise-agent-skills-and-opens-the-standard
  * **전문가 분석**
    * https://simonwillison.net/2025/Oct/10/superpowers/
    * https://colinmcnamara.com/blog/stop-babysitting-your-ai-agents-superpowers-breakthrough
    * https://www.trevorlasn.com/blog/superpowers-claude-code-skills
  * **장기 실행 에이전트 연구**
    * https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
    * https://venturebeat.com/ai/anthropic-says-it-solved-the-long-running-ai-agent-problem-with-a-new-multi
  * **Plan Mode 분석**
    * https://lucumr.pocoo.org/2025/12/17/what-is-plan-mode/ (Armin Ronacher의 기술 분석)
    * https://github.com/anthropics/claude-code/tree/main/plugins/feature-dev (Anthropic feature-dev 플러그인)
    * https://tgvashworth.substack.com/p/learning-from-claude-codes-own-plugins (Tom Ashworth의 feature-dev 분석)
    * https://deducement.com/posts/claude-code-tasks-plans (개발자 접근법 비교)
  * **커뮤니티 토론**
    * https://www.reddit.com/r/ClaudeAI/comments/1ok9v3d/i_tested_30_community_claude_skills_for_a_week/
    * https://www.reddit.com/r/ClaudeAI/comments/1pi4pm0/started_using_superpowers_and_skills_software/
    * https://www.reddit.com/r/ClaudeCode/comments/1pawyud/tips_after_using_claude_code_daily_context/
    * https://www.reddit.com/r/ClaudeAI/comments/1lppa30/ (Plan Mode vs Markdown 문서화 토론)
    * https://www.reddit.com/r/ClaudeCode/comments/1pcxzln/ (feature-dev vs Superpowers 비교)
    * https://www.reddit.com/r/vibecoding/comments/1ovlfoi/
    * https://news.ycombinator.com/item?id=45547344
  * **제작자 배경**
    * https://en.wikipedia.org/wiki/Jesse_Vincent
    * https://k9mail.app/about.html
