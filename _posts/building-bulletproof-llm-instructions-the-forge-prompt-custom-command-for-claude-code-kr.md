# LLM이 무시할 수 없는 지시문 만들기: Claude Code용 /forge-prompt 커스텀 커맨드 완벽 가이드

## 들어가며

* Claude가 내 지시를 스무 번째 무시했을 때 깨달았다. 문제는 Claude가 아니라 나였다. 인간의 뇌에는 완벽하게 명확해 보이는 지시문이 AI에게는 해석, 합리화, 그리고 지름길을 위한 여지를 너무 많이 남겼던 것이다.

* Claude Code는 Anthropic이 공식 출시한 CLI 도구다. 개발자가 터미널에서 직접 AI 코딩 어시스턴트와 상호작용할 수 있게 해준다. [[Link]](https://docs.anthropic.com/en/docs/claude-code)

* 이 기사는 Claude Code 기초—설치, 대화 흐름, 커스텀 커맨드 개념—에 이미 익숙하다는 전제로 작성했다. `.claude/` 디렉토리를 다루는 데 익숙하고 스킬이나 슬래시 커맨드를 직접 실험해본 적 있다면 제대로 찾아온 것이다.

* Claude Code의 가장 강력하면서도 과소평가된 기능 중 하나가 **커스텀 슬래시 커맨드 시스템**이다. `.claude/commands/` 디렉토리에 재사용 가능한 프롬프트를 저장할 수 있다. [[Link]](https://alexop.dev/posts/claude-code-slash-commands-guide/)

* 필자는 `/forge-prompt` 커스텀 커맨드를 "지시문 대장간"으로 설계했다. Claude Opus 4.5(그리고 미래 모델들)가 예외적인 정밀도로 따를 수 있는 방탄 지시문과 스킬을 생성하기 위해서다.

* 이 커맨드는 Claude 생태계에서 가장 정교한 두 스킬 시스템을 철저히 벤치마킹해서 만들었다. 하나는 Anthropic의 공식 **frontend-design** 플러그인이고, 다른 하나는 커뮤니티 주도의 **Superpowers** 플러그인이다. Superpowers는 **Jesse Vincent**(일명 **obra**)가 개발했다. 그는 **Request Tracker**를 만들고, **Perl** 프로젝트를 이끌었으며, **Keyboardio**를 공동 창립한 전설적인 개발자다. [[Link 1]](https://claude-plugins.dev/skills/@anthropics/claude-code/frontend-design) [[Link 2]](https://github.com/obra/superpowers)

* 목표는 단순했다. LLM에게 즉석에서 지시문을 생성하라고 요청하는 대신, 인간과 LLM 모두 지시문을 어떻게 처리하는지 깊이 이해하는 세계적 수준 개발자들의 지혜를 담은 체계적인 방법론을 원했다.

## /forge-prompt를 만든 이유

* 수년간 LLM과 작업하면서 반복되는 패턴을 발견했다. **인간에게 명확하게 들리는 지시문이 AI 에이전트가 실행할 때는 종종 실패한다.**

* 문제는 LLM이 지시를 따를 수 없다는 게 아니다. 대부분의 지시문이 해석, 합리화, 지름길을 위한 여지를 너무 많이 남긴다는 것이다.

* 필자는 Anthropic의 공식 `frontend-design` 스킬과 Jesse Vincent의 Superpowers 플러그인을 광범위하게 연구했다. 무엇이 그들의 지시문을 그토록 효과적으로 만드는지 분석했다.

* 답은 명확했다. **강한 언어, 명시적인 합리화 방지 메커니즘, 그리고 모호함의 여지를 남기지 않는 구조화된 구성요소.**

* `/forge-prompt`는 이 패턴들을 누구나 프로덕션급 지시문을 만들 수 있는 재사용 가능한 프레임워크로 코드화한다.

## 문제: LLM과 합리화의 함정

* Claude 같은 현대 LLM은 놀라운 능력을 갖추고 있지만 공통된 실패 모드를 공유한다. **합리화**다.

* 모호한 지시문을 받으면 AI 에이전트는 지름길을 정당화하고, 불필요하다고 판단한 단계를 건너뛰고, 압박을 받으면 규칙을 느슨하게 해석할 창의적인 방법을 찾아낸다.

* Reddit 커뮤니티는 이 현상을 광범위하게 문서화했다. 사용자들은 잘 작성된 CLAUDE.md 파일조차 Claude가 특정 작업에 "과잉"이라고 결정하면 무시당한다고 보고했다. [[Link]](https://news.ycombinator.com/item?id=46098838)

* Hacker News의 한 댓글이 이를 잘 보여준다. "내 친구는 Claude에게 항상 자신을 'Mr Tinkleberry'라고 부르라고 지시한다. 그는 Claude가 CLAUDE.md의 지시에 주의를 기울이지 않을 때를 알 수 있다고 한다."

* Superpowers 철학은 이를 정면으로 다룬다. **"구조가 필요 없다고 생각할 때가 가장 필요할 때다."** [[Link]](https://github.com/obra/superpowers)

## Claude Code의 지시문 아키텍처 이해하기

* `/forge-prompt`로 들어가기 전에 Claude Code의 지시문 시스템 계층 구조를 이해하는 것이 필수다.

* 커뮤니티는 이 구성요소들의 차이점을 활발히 논의해왔다. 다음 비교표로 요약된다. [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1ped515/understanding_claudemd_vs_skills_vs_slash/)

| 기능 | 호출 방식 | 핵심 목적 | 최적 용도 |
|---------|------------|--------------|----------|
| **CLAUDE.md** | 자동 (항상 로드) | 모든 대화의 기본 프롬프트 | 프로젝트별 컨벤션 |
| **Skills** | 에이전트 호출 (자동) | 온디맨드 지식, 점진적 공개 (필요할 때만 로드) | API 문서, 스타일 가이드, 복잡한 패턴 |
| **Slash Commands** | 사용자 또는 에이전트 | 단발성 작업용 재사용 프롬프트 | PR 표준화, 테스트 실행 |
| **Plugins** | 패키지 형식 | 스킬, 커맨드, 에이전트, 훅 번들링 | 배포 및 설치 |

* 핵심 통찰은 **스킬과 슬래시 커맨드가 다른 의도를 위해 존재한다**는 점이다. 스킬은 주로 관련 있을 때 Claude가 자동으로 호출하도록 설계됐고, 슬래시 커맨드는 사용자가 특정 순간에 호출하도록 설계됐다. 물론 양쪽 모두 어느 쪽에서든 트리거할 수 있다.

## Superpowers 철학: 실전 검증된 프로토콜

* Superpowers 플러그인은 규율 있는 동작을 강제하는 조합 가능한 "스킬"로 구축된 완전한 소프트웨어 개발 워크플로우다.

* 핵심 철학은 네 가지 기둥에 기반한다.

* **합리화 방지** - 가장 흔한 실패 모드는 "이 경우는 다르다"
* **규율 강제** - 구조가 결정 피로와 지름길을 제거한다
* **실패를 가시화** - 명확한 기준이 궤도 이탈을 드러낸다
* **실행 가능하게** - 모든 규칙에는 구체적인 행동이 있다. 추상적인 조언이 아니다

* Superpowers는 **테스트 주도 개발(TDD)**을 프로세스 문서 자체에 적용한다.

* 테스트 케이스(서브에이전트를 사용한 압박 시나리오—실패를 유발하도록 설계된 엣지 케이스)를 작성하고, 실패를 관찰하고(베이스라인 동작), 스킬(문서)을 작성하고, 테스트 통과를 관찰하고(에이전트 준수), 리팩토링한다(허점 차단). [[Link]](https://github.com/obra/superpowers)

## Anthropic의 Frontend-Design 스킬: 공식 벤치마크

* Anthropic의 공식 `frontend-design` 스킬은 Claude가 실제로 따르는 지시문 작성법을 보여준다.

* 이 스킬은 강하고 모호하지 않은 언어 패턴을 사용한다.

```markdown
**CRITICAL**: Choose a clear conceptual direction and execute it with precision.

NEVER use generic AI-generated aesthetics like overused font families
(Inter, Roboto, Arial, system fonts)...

**IMPORTANT**: Match implementation complexity to the aesthetic vision.
```

* CRITICAL, NEVER, IMPORTANT 같은 강조 단어에 **전체 대문자**를 의도적으로 사용한다.

* 이 스킬은 Claude에게 하지 말아야 할 것만이 아니라 **무엇을 해야 하는지**도 알려준다. Anthropic의 프롬프트 엔지니어링 가이드에서 나온 핵심 모범 사례다. [[Link]](https://claude.com/blog/best-practices-for-prompt-engineering)

## /forge-prompt 커맨드: 지시문 대장간의 해부학

* 필자는 `/forge-prompt`를 Superpowers와 Anthropic 공식 스킬의 교훈을 방탄 지시문 생성을 위한 **9개 구성요소 프레임워크**로 통합해 설계했다.

* Superpowers와 Anthropic 공식 플러그인의 수십 개 효과적인 스킬을 분석한 후, 가장 신뢰할 수 있는 지시문들이 공유하는 9개의 반복적인 구조적 요소를 식별했다.

### Iron Law

* 모든 forge-prompt 출력은 협상 불가능한 핵심 규칙으로 시작한다.

```
NO INSTRUCTION WITHOUT ALL 9 COMPONENTS.
"A skill without Iron Law is a suggestion. A skill without Red Flags is a trap."
```

* 이 Iron Law 패턴은 Superpowers에서 직접 가져왔다. 각 스킬에는 위반 시 실패가 보장되는 하나의 규칙이 있다.

### 9개 필수 구성요소

* `/forge-prompt`는 모호함의 여지를 남기지 않는 완전한 구조를 강제한다.

**1. YAML 프론트매터 (메타데이터)**

```yaml
---
name: kebab-case-name
description: Use when [TRIGGER CONDITION] - [WHAT IT DOES] that [WHY IT MATTERS]
---
```

* description 필드는 필자가 **Claude Search Optimization(CSO)**이라 부르는 것에 핵심적이다. Claude가 관련 있을 때 스킬을 발견하고 로드하도록 돕는 설명 작성 관행이다.

**2. Iron Law (협상 불가능한 핵심 규칙)**

* 위반할 수 없는 **단 하나의** 규칙. 예시:
  - `NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST`
  - `NO CODE WITHOUT FAILING TEST FIRST`
  - `NO COMMIT WITHOUT VERIFICATION COMMAND OUTPUT`

**3. When to Use / When NOT to Use**

* 이 섹션은 반드시 직관에 반하는 트리거를 포함해야 한다. 개발자가 프로세스를 건너뛰고 싶은 유혹이 가장 강한 상황들이다.

**4. 프로세스/단계 구조**

* 명확하고 순차적인 단계들과 **게이트**(다음으로 진행하기 전 통과해야 하는 체크포인트).

**5. Red Flags 섹션**

* 실패 직전임을 알리는 정신적 패턴들:

```markdown
If you catch yourself thinking:
- "Quick fix for now, investigate later"
- "This case is different/simple"
- "I already know what the problem is"
- "Just try this and see"

**ALL of these mean: STOP. [Specific action to take].**
```

**6. Common Rationalizations 테이블**

* 모든 변명을 직접적인 반박으로 선제 대응:

| Excuse | Reality |
|--------|---------|
| "Simple issues don't need this" | Simple issues have root causes too. Process is fast for simple cases. |
| "Emergency, no time" | Emergency pressure is exactly when systematic approach saves time. |
| "I'll test if problems emerge" | Problems = agents can't use skill. Test BEFORE deploying. |

**7. Quick Reference 테이블**

* 실행 중 스캔을 위한 한눈에 보는 요약.

**8. Key Principles / Summary**

* 빠른 상기를 위한 핵심 원칙.

**9. Integration / Related Skills**

* 함께 작동하는 다른 스킬에 대한 상호 참조.

## LLM이 실제로 따르는 언어 패턴

* `/forge-prompt`는 Anthropic의 연구가 효과적이라고 입증한 특정 언어 패턴을 강제한다.

| Weak (피하기) | Strong (사용하기) |
|--------------|--------------|
| "You should" | "You MUST" |
| "Consider" | "REQUIRED" |
| "It's recommended" | "This is not negotiable" |
| "Try to" | "ALWAYS" / "NEVER" |
| "It's helpful to" | "CRITICAL" |
| "You might want to" | "You cannot proceed until" |

* 이는 Anthropic의 공식 가이던스와 일치한다. **"모델에게 정확히 보고 싶은 것을 알려줘라. 포괄적인 출력을 원하면 직접 요청해라."** [[Link]](https://claude.com/blog/best-practices-for-prompt-engineering)

## 프롬프트 엔지니어링 모범 사례 통합

* `/forge-prompt` 커맨드는 2025년 프롬프트 엔지니어링 모범 사례에서 검증된 여러 기법을 통합한다.

### 명시적이고 명확하게

* 현대 AI 모델은 명확하고 명시적인 지시에 예외적으로 잘 반응한다.

* Anthropic의 가이드는 말한다. "모델이 원하는 것을 추론할 것이라고 가정하지 마라—직접 말해라." [[Link]](https://claude.com/blog/best-practices-for-prompt-engineering)

### 맥락과 동기 제공

* 무엇이 왜 중요한지 설명하면 AI 모델이 목표를 더 잘 이해한다.

* 단순히 "NEVER use bullet points"라고 말하는 대신, `/forge-prompt` 접근법은 이렇다. "Use flowing prose because bullet points fragment ideas that should connect logically, making it harder for readers to follow the reasoning chain."

### 예시 사용

* `/forge-prompt` 출력에는 항상 구체적인 예시가 포함된다. Anthropic이 지적했듯이, "예시는 말하지 않고 보여주며, 설명만으로는 표현하기 어려운 미묘한 요구사항을 명확히 한다."

### 불확실성 표현 권한 부여

* 잘 만들어진 지시문에는 Claude가 추측하기보다 정보가 부족할 때 인정해도 된다는 명시적인 허락이 포함된다.

## 안티 패턴 경고: 하지 말아야 할 것

* `/forge-prompt`는 다음과 같은 지시문 생성을 명시적으로 경고한다.

* 부드러운 언어 사용 ("consider", "try to", "you might want to")
* Iron Law 없음 (위반할 수 없는 단 하나의 규칙)
* Red Flags 섹션 생략 (합리화 예측 실패)
* 모호한 성공 기준 ("do a good job")
* 융통성 허용 ("unless you have a good reason")
* 선의 가정 ("you probably know when to skip this")
* 너무 추상적 (구체적 행동이나 예시 없음)
* 명확한 단계 없이 너무 김 (텍스트 벽)

## 실전 적용: 커밋 메시지 스킬 생성

* `/forge-prompt`로 커밋 메시지 스킬을 만드는 방법:

```bash
> /forge-prompt Create a skill for writing semantic commit messages following conventional commits spec"
```

* 출력물은 다음을 포함한다.

* **Iron Law**: `NO COMMIT WITHOUT TYPE PREFIX AND SCOPE`
* **Red Flags**: "If you catch yourself thinking 'this is just a small fix'..."
* **Rationalizations Table**: "Too tedious for small changes" 같은 변명을 반박으로 매핑
* **Quick Reference**: 커밋 타입 테이블 (feat, fix, docs, style, refactor, test, chore)

## 커뮤니티 피드백과 활성화율

* Claude Code 커뮤니티는 스킬 활성화 신뢰성을 광범위하게 테스트했고, 이 발견들이 `/forge-prompt`의 출력 구조화 방식에 직접 반영됐다.

* 한 체계적 연구에 따르면 단순한 지시 훅으로는 스킬이 약 20%만 활성화됐다. 하지만 **강제 평가 훅**을 구현하자—Claude가 진행 전 각 스킬을 YES/NO 추론으로 명시적으로 평가하게 만드는—**84% 활성화율**을 달성했다. [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1oywsa1/claude_code_skills_activate_20_of_the_time_heres/)

* 활성화를 개선하는 핵심 요소들:
  - 구체적인 트리거 조건이 있는 풍부한 description 필드
  - 기술 중립적인 문제 설명
  - 에러 메시지 키워드와 증상 언어
  - 능동태의 서술적 명명 ("creating-skills"이지 "skill-creation"이 아님)

* `/forge-prompt`가 상세한 트리거 조건이 있는 YAML 프론트매터를 첫 번째 필수 구성요소로 강제하는 이유가 바로 이것이다. 관료주의가 아니라 검증된 활성화 최적화다.

## AI 지원 개발에서 이것이 중요한 이유

* 위에서 논의한 패턴들은 이론적인 것만이 아니다. 일상 개발 워크플로우에 실질적인 영향을 미친다.

* Claude Code 팀의 **Boris**가 Hacker News에서 지적했다. "Claude가 반복적으로 틀리거나 이해하지 못하거나 많은 토큰을 소비하는 것이 있다면 CLAUDE.md에 넣어라. Claude는 이 파일을 자동으로 읽고, 같은 말을 반복하지 않는 좋은 방법이다." [[Link]](https://news.ycombinator.com/item?id=46256606)

* `/forge-prompt` 커맨드는 이 원칙을 더 발전시킨다. 다음을 수행하는 지시문 생성을 위한 **체계적인 방법론**을 제공한다.
  - 실패 모드가 발생하기 전에 예측
  - LLM이 이용할 수 있는 허점 차단
  - 준수율 개선이 검증된 언어 패턴 사용
  - 성공 확인을 위한 검증 메커니즘 포함

## /forge-prompt 시작하기

* `/forge-prompt`를 사용하려면 `~/.claude/commands/forge-prompt.md`(전역 접근용) 또는 `.claude/commands/forge-prompt.md`(프로젝트별) 파일을 생성한다.

* 아래 제공된 완전한 커맨드 템플릿을 복사해 저장한다.

* 어떤 지시문 주제로든 호출한다.

```bash
> /forge-prompt [Your instruction topic here]
```

* 커맨드가 Claude를 9개 필수 구성요소 전체 생성으로 안내해 중요한 요소가 누락되지 않도록 한다.

## 완전한 /forge-prompt 커맨드

* 아래 전체 내용을 복사해 `.claude/commands/` 디렉토리에 `forge-prompt.md`로 저장한다.

```markdown
$ nano .claude/commands/forge-prompt.md
---
description: Create bulletproof instructions/skills following the Superpowers philosophy - strong language, mandatory checklists, anti-rationalization tables, and iron laws
---

# Forge Skill - Instruction Smithy

You are creating a **bulletproof instruction/skill** following the Superpowers philosophy for:

**$ARGUMENTS**

---

## The Iron Law

NO INSTRUCTION WITHOUT ALL 9 COMPONENTS.
"A skill without Iron Law is a suggestion. A skill without Red Flags is a trap."

**Violating the letter of this structure is violating the spirit of effective instructions.**

---

## The Philosophy

Superpowers skills are NOT suggestions. They are **battle-tested protocols** designed to:

1. **Prevent rationalization** - The #1 failure mode is "this case is different"
2. **Force discipline** - Structure eliminates decision fatigue and shortcuts
3. **Make failure visible** - Clear criteria reveal when you're off track
4. **Be actionable** - Every rule has a concrete action, not abstract advice

**Core belief:** If you think you don't need the structure, you need it most.

---

## The 9 Required Components

Create TodoWrite todos for EACH component as you work through them.

### 1. YAML Frontmatter (Metadata)

---
name: kebab-case-name
description: Use when [TRIGGER CONDITION] - [WHAT IT DOES] that [WHY IT MATTERS]
---

**Trigger condition patterns:**
- "Use when encountering X, before doing Y"
- "Use when starting X that requires Y"
- "Use when finishing X, before claiming Y"

**Example:**

description: Use when encountering any bug, before proposing fixes - four-phase framework that ensures understanding before attempting solutions

### 2. Iron Law (Non-Negotiable Core Rule)

The ONE rule that, if broken, guarantees failure.

**Format:**

## The Iron Law

\`\`\`
[ALL CAPS, IMPERATIVE STATEMENT]
\`\`\`

[Supporting statement about why this matters]

**Violating the letter of this rule is violating the spirit of [skill name].**

**Examples:**
- `NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST`
- `NO REPORT WITHOUT 15+ SEARCHES AND PHASE ZERO FIRST`
- `NO CODE WITHOUT FAILING TEST FIRST`
- `NO COMMIT WITHOUT VERIFICATION COMMAND OUTPUT`

### 3. When to Use / When NOT to Use

**Format:**

## When to Use

Use for [CATEGORY]:
- Specific scenario 1
- Specific scenario 2
- Specific scenario 3

**Use this ESPECIALLY when:**
- Counter-intuitive trigger 1 (when you want to skip it most)
- Counter-intuitive trigger 2
- Counter-intuitive trigger 3

**Don't skip when:**
- Excuse that seems valid but isn't
- Another excuse
- Time pressure excuse

**Key insight:** The "ESPECIALLY when" section should list situations where people are MOST tempted to skip it.

### 4. Process/Phase Structure

Break the skill into clear, sequential phases with gates (checkpoints that must be passed before proceeding).

**Format:**

## The [Number] Phases

You MUST complete each phase before proceeding to the next.

### Phase 1: [Name]

**[GATE CONDITION]:**

1. **Step Name**
   - Substep detail
   - Substep detail
   - Success criteria

2. **Step Name**
   - Substep detail

**Gate patterns:**
- "BEFORE attempting ANY [action]:"
- "You cannot proceed to Phase N until:"
- "If [condition], STOP and return to Phase 1"

### 5. Red Flags Section

Mental patterns that signal you're about to fail.

**Format:**

## Red Flags - STOP and [Action]

If you catch yourself thinking:
- "[Rationalization thought 1]"
- "[Rationalization thought 2]"
- "[Shortcut thought 1]"
- "[Overconfidence thought 1]"
- "[Time pressure thought 1]"

**ALL of these mean: STOP. [Specific action to take].**

**Common red flag patterns:**
- "Quick fix for now, investigate later"
- "This case is different/simple"
- "I already know what the problem is"
- "Just try this and see"
- "I don't have time for the full process"

### 6. Common Rationalizations Table

Preempt every excuse with direct rebuttal.

**Format:**

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "[Excuse 1]" | [Direct rebuttal explaining why it's wrong] |
| "[Excuse 2]" | [Direct rebuttal explaining why it's wrong] |
| "[Excuse 3]" | [Direct rebuttal explaining why it's wrong] |

**Rebuttal tone:** Direct, no hedging, explains the consequence.

**Example rebuttals:**
- "Simple issues have root causes too. Process is fast for simple cases."
- "Emergency pressure is exactly when systematic approach saves time."
- "Partial understanding guarantees bugs. Read it completely."

### 7. Quick Reference Table

One-glance summary of the entire skill.

**Format:**

## Quick Reference

| Phase | Key Activities | Success Criteria |
|-------|---------------|------------------|
| **1. [Name]** | [2-3 activities] | [Measurable outcome] |
| **2. [Name]** | [2-3 activities] | [Measurable outcome] |

### 8. Key Principles / Summary

Core principles for quick recall.

**Format:**

## Key Principles

- **[Principle name]** - [One line explanation]
- **[Principle name]** - [One line explanation]
- **[Principle name]** - [One line explanation]

**Or alternative closing format:**

## Summary

**Starting [task type]:**
1. [First action]
2. [Second action]
3. [Third action]

**[Situation]?** [Action].

**[Key insight] = [mandatory action].**

### 9. Integration / Related Skills (Optional but Recommended)

**Format:**

## Integration with Other Skills

**This skill requires using:**
- **[skill-name]** - REQUIRED when [condition]
- **[skill-name]** - REQUIRED for [purpose]

**Complementary skills:**
- **[skill-name]** - [When to use together]

---

## Language & Tone Guide

### Strong Language Patterns

Use these deliberately and consistently:

| Weak (Avoid) | Strong (Use) |
|--------------|--------------|
| "You should" | "You MUST" |
| "Consider" | "REQUIRED" |
| "It's recommended" | "This is not negotiable" |
| "Try to" | "ALWAYS" / "NEVER" |
| "It's helpful to" | "CRITICAL" |
| "You might want to" | "You cannot proceed until" |
| "It's important" | "If you skip this, you will fail" |

### Emphasis Patterns

- **ALL CAPS** for critical terms: MUST, NEVER, ALWAYS, REQUIRED, CRITICAL, STOP
- **Code blocks** for Iron Laws and key rules
- **Bold** for section headers and key terms
- **Tables** for comparisons and quick reference
- **Bullet points** for lists, **numbered lists** for sequences

### Philosophical Phrases to Include

- "Violating the letter of this rule is violating the spirit of [X]"
- "If you think [X], you are rationalizing"
- "The moment you feel [X] is the most dangerous moment"
- "ALL of these mean: STOP."
- "[Excuse] is ALWAYS wrong"
- "This is not negotiable. This is not optional."

---

## Anti-Pattern Warnings

**DO NOT create instructions that:**

- ❌ Use soft language ("consider", "try to", "you might want to")
- ❌ Lack an Iron Law (the ONE rule that cannot be broken)
- ❌ Skip the Red Flags section (failing to anticipate rationalization)
- ❌ Have vague success criteria ("do a good job")
- ❌ Allow wiggle room ("unless you have a good reason")
- ❌ Assume good faith ("you probably know when to skip this")
- ❌ Are too abstract (no concrete actions or examples)
- ❌ Are too long without clear phases (wall of text)

**DO create instructions that:**

- ✅ Have ONE non-negotiable Iron Law
- ✅ Anticipate every excuse with direct rebuttals
- ✅ Include measurable success criteria
- ✅ Gate each phase with clear conditions
- ✅ Use strong, unambiguous language
- ✅ Provide concrete examples and patterns
- ✅ Are scannable (tables, bullets, clear headers)

---

## Final Verification Checklist

Before considering the instruction complete, verify:

### Structure Checklist
- [ ] YAML frontmatter with name and description (with trigger condition)
- [ ] Iron Law in code block with supporting statement
- [ ] When to Use section with "ESPECIALLY when" counter-intuitive triggers
- [ ] Clear phases with gate conditions
- [ ] Red Flags section with "If you catch yourself thinking" pattern
- [ ] Common Rationalizations table with Excuse | Reality format
- [ ] Quick Reference table for one-glance summary
- [ ] Key Principles or Summary section

### Language Checklist
- [ ] Uses MUST, NEVER, ALWAYS, REQUIRED appropriately
- [ ] No soft language (should, consider, try to, might)
- [ ] Includes at least 3 "Violating the letter" type phrases
- [ ] Red flags end with "ALL of these mean: STOP"
- [ ] Each rationalization has a direct, no-hedge rebuttal

### Content Checklist
- [ ] Iron Law is ONE clear rule (not multiple)
- [ ] Red Flags include time-pressure and overconfidence thoughts
- [ ] Rationalizations table has at least 5 entries
- [ ] Success criteria are measurable, not vague
- [ ] Examples are concrete and actionable

---

## Output Location

Save the generated instruction to:
- **For skills:** `.claude/plugins/[plugin-name]/skills/[skill-name]/SKILL.md`
- **For commands:** `.claude/commands/[command-name].md`
- **For standalone:** `docs/instructions/[name].md` or user-specified path

---

## Execution

Now create a bulletproof instruction for **$ARGUMENTS** following ALL components above.

Use TodoWrite to track each of the 9 components as you complete them.

Remember: **If you skip any component, the instruction will fail in production.**
```

## 결론

* `/forge-prompt` 커스텀 커맨드는 Anthropic의 공식 플러그인과 실전 검증된 Superpowers 프레임워크에서 얻은 교훈의 종합이다.

* Claude가 무시하거나, 합리화하거나, 너무 느슨하게 해석하는 지시문 작성에 지쳤기에 이 도구를 만들었다.

* 이 도구는 LLM 지시문 설계의 근본적인 과제를 해결한다. **지름길을 택하고 싶은 유혹이 있을 때조차 AI가 실제로 따를 지시문을 어떻게 작성하는가?**

* 답은 강한 언어, 명시적인 합리화 방지 테이블, 필수 체크리스트, 그리고 해석의 여지를 남기지 않는 Iron Law에 있다.

* Claude Code로 생산성을 극대화하려는 개발자에게 `/forge-prompt` 같은 도구를 통한 지시문 설계 마스터는 더 이상 선택이 아니다. 필수다.

* 위의 완전한 템플릿을 복사해 `.claude/commands/` 디렉토리에 저장하고, 오늘부터 방탄 지시문을 만들기 시작하라.

## References
* [https://docs.anthropic.com/en/docs/claude-code](https://docs.anthropic.com/en/docs/claude-code)
* [https://claude.com/blog/skills-explained](https://claude.com/blog/skills-explained)
* [https://github.com/obra/superpowers](https://github.com/obra/superpowers)
* [https://github.com/anthropics/claude-code/tree/main/plugins/frontend-design](https://github.com/anthropics/claude-code/tree/main/plugins/frontend-design)
* [https://claude.com/blog/best-practices-for-prompt-engineering](https://claude.com/blog/best-practices-for-prompt-engineering)
* [https://alexop.dev/posts/claude-code-slash-commands-guide/](https://alexop.dev/posts/claude-code-slash-commands-guide/)
* [https://www.reddit.com/r/ClaudeAI/comments/1ped515/understanding_claudemd_vs_skills_vs_slash/](https://www.reddit.com/r/ClaudeAI/comments/1ped515/understanding_claudemd_vs_skills_vs_slash/)
* [https://www.reddit.com/r/ClaudeCode/comments/1oywsa1/claude_code_skills_activate_20_of_the_time_heres/](https://www.reddit.com/r/ClaudeCode/comments/1oywsa1/claude_code_skills_activate_20_of_the_time_heres/)
* [https://news.ycombinator.com/item?id=46256606](https://news.ycombinator.com/item?id=46256606)
* [https://news.ycombinator.com/item?id=46098838](https://news.ycombinator.com/item?id=46098838)
