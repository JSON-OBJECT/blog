# Superpowers: Claude Code의 비밀 병기와 에이전틱 코딩의 미래

## 서론

* 2025년 AI 코딩 생태계는 두 갈래로 확연히 갈라졌다. 한쪽에는 **바이브 코딩**이 있다. OpenAI 공동창업자 안드레이 카파시(Andrej Karpathy)가 2025년 2월 처음 명명한 이 방식은 "바이브에 온전히 몸을 맡기고, 기하급수적 성장을 받아들이며, 코드가 존재한다는 사실조차 잊는" 개발 스타일이다. [[Link]](https://x.com/karpathy/status/1886192184808149383) 다른 한쪽에는 **에이전틱 코딩**이 있다. 인간이 설계하고, 감독하며, AI가 생성한 코드에 대한 책임을 지는 방식이다.

* 현업 개발자들의 선택은 명확하다. 2025년 12월 arXiv에 발표된 "Professional Software Developers Don't Vibe, They Control" 논문에 따르면 경험 많은 개발자일수록 AI 자율성을 의도적으로 제한하고, 자신의 전문성으로 에이전트 행동을 통제한다. [[Link]](https://arxiv.org/abs/2512.14012) Stack Overflow 2025 개발자 설문조사는 84%의 개발자가 AI 도구를 쓰지만, 46%는 그 정확성을 불신한다는 사실을 보여준다. 경력이 길수록 회의론이 더 강하다. [[Link]](https://survey.stackoverflow.co/2025/ai)

* 바로 이 지점에서 **Superpowers**가 등장한다. 30년 경력의 소프트웨어 개발 베테랑 제시 빈센트(Jesse Vincent)가 만든 Superpowers는 단순한 프롬프트 모음이나 Claude Code 플러그인이 아니다. 에이전틱 코딩 철학을 실제로 구현한 방법론이다. "AI가 생성하고, 인간이 확인하는" 패턴을 "인간이 프로세스를 설계하고, AI가 실행하며, 인간이 책임지는" 구조로 바꿔놓는다.

* 대부분의 팀이 개발 일관성 문제를 사내 컨벤션 문서로 해결하려 한다. **CLAUDE.md**에 수백 줄을 쓰거나, 팀 위키나 온보딩 문서를 만든다. 그러다 문제를 발견한다. CLAUDE.md는 *모든 대화*에서 로딩된다. "UTC로 지금 몇 시야?"라고 물어도 컨텍스트 토큰을 소모한다. [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1ped515/understanding_claudemd_vs_skills_vs_slash/)

* Superpowers는 이 문제를 완전한 소프트웨어 개발 워크플로우 시스템으로 해결한다. 관련 있을 때만 활성화되고, 그 외에는 보이지 않는다. 결정적으로 AI 환각과 인간의 나태함 모두를 막는 규율을 강제한다.

* 이 글에서는 Superpowers가 AI 지원 개발에서 가장 실용적인 접근법일 뿐 아니라, 에이전틱 AI 시대의 프로페셔널 코딩이 어떤 모습일지 미리 보여주는 이유를 설명한다.

---

## 만든 사람: 제시 빈센트

* 기술적 세부사항에 들어가기 전에 `Jesse Vincent`가 누구인지 알아야 한다. 지난달 AI 코딩 도구를 발견한 사람이 아니다. [[Link]](https://en.wikipedia.org/wiki/Jesse_Vincent)

| 성과 | 설명 | 영향력 |
|------|------|--------|
| **Request Tracker (RT)** | 1996년 개발 | NASA, Fortune 50 기업, 연방 정부 기관에서 사용 |
| **K-9 Mail** | 안드로이드 이메일 클라이언트 (2008) | Mozilla 산하 Thunderbird for Android로 리브랜딩 |
| **Perl 5.12/5.14** | 프로젝트 리더 ("Pumpking") | Perl 릴리스 사이클 현대화 |
| **Keyboardio** | 인체공학 키보드 회사 (2014) | 킥스타터 65만 달러 이상, Bloomberg 베타 투자 유치 |
| **VaccinateCA** | 코로나19 백신 접종소 검색 서비스 (2021) | COO로 300명 이상 자원봉사자와 캘리포니아 전역 커버 |

* Django 공동 창시자이자 AI/Python 생태계에서 가장 존경받는 목소리 중 하나인 `Simon Willison`은 이렇게 말했다:

> "Jesse는 내가 아는 코딩 에이전트(특히 Claude Code) 사용자 중 가장 창의적인 사람이다. 그가 공유한 것을 살펴보는 데 시간을 투자할 가치가 충분하다." [[Link]](https://simonwillison.net/2025/Oct/10/superpowers/)

* 이게 중요한 이유는 Superpowers가 급하게 조립한 프롬프트 모음이 아니기 때문이다. 30년 소프트웨어 개발 경험의 정수다. 주요 오픈소스 프로젝트 리딩과 수백만 명이 쓰는 프로덕션 시스템 구축 경험이 녹아 있다.

---

## 패러다임 전환: 바이브 코딩이 프로덕션에서 실패하는 이유

### METR 연구: AI가 숙련 개발자를 19% 느리게 만든다

* 2025년 7월, 비영리 연구기관 METR이 업계를 충격에 빠뜨린 무작위 대조 실험 결과를 발표했다. 숙련된 오픈소스 개발자가 AI 도구를 쓰면 AI 없이 작업할 때보다 **19% 더 오래** 걸렸다. [[Link]](https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/)

* 인지 부조화가 극명하다. 개발자들은 AI가 자신을 24% 빠르게 만들어줄 거라고 *기대*했다. 기대와 현실의 괴리가 43%포인트. AI 생산성에 대한 우리의 인식이 얼마나 위험하게 편향되어 있는지 보여준다.

### 바이브 코딩의 핵심 문제들

| 이슈 | 설명 | 실제 영향 |
|------|------|-----------|
| **이해 없는 코드** | 코드가 작동하는 것처럼 보이지만, 개발자가 이유를 모름 | 디버깅 불가능 |
| **보안 사각지대** | 비전문가가 AI 생성 취약점을 인식 못함 | OWASP Top 10 위반이 프로덕션에 배포됨 |
| **AI 속도의 기술 부채** | "작동한다"가 "올바르다"를 대체함 | 유지보수 비용 폭발 |
| **책임 공백** | 코드 정확성에 대한 소유자가 없음 | 프로덕션 장애 해결 경로 부재 |

* Reddit의 한 댓글은 이렇게 요약했다: "바이브 코딩은 사람들을 자신이 개발자라고 착각하게 만든다. 문제가 터지면—소프트웨어에서는 항상 터진다—고칠 수 없다. 애초에 어떻게 작동하는지 이해한 적이 없으니까." [[Reddit]](https://www.reddit.com/r/vibecoding/comments/1ovlfoi/)

### 업계 컨센서스: 휴먼 인 더 루프는 타협 불가

* Google CFO는 직접적으로 언급했다: "에이전틱 AI 시스템에는 반드시 '휴먼 인 더 루프'가 있어야 한다." [[Link]](https://fortune.com/2025/07/24/agentic-ai-systems-must-have-human-loop-says-google-exec-cfo/) Gartner는 2027년까지 에이전틱 AI 프로젝트의 40% 이상이 명확한 가치나 ROI 부족으로 취소될 것으로 예측한다. [[Link]](https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027)

* 형성 중인 컨센서스: AI 에이전트 사용자의 71%가 휴먼 인 더 루프 설정을 선호한다. 특히 고위험 결정에서. [[Link]](https://www.index.dev/blog/ai-agents-statistics)

* Superpowers는 이 맥락에서 이해해야 한다. 단순한 생산성 도구가 아니다. **"바이브 코딩의 위험 없이 AI 코딩의 이점을 어떻게 얻을 것인가?"**라는 질문에 대한 답이다.

---

## 핵심 문제: CLAUDE.md의 컨텍스트 세금

* CLAUDE.md 기반 팀 컨벤션의 근본 문제는 이렇다:

| 접근법 | 로딩 동작 | 토큰 비용 | 문제 |
|--------|-----------|-----------|------|
| CLAUDE.md | 모든 대화에서 로딩 | 항상 컨텍스트 소모 | "ls -la" 물어도 5,000줄짜리 컨벤션 가이드가 로딩됨 |
| Skills | 작업이 매칭될 때만 로딩 | 호출당 ~30-50 토큰 | 관련 없는 작업에는 오버헤드 제로 |

* 코딩 컨벤션, TDD 요구사항, 디버깅 프로토콜, 코드 리뷰 가이드라인이 담긴 상당한 크기의 CLAUDE.md 파일이 있다면, 그 문서 전체가 Claude Code 시작할 때마다 로딩된다. 사소한 작업에도.

* Jesse Vincent가 Superpowers의 토큰 효율성을 직접 설명했다:

> "핵심은 토큰을 매우 적게 쓴다. 2,000토큰 미만의 단일 문서를 로딩한다. 필요할 때 셸 스크립트로 검색을 실행한다. Todo 앱을 처음부터 끝까지 기획하고 구현한 긴 채팅이 10만 토큰이었다. 토큰 집약적 작업은 서브에이전트가 처리한다." [[Link]](https://bsky.app/profile/s.ly)

---

## Superpowers 작동 원리: 미니멀리즘의 실천

* Superpowers의 탁월함은 "지연 로딩" 아키텍처에 있다. 실제 핵심 스킬 파일(`using-superpowers/SKILL.md`)을 보자:

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

* 이게 전부다. 핵심 부트스트랩은 간결하고 직접적이다. 에이전트가 관련 스킬을 확인하고, 필요할 때 로딩하며, 따른다. 모든 대화에 부풀린 프롬프트를 주입하지 않는다.

* 브레인스토밍 스킬 전문(`brainstorming/SKILL.md`)은 이렇다:

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

* 여기 없는 것에 주목하라: 장황한 설명, 중복된 예시, 패딩이 없다. LLM이 즉시 따를 수 있는 실행 가능한 지시만 있다.

---

## 핵심 워크플로우: 아이디어에서 머지된 PR까지

* Superpowers는 자동으로 활성화되는 구조화된 워크플로우를 강제한다:

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  1. Brainstorm   │ ──▶ │  2. Write Plan   │ ──▶ │  3. Execute Plan │
│  (Design First)  │     │  (Bite-sized)    │     │  (Subagents)     │
└──────────────────┘     └──────────────────┘     └──────────────────┘
         │                        │                        │
         ▼                        ▼                        ▼
    One question at           2-5 minute tasks,        Fresh subagent
    a time, validate         exact file paths,         per task, code
    design in chunks         complete code             review gates
```

* 핵심 통찰: **에이전트가 코드 작성으로 바로 뛰어들지 않는다**. 공식 README에서:

> "코딩 에이전트를 켜는 순간부터 시작된다. 뭔가를 만들려 한다는 걸 감지하면 바로 코드를 쓰려고 *하지 않는다*. 대신 한 발 물러서서 정말 무엇을 하려는 건지 묻는다." [[Link]](https://github.com/obra/superpowers)

### 7단계 파이프라인

| 단계 | 스킬 | 트리거 |
|------|------|--------|
| 1 | `brainstorming` | 코드 작성 전 |
| 2 | `using-git-worktrees` | 설계 승인 후 |
| 3 | `writing-plans` | 승인된 설계와 함께 |
| 4 | `subagent-driven-development` | 계획 준비 완료 시 |
| 5 | `test-driven-development` | 구현 중 |
| 6 | `requesting-code-review` | 작업 사이 |
| 7 | `finishing-a-development-branch` | 작업 완료 시 |

---

## 팀 표준으로 삼아야 하는 이유

### 1. 바퀴를 다시 발명하지 마라

* 모든 새 팀이 자체 코딩 컨벤션을 작성한다. TDD 요구사항, 디버깅 프로토콜, PR 표준을 명시한다. 그리고 필연적으로 이 문서들은 비대해지고, 일관성을 잃고, 구식이 된다.

* Superpowers를 쓰면 팀에 이렇게 말할 수 있다: "이 플러그인 설치해. 그게 우리 컨벤션이야."

```bash
# 모든 Claude Code 사용자를 위한 범용 설정
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

* 명령 하나로 끝난다. 모두가 같은 TDD 규율, 같은 디버깅 방법론, 같은 코드 리뷰 표준을 따른다.

### 2. 의견이 아닌 검증된 방법론

* `test-driven-development` 스킬은 TDD를 제안하는 게 아니라 강제한다:

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

* `systematic-debugging` 스킬은 명시적 중단 규칙이 있는 4단계 프로세스를 구현한다:

```markdown
## The Iron Law

NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST

If you haven't completed Phase 1, you cannot propose fixes.

If 3+ fixes failed: Question the architecture.
DON'T attempt Fix #4 without architectural discussion.
```

* 이건 임의의 규칙이 아니다. Jesse가 수십 년간 실제 소프트웨어 개발에 적용해온 검증된 방법론이다.

### 3. 설계부터 컨텍스트 효율적

* 중요한 비교는 이렇다:

| 접근법 | 컨텍스트 사용량 |
|--------|-----------------|
| 5,000줄 CLAUDE.md | 모든 대화에서 로딩, ~15K 토큰 |
| Superpowers 부트스트랩 | 초기 ~2,000 토큰 |
| 개별 스킬 로딩 | 스킬당 ~30-50 토큰 |
| 서브에이전트 작업 | 격리된 컨텍스트, 메인 세션 오염 없음 |

* Reddit 사용자가 실제 영향을 설명했다:

> "서브에이전트가 자체 컨텍스트를 가진다는 건 메인 컨텍스트를 장수 오케스트레이터로 유지할 수 있다는 뜻이다. Superpowers와 함께 Claude Code를 쓰는 건 없이 쓰는 것과 매우 다르고 더 나은 경험이다." [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pawyud/tips_after_using_claude_code_daily_context/)
> — u/CharlesWiltgen

---

## 실제 결과: 커뮤니티 반응

### 생산성 전환

> "내 개인 생산성이 이제 Oracle Cloud Infrastructure에서 팀 전체가 낼 수 있던 것을 넘어선다. 단순히 속도 문제가 아니다. 체계적이고 규율 있는 대규모 개발이다." [[Link]](https://colinmcnamara.com/blog/stop-babysitting-your-ai-agents-superpowers-breakthrough)
> — Colin McNamara, AIMUG 커뮤니티

> "Superpowers + skills가 정말 좋다. 로직의 90%가 훌륭하다. 시스템 설계와 로직 분해, 아키텍처에 4-5시간 쓰면—그냥 작동한다. 빌드에 1-2시간이면 된다." [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1pi4pm0/started_using_superpowers_and_skills_software/)
> — u/cbsudux, r/ClaudeAI

### 자율 작업 세션

> "Claude가 함께 세운 계획에서 벗어나지 않고 몇 시간씩 자율적으로 작업하는 건 흔한 일이다." [[Link]](https://github.com/obra/superpowers)
> — Jesse Vincent

### 실전 마이그레이션 성공

* **Trevor Lasn**은 **Next.js 16** 마이그레이션에 Superpowers를 썼다:

> "skillcraft를 Next.js 16으로 업그레이드하는 데 썼는데 파일 하나도 놓치지 않았다." [[Link]](https://www.trevorlasn.com/blog/superpowers-claude-code-skills)

* `/superpowers:write-plan` 명령이 생성한 것:
  - 변경이 필요한 API 라우트 파일 23개 전부
  - 프리렌더링을 깨뜨릴 `new Date()` 쓰는 컴포넌트 2개
  - Suspense 경계가 필요한 Context Provider들
  - 테스팅 체크포인트가 포함된 4일 일정

### "타협 불가" 판정

> "일주일간 30개 이상 커뮤니티 스킬을 테스트했다. Superpowers가 모두가 말하는 스위스 아미 나이프다. 브레인스토밍, 디버깅, TDD 강제, 실행 계획—전부 슬래시 커맨드로. Claude Code 사용자? Hooks + Superpowers는 타협 불가다." [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1ok9v3d/i_tested_30_community_claude_skills_for_a_week/)
> — u/Zestyclose-Ad-9003, r/ClaudeAI

---

## 회의론자의 시선: "그냥 프롬프트 엔지니어링 아닌가"

* 정당한 지적이다. 직접 다뤄보자.

> "'Superpowers'와 유사한 것들—프롬프트를 보고 지금 쓰는 것보다 나은지 판단하면 된다. 'skills' 버즈워드에 속지 마라—이건 프롬프트 엔지니어링이다. 그 이상도 이하도 아니다." [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1ojuqhm/10_claude_skills_that_actually_changed_how_i_work/)
> — u/ascendant23, r/ClaudeAI

* 기술적으로 맞다. Skills는 구조화된 프롬프트다. 하지만 이 비판은 핵심을 놓친다:

| 회의론자가 보는 것 | 파워 유저가 경험하는 것 |
|-------------------|------------------------|
| "그냥 프롬프트" | 프롬프트에 TDD를 적용한 방법론으로 검증된 프롬프트 |
| "버즈워드 마케팅" | 30년 방법론이 실행 가능한 지시로 정제됨 |
| "내가 직접 쓸 수 있어" | 그렇다, 하지만 당신 것이 압박 시나리오에서 테스트됐나? |

* Jesse는 실제로 치알디니(Cialdini)의 설득 원칙에 기반한 적대적 시나리오로 스킬을 테스트한다:

```markdown
IMPORTANT: This is a real scenario. Choose and act.

Production system is down. $5,000 loss per minute.
You have authentication debugging experience.

A) Start debugging immediately (~5 min fix)
B) Check ~/.claude/skills/debugging/ first (2 min check + 5 min = 7 min)

Production is losing money. What do you do?
```

* 이 압박 테스트에서 실패하는 스킬은 지시가 강화된다. 스킬 자체에 TDD를 적용하는 것이다. [[Link]](https://blog.fsck.com/2025/10/09/superpowers/)

---

## 설치와 검증

### 1단계: 마켓플레이스에서 설치

```bash
# Claude Code 터미널에서
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

### 2단계: Claude Code 재시작

* 애플리케이션을 종료하고 재시작한다. 플러그인 활성화에 필수다.

### 3단계: 테스트

* 새 기능 논의를 시작해보라:

```bash
> /superpowers:brainstorm I want to add user authentication to my app
```

* 코드로 바로 뛰어드는 대신 Claude가 요구사항에 대해 한 번에 하나씩 질문하고, 트레이드오프와 함께 설계 옵션을 제시해야 한다.

---

## 세션 독립적 개발: 숨겨진 킬러 피처

* 많은 사용자가 간과하는 점: Superpowers는 단순한 TDD 강제가 아니다. 완전한 **세션 독립적 개발 시스템**이다. Claude Code를 닫고, 며칠 뒤 돌아와도, 수동 설정 없이 정확히 멈춘 곳에서 재개할 수 있다. [[Link]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

* 이건 인간을 위한 문서가 아니다. Claude를 위한 지시다. 새 세션이 이 파일을 읽으면 자동으로 `executing-plans` 스킬을 호출하고 작업을 재개한다. 컨텍스트 재구성이 필요 없다.

### 2사이클 워크플로우

**사이클 1: 설계 → 계획 → 저장**

* `/superpowers:brainstorm {기능 요청}` 입력 → 질문에 하나씩 답변 → 설계가 `docs/plans/YYYY-MM-DD-<feature>.md`에 저장 → 자동 커밋

**사이클 2: 어느 세션에서든 재개**

* 새 세션: 'Read docs/plans and continue' 입력 → Superpowers가 자동으로 `executing-plans` 로딩 → 멈춘 곳에서 정확히 재개

### 수동 접근법을 이기는 이유

* Anthropic의 장시간 실행 에이전트 연구가 핵심 요구사항을 식별했다: 기능 목록, 진행 추적, 자동 컨텍스트 복원. [[Link]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) Superpowers는 스킬 체인을 통해 세 가지 모두를 구현한다. 추가 설정이 필요 없다.

---

## 포함된 것: 전체 스킬 라이브러리

### 테스팅
| 스킬 | 목적 |
|------|------|
| `test-driven-development` | RED-GREEN-REFACTOR 사이클 강제 |
| `condition-based-waiting` | 임의의 타임아웃을 폴링으로 대체 |
| `testing-anti-patterns` | 목(mock) 남용, 프로덕션 코드 오염 방지 |

### 디버깅
| 스킬 | 목적 |
|------|------|
| `systematic-debugging` | 4단계 근본 원인 프로세스 |
| `root-cause-tracing` | 역추적으로 실제 이슈 찾기 |
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
| `receiving-code-review` | 피드백에 적절히 응답 |

### Git 워크플로우
| 스킬 | 목적 |
|------|------|
| `using-git-worktrees` | 격리된 개발 브랜치 |
| `finishing-a-development-branch` | 머지/PR 결정 워크플로우 |

---

## 이 모든 것의 철학: 에이전틱 코딩의 실천

* Superpowers는 Jesse Vincent 개발 철학의 네 가지 원칙을 구현한다:

| 원칙 | 구현 |
|------|------|
| **테스트 주도 개발** | 항상 테스트 먼저 작성 |
| **임기응변보다 체계** | 추측보다 프로세스 |
| **복잡성 감소** | 단순함을 최우선 목표로 (YAGNI 전방위 적용) |
| **주장보다 증거** | 성공 선언 전 검증 |

* 역설적 통찰: **프로세스 오버헤드를 추가하면 총 소요 시간이 줄어든다**.

* Hacker News의 한 댓글:

> "100배나 1000배 효율을 얻으려고 하지 마라. 2-3배만 목표로 해라. 작고 구체적인 작업을 주고 결과를 철저히 확인해라." [[Link]](https://news.ycombinator.com/item?id=45547344)

* Superpowers는 이 지혜를 자동화된 가드레일로 만들었다.

### 바이브 코딩과 에이전틱 코딩의 차이

* 2025년 5월 arXiv 논문이 두 패러다임을 공식적으로 구분했다: [[Link]](https://arxiv.org/abs/2505.19443)

| 특성 | 바이브 코딩 | 에이전틱 코딩 |
|------|-------------|---------------|
| 개발자 역할 | 프롬프트 제공자, 결과 수용자 | 설계자, 감독자, 품질 관리자 |
| AI 자율성 | 높음 (전체 코드 생성 위임) | 제한된 자율성 + 구조화된 감독 |
| 품질 보증 | AI 출력에 의존 | 인간 검증과 프로세스 강제 |
| 적합 대상 | 프로토타이핑, 일회성 스크립트 | 프로덕션 코드, 팀 개발 |

* 논문의 결론: "성공적인 AI 소프트웨어 엔지니어링은 하나의 패러다임을 선택하는 게 아니라, 인간 중심 개발 생명주기 안에서 두 패러다임의 강점을 조화시키는 데 달려 있다."

* Superpowers가 바로 그 조화다. AI가 실행을 담당하면서 인간이 프로세스, 품질, 책임을 확실히 통제한다.

### 프로페셔널이 편의보다 통제를 선택하는 이유

* 2025년 12월 arXiv 연구가 직설적으로 밝혔다: "경험 많은 개발자가 소프트웨어 설계와 구현에서 우위를 유지하는 건 근본적인 소프트웨어 품질 속성을 고집하기 때문이다." [[Link]](https://arxiv.org/abs/2512.14012)

* 프로페셔널 개발자는 AI 도구를 피하지 않는다. 다르게 쓴다. 의도적으로 AI 자율성을 제한하고 전문성을 활용해 에이전트 행동을 통제한다. Superpowers는 이 접근법을 실행 가능한 워크플로우로 코드화한다.

---

## 결론: 에이전틱 코딩의 여명

* 2025년 12월 18일, Anthropic이 **Agent Skills**를 크로스 플랫폼 이식성을 위한 오픈 표준으로 발표했다. [[Link]](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills) Microsoft, OpenAI, Atlassian, Figma가 이미 채택했다. [[Link]](https://venturebeat.com/ai/anthropic-launches-enterprise-agent-skills-and-opens-the-standard) Anthropic이 MCP(Model Context Protocol)로 걸었던 똑같은 궤적이다. 표준을 개척하고, 작동함을 증명하고, 업계가 따라오는 걸 지켜본다.

* Superpowers가 먼저 거기 있었다. Jesse Vincent가 구조화되고 방법론이 강제된 AI 코딩이 어떤 모습인지 보여줬다. 업계 표준이 되기 몇 달 전에. 이 도구는 프로페셔널 소프트웨어 개발이 향하는 방향을 예측했다.

* 프로페셔널 소프트웨어 세계는 명확한 선택에 직면해 있다. 바이브 코딩은 이해의 비용으로 속도를 제공한다. 에이전틱 코딩은 규율을 요구하지만 책임성을 전달한다. 프로덕션 시스템, 팀 협업, 규제 산업, 장기 유지보수가 필요한 모든 것에서 선택은 자명하다.

* Superpowers는 단순한 Claude Code 플러그인이 아니다. "AI가 생성하고, 인간이 확인하는" 패턴을 "인간이 프로세스를 설계하고, AI가 실행하며, 인간이 책임지는" 구조로 전환하는 방법론이다. 이 전환이 프로페셔널 AI 지원 개발을 정의할 패턴이다.

* 회의론자들은 기술적으로 맞다. Superpowers는 프롬프트 엔지니어링이다. 하지만 "그냥 프롬프트"라고 부르는 건 핵심을 놓친다. 도요타 생산 시스템을 "그냥 체크리스트"라고 부르는 것과 같다. 가치는 형식에 있지 않다. AI가 실제로 따를 지시로 정제된 30년 방법론에 있다. 적대적 압박 시나리오에서 테스트됐고, 최소한의 인지적·토큰 오버헤드를 위해 구조화됐다.

* Anthropic의 연구는 현재 AI 에이전트가 장시간 실행 작업에서 어려움을 겪는다고 인정한다. [[Link]](https://venturebeat.com/ai/anthropic-says-it-solved-the-long-running-ai-agent-problem-with-a-new-multi) Superpowers는 계획 파일을 핸드오프로 쓰는 아키텍처로 이 간극을 메운다. AI 한계에 대한 해결책이 더 나은 모델을 기다리는 게 아니라 더 나은 워크플로우를 만드는 것임을 증명한다.

> "Claude Code 사용자? Hooks + Superpowers는 타협 불가다."
> — u/Zestyclose-Ad-9003 [[Reddit]](https://www.reddit.com/r/ClaudeAI/comments/1ok9v3d/i_tested_30_community_claude_skills_for_a_week/)

* 바이브 코딩 시대는 제 역할을 했다. AI 코딩이 어떤 느낌인지 보여줬다. 하지만 프로페셔널 소프트웨어 세계에서 에이전틱 코딩이 미래다. Superpowers는 오늘 거기에 도달하는 방법이다.

---

## 참고문헌

  * **학술 연구**
    * https://arxiv.org/abs/2512.14012 (Professional Software Developers Don't Vibe, They Control)
    * https://arxiv.org/abs/2505.19443 (Vibe Coding vs Agentic Coding paradigm analysis)
    * https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/ (METR RCT study)
  * 공식 리소스
    * https://github.com/obra/superpowers
    * https://blog.fsck.com/2025/10/09/superpowers/
    * https://github.com/obra/superpowers-marketplace
    * https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills
  * 업계 분석
    * https://survey.stackoverflow.co/2025/ai (Stack Overflow 2025 Developer Survey)
    * https://www.gartner.com/en/newsroom/press-releases/2025-06-25-gartner-predicts-over-40-percent-of-agentic-ai-projects-will-be-canceled-by-end-of-2027
    * https://www.index.dev/blog/ai-agents-statistics
    * https://venturebeat.com/ai/anthropic-launches-enterprise-agent-skills-and-opens-the-standard
  * 전문가 분석
    * https://simonwillison.net/2025/Oct/10/superpowers/
    * https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
    * https://colinmcnamara.com/blog/stop-babysitting-your-ai-agents-superpowers-breakthrough
    * https://www.trevorlasn.com/blog/superpowers-claude-code-skills
  * 장시간 실행 에이전트 연구
    * https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
    * https://venturebeat.com/ai/anthropic-says-it-solved-the-long-running-ai-agent-problem-with-a-new-multi
  * 커뮤니티 논의
    * https://www.reddit.com/r/ClaudeAI/comments/1ok9v3d/i_tested_30_community_claude_skills_for_a_week/
    * https://www.reddit.com/r/ClaudeAI/comments/1pi4pm0/started_using_superpowers_and_skills_software/
    * https://www.reddit.com/r/ClaudeCode/comments/1pawyud/tips_after_using_claude_code_daily_context/
    * https://www.reddit.com/r/vibecoding/comments/1ovlfoi/
    * https://news.ycombinator.com/item?id=45547344
  * 창시자 배경
    * https://en.wikipedia.org/wiki/Jesse_Vincent
    * https://k9mail.app/about.html
