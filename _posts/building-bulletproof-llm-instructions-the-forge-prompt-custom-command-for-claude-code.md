Building Bulletproof LLM Instructions: The /forge-prompt Custom Command for Claude Code

## Introduction

* After writing my twentieth instruction that **Claude** ignored, I realized the problem wasn't **Claude**—it was me. The instructions that sounded perfectly clear to my human brain left too much room for **AI** interpretation, rationalization, and shortcuts.

* **Claude Code** is **Anthropic**'s official **CLI** tool that enables developers to interact with **AI** coding assistants directly from the terminal. [[Link]](https://docs.anthropic.com/en/docs/claude-code)

* This article assumes you're already familiar with **Claude Code** basics—installation, conversation flow, and the general concept of custom commands. If you're comfortable navigating `.claude/` directories and have experimented with skills or slash commands, you're in the right place.

* One of its most powerful yet underutilized features is the **custom slash command system**, which allows developers to create reusable prompts stored in the `.claude/commands/` directory. [[Link]](https://alexop.dev/posts/claude-code-slash-commands-guide/)

* I created the `/forge-prompt` custom command as an "instruction smithy" designed to generate bulletproof instructions and skills that **Claude Opus 4.5** (and future models) can follow with exceptional precision.

* This command was built by thoroughly benchmarking two of the most sophisticated skill systems in the **Claude** ecosystem: **Anthropic**'s official **frontend-design** plugin and the community-driven **Superpowers** plugin developed by **Jesse Vincent** (aka **obra**)—a legendary developer known for creating **Request Tracker**, leading the **Perl** project, and co-founding **Keyboardio**. [[Link 1]](https://claude-plugins.dev/skills/@anthropics/claude-code/frontend-design) [[Link 2]](https://github.com/obra/superpowers)

* My goal was simple: instead of asking **LLM**s to generate instructions on the fly, I wanted a systematic methodology that captures the wisdom of world-class developers who deeply understand how both humans and **LLM**s process instructions.

## Why I Built /forge-prompt

* After years of working with **LLM**s, I noticed a recurring pattern: **instructions that sound clear to humans often fail when executed by AI agents.**

* The problem isn't that **LLM**s can't follow instructions—it's that most instructions leave too much room for interpretation, rationalization, and shortcuts.

* I studied **Anthropic**'s official `frontend-design` skill and **Jesse Vincent**'s **Superpowers** plugin extensively, analyzing what made their instructions so effective.

* The answer was clear: **strong language, explicit anti-rationalization mechanisms, and structured components that leave no room for ambiguity.**

* `/forge-prompt` codifies these patterns into a reusable framework that anyone can use to create production-grade instructions.

## The Problem: **LLM**s and the Rationalization Trap

* Modern **LLM**s like **Claude** are incredibly capable, but they share a common failure mode: **rationalization**.

* When given vague instructions, **AI** agents will find creative ways to justify shortcuts, skip steps they deem unnecessary, or interpret rules loosely when under pressure.

* The **Reddit** community has extensively documented this phenomenon, with users reporting that even well-written **CLAUDE.md** files get ignored when **Claude** decides the instructions are "overkill" for a particular task. [[Link]](https://news.ycombinator.com/item?id=46098838)

* As one **Hacker News** commenter noted: "A friend of mine tells **Claude** to always address him as 'Mr Tinkleberry', he says he can tell **Claude** is not paying attention to the instructions on **CLAUDE.md**."

* The **Superpowers** philosophy directly addresses this: **"If you think you don't need the structure, you need it most."** [[Link]](https://github.com/obra/superpowers)

## Understanding **Claude Code**'s Instruction Architecture

* Before diving into `/forge-prompt`, it's essential to understand the hierarchy of instruction systems in **Claude Code**.

* The community has been actively discussing the differences between these components, as summarized in this comparison: [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1ped515/understanding_claudemd_vs_skills_vs_slash/)

| Feature | Invocation | Core Purpose | Best For |

|---------|------------|--------------|----------|

| **CLAUDE.md** | Automatic (always loaded) | Default prompt for every conversation | Project-specific conventions |

| **Skills** | Agent-invoked (automatic) | On-demand knowledge, progressively disclosed (loaded only when needed) | **API** docs, style guides, complex patterns |

| **Slash Commands** | User or Agent | Reusable prompts for single-shot tasks | Standardizing PRs, running tests |

| **Plugins** | Package format | Bundle skills, commands, agents, hooks | Distribution and installation |

* The key insight is that **Skills and Slash Commands serve different intentions**: skills are primarily designed for **Claude** to invoke automatically when relevant, while slash commands are designed for users to invoke at specific moments—though both can be triggered by either party.

## The **Superpowers** Philosophy: Battle-Tested Protocols

* The **Superpowers** plugin represents a complete software development workflow built on composable "skills" that enforce disciplined behavior.

* Its core philosophy rests on four pillars:

* **Prevent rationalization** - The #1 failure mode is "this case is different"

* **Force discipline** - Structure eliminates decision fatigue and shortcuts

* **Make failure visible** - Clear criteria reveal when you're off track

* **Be actionable** - Every rule has a concrete action, not abstract advice

* **Superpowers** applies **Test-Driven Development** to process documentation itself.

* You write test cases (pressure scenarios—edge cases designed to trigger failures—with subagents), watch them fail (baseline behavior), write the skill (documentation), watch tests pass (agents comply), and refactor (close loopholes). [[Link]](https://github.com/obra/superpowers)

## **Anthropic**'s Frontend-Design Skill: The Official Benchmark

* **Anthropic**'s official `frontend-design` skill demonstrates how to write instructions that **Claude** actually follows.

* The skill uses strong, unambiguous language patterns:

```markdown

**CRITICAL**: Choose a clear conceptual direction and execute it with precision.

NEVER use generic AI-generated aesthetics like overused font families

(Inter, Roboto, Arial, system fonts)...

**IMPORTANT**: Match implementation complexity to the aesthetic vision.

```

* Notice the deliberate use of **ALL CAPS** for emphasis words like CRITICAL, NEVER, and IMPORTANT.

* The skill also tells **Claude** what TO do instead of just what NOT to do—a key best practice from **Anthropic**'s own prompt engineering guide. [[Link]](https://claude.com/blog/best-practices-for-prompt-engineering)

## The /forge-prompt Command: Anatomy of an Instruction Smithy

* I designed `/forge-prompt` to synthesize lessons from both **Superpowers** and **Anthropic**'s official skills into a **9-component framework** for creating bulletproof instructions.

* After analyzing dozens of effective skills from both **Superpowers** and **Anthropic**'s official plugins, I identified 9 recurring structural elements that the most reliable instructions share.

### The Iron Law

* Every forge-prompt output begins with a non-negotiable core rule:

```

NO INSTRUCTION WITHOUT ALL 9 COMPONENTS.

"A skill without Iron Law is a suggestion. A skill without Red Flags is a trap."

```

* This Iron Law pattern comes directly from **Superpowers**, where each skill has ONE rule that, if broken, guarantees failure.

### The 9 Required Components

* `/forge-prompt` enforces a complete structure that leaves no room for ambiguity:

**1. **YAML** Frontmatter (Metadata)**

```yaml

---

name: kebab-case-name

description: Use when [TRIGGER CONDITION] - [WHAT IT DOES] that [WHY IT MATTERS]

---

```

* The description field is critical for what I call **Claude Search Optimization** (**CSO**)—the practice of writing descriptions that help **Claude** discover and load your skill when relevant.

**2. Iron Law (Non-Negotiable Core Rule)**

* The ONE rule that cannot be violated. Examples include:

 - `NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST`

 - `NO CODE WITHOUT FAILING TEST FIRST`

 - `NO COMMIT WITHOUT VERIFICATION COMMAND OUTPUT`

**3. When to Use / When NOT to Use**

* This section must include counter-intuitive triggers—situations where developers are MOST tempted to skip the process.

**4. Process/Phase Structure**

* Clear, sequential phases with **gates** (checkpoints that must be passed before proceeding).

**5. Red Flags Section**

* Mental patterns that signal you're about to fail:

```markdown

If you catch yourself thinking:

\- "Quick fix for now, investigate later"

\- "This case is different/simple"

\- "I already know what the problem is"

\- "Just try this and see"

**ALL of these mean: STOP. [Specific action to take].**

```

**6. Common Rationalizations Table**

* Preempt every excuse with a direct rebuttal:

| Excuse | Reality |

|--------|---------|

| "Simple issues don't need this" | Simple issues have root causes too. Process is fast for simple cases. |

| "Emergency, no time" | Emergency pressure is exactly when systematic approach saves time. |

| "I'll test if problems emerge" | Problems = agents can't use skill. Test BEFORE deploying. |

**7. Quick Reference Table**

* One-glance summary for scanning during execution.

**8. Key Principles / Summary**

* Core principles for quick recall.

**9. Integration / Related Skills**

* Cross-references to other skills that work together.

## Language Patterns That **LLM**s Actually Follow

* `/forge-prompt` enforces specific language patterns that **Anthropic**'s research has shown to be effective:

| Weak (Avoid) | Strong (Use) |

|--------------|--------------|

| "You should" | "You MUST" |

| "Consider" | "REQUIRED" |

| "It's recommended" | "This is not negotiable" |

| "Try to" | "ALWAYS" / "NEVER" |

| "It's helpful to" | "CRITICAL" |

| "You might want to" | "You cannot proceed until" |

* This aligns with **Anthropic**'s official guidance: **"Tell the model exactly what you want to see. If you want comprehensive output, ask for it."** [[Link]](https://claude.com/blog/best-practices-for-prompt-engineering)

## Prompt Engineering Best Practices Integration

* The `/forge-prompt` command incorporates several proven prompt engineering techniques from 2025 best practices:

### Be Explicit and Clear

* Modern **AI** models respond exceptionally well to clear, explicit instructions.

* **Anthropic**'s guide states: "Don't assume the model will infer what you want—state it directly." [[Link]](https://claude.com/blog/best-practices-for-prompt-engineering)

### Provide Context and Motivation

* Explaining WHY something matters helps **AI** models understand goals better.

* Rather than just saying "NEVER use bullet points," the `/forge-prompt` approach would be: "Use flowing prose because bullet points fragment ideas that should connect logically, making it harder for readers to follow the reasoning chain."

### Use Examples

* `/forge-prompt` outputs always include concrete examples because, as **Anthropic** notes, "examples show rather than tell, clarifying subtle requirements that are difficult to express through description alone."

### Give Permission to Express Uncertainty

* Well-crafted instructions include explicit permission for **Claude** to acknowledge when it doesn't have enough information rather than guessing.

## Anti-Pattern Warnings: What NOT to Do

* `/forge-prompt` explicitly warns against creating instructions that:

* Use soft language ("consider", "try to", "you might want to")

* Lack an Iron Law (the ONE rule that cannot be broken)

* Skip the Red Flags section (failing to anticipate rationalization)

* Have vague success criteria ("do a good job")

* Allow wiggle room ("unless you have a good reason")

* Assume good faith ("you probably know when to skip this")

* Are too abstract (no concrete actions or examples)

* Are too long without clear phases (wall of text)

## Real-World Application: Creating a Commit Message Skill

* Here's how you might use `/forge-prompt` to create a commit message skill:

```bash

> /forge-prompt Create a skill for writing semantic commit messages following conventional commits spec"

```

* The output would include:

* **Iron Law**: `NO COMMIT WITHOUT TYPE PREFIX AND SCOPE`

* **Red Flags**: "If you catch yourself thinking 'this is just a small fix'..."

* **Rationalizations Table**: Mapping excuses like "Too tedious for small changes" to rebuttals

* **Quick Reference**: Table of commit types (feat, fix, docs, style, refactor, test, chore)

## Community Feedback and Activation Rates

* The **Claude Code** community has extensively tested skill activation reliability—and these findings directly inform how `/forge-prompt` structures its outputs.

* One systematic study found that skills activate only about 20% of the time with simple instruction hooks, but implementing a **forced evaluation hook**—which makes **Claude** explicitly evaluate each skill with YES/NO reasoning before proceeding—achieved **84% activation rates**. [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1oywsa1/claude_code_skills_activate_20_of_the_time_heres/)

* Key factors that improve activation:

 - Rich description fields with concrete trigger conditions

 - Technology-agnostic problem descriptions

 - Error message keywords and symptom language

 - Descriptive naming with active voice ("creating-skills" not "skill-creation")

* This is precisely why `/forge-prompt` enforces **YAML** frontmatter with detailed trigger conditions as its first required component—it's not bureaucracy, it's proven activation optimization.

## Why This Matters for **AI**-Assisted Development

* The patterns discussed above aren't just theoretical—they have real implications for daily development workflows.

* As **Boris** from the **Claude Code** team noted on **Hacker News**: "If there is anything **Claude** tends to repeatedly get wrong, not understand, or spend lots of tokens on, put it in your **CLAUDE.md**. **Claude** automatically reads this file and it's a great way to avoid repeating yourself." [[Link]](https://news.ycombinator.com/item?id=46256606)

* The `/forge-prompt` command takes this principle further by providing a **systematic methodology** for creating instructions that:

 - Anticipate failure modes before they occur

 - Close loopholes that **LLM**s might exploit

 - Use language patterns proven to improve compliance

 - Include verification mechanisms to confirm success

## Getting Started with /forge-prompt

* To use `/forge-prompt`, create a file at `~/.claude/commands/forge-prompt.md` (for global access) or `.claude/commands/forge-prompt.md` (for project-specific).

* Copy the complete command template provided below and save it.

* Invoke it with any instruction topic:

```bash

> /forge-prompt [Your instruction topic here]

```

* The command will guide Claude through creating all 9 required components, ensuring no critical element is missed.

## The Complete /forge-prompt Command

* Copy the entire content below and save it as `forge-prompt.md` in your `.claude/commands/` directory:

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

1\. **Prevent rationalization** - The #1 failure mode is "this case is different"

2\. **Force discipline** - Structure eliminates decision fatigue and shortcuts

3\. **Make failure visible** - Clear criteria reveal when you're off track

4\. **Be actionable** - Every rule has a concrete action, not abstract advice

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

\- "Use when encountering X, before doing Y"

\- "Use when starting X that requires Y"

\- "Use when finishing X, before claiming Y"

**Example:**

description: Use when encountering any bug, before proposing fixes - four-phase framework that ensures understanding before attempting solutions

### 2. Iron Law (Non-Negotiable Core Rule)

The ONE rule that, if broken, guarantees failure.

**Format:**

## The Iron Law

\\`\\`\\`

[ALL CAPS, IMPERATIVE STATEMENT]

\\`\\`\\`

[Supporting statement about why this matters]

**Violating the letter of this rule is violating the spirit of [skill name].**

**Examples:**

\- `NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST`

\- `NO REPORT WITHOUT 15+ SEARCHES AND PHASE ZERO FIRST`

\- `NO CODE WITHOUT FAILING TEST FIRST`

\- `NO COMMIT WITHOUT VERIFICATION COMMAND OUTPUT`

### 3. When to Use / When NOT to Use

**Format:**

## When to Use

Use for [CATEGORY]:

\- Specific scenario 1

\- Specific scenario 2

\- Specific scenario 3

**Use this ESPECIALLY when:**

\- Counter-intuitive trigger 1 (when you want to skip it most)

\- Counter-intuitive trigger 2

\- Counter-intuitive trigger 3

**Don't skip when:**

\- Excuse that seems valid but isn't

\- Another excuse

\- Time pressure excuse

**Key insight:** The "ESPECIALLY when" section should list situations where people are MOST tempted to skip it.

### 4. Process/Phase Structure

Break the skill into clear, sequential phases with gates (checkpoints that must be passed before proceeding).

**Format:**

## The [Number] Phases

You MUST complete each phase before proceeding to the next.

### Phase 1: [Name]

**[GATE CONDITION]:**

1\. **Step Name**

  - Substep detail

  - Substep detail

  - Success criteria

2\. **Step Name**

  - Substep detail

**Gate patterns:**

\- "BEFORE attempting ANY [action]:"

\- "You cannot proceed to Phase N until:"

\- "If [condition], STOP and return to Phase 1"

### 5. Red Flags Section

Mental patterns that signal you're about to fail.

**Format:**

## Red Flags - STOP and [Action]

If you catch yourself thinking:

\- "[Rationalization thought 1]"

\- "[Rationalization thought 2]"

\- "[Shortcut thought 1]"

\- "[Overconfidence thought 1]"

\- "[Time pressure thought 1]"

**ALL of these mean: STOP. [Specific action to take].**

**Common red flag patterns:**

\- "Quick fix for now, investigate later"

\- "This case is different/simple"

\- "I already know what the problem is"

\- "Just try this and see"

\- "I don't have time for the full process"

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

\- "Simple issues have root causes too. Process is fast for simple cases."

\- "Emergency pressure is exactly when systematic approach saves time."

\- "Partial understanding guarantees bugs. Read it completely."

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

\- **[Principle name]** - [One line explanation]

\- **[Principle name]** - [One line explanation]

\- **[Principle name]** - [One line explanation]

**Or alternative closing format:**

## Summary

**Starting [task type]:**

1\. [First action]

2\. [Second action]

3\. [Third action]

**[Situation]?** [Action].

**[Key insight] = [mandatory action].**

### 9. Integration / Related Skills (Optional but Recommended)

**Format:**

## Integration with Other Skills

**This skill requires using:**

\- **[skill-name]** - REQUIRED when [condition]

\- **[skill-name]** - REQUIRED for [purpose]

**Complementary skills:**

\- **[skill-name]** - [When to use together]

---

## Language \& Tone Guide

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

\- **ALL CAPS** for critical terms: MUST, NEVER, ALWAYS, REQUIRED, CRITICAL, STOP

\- **Code blocks** for Iron Laws and key rules

\- **Bold** for section headers and key terms

\- **Tables** for comparisons and quick reference

\- **Bullet points** for lists, **numbered lists** for sequences

### Philosophical Phrases to Include

\- "Violating the letter of this rule is violating the spirit of [X]"

\- "If you think [X], you are rationalizing"

\- "The moment you feel [X] is the most dangerous moment"

\- "ALL of these mean: STOP."

\- "[Excuse] is ALWAYS wrong"

\- "This is not negotiable. This is not optional."

---

## Anti-Pattern Warnings

**DO NOT create instructions that:**

\- ❌ Use soft language ("consider", "try to", "you might want to")

\- ❌ Lack an Iron Law (the ONE rule that cannot be broken)

\- ❌ Skip the Red Flags section (failing to anticipate rationalization)

\- ❌ Have vague success criteria ("do a good job")

\- ❌ Allow wiggle room ("unless you have a good reason")

\- ❌ Assume good faith ("you probably know when to skip this")

\- ❌ Are too abstract (no concrete actions or examples)

\- ❌ Are too long without clear phases (wall of text)

**DO create instructions that:**

\- ✅ Have ONE non-negotiable Iron Law

\- ✅ Anticipate every excuse with direct rebuttals

\- ✅ Include measurable success criteria

\- ✅ Gate each phase with clear conditions

\- ✅ Use strong, unambiguous language

\- ✅ Provide concrete examples and patterns

\- ✅ Are scannable (tables, bullets, clear headers)

---

## Final Verification Checklist

Before considering the instruction complete, verify:

### Structure Checklist

\- [ ] YAML frontmatter with name and description (with trigger condition)

\- [ ] Iron Law in code block with supporting statement

\- [ ] When to Use section with "ESPECIALLY when" counter-intuitive triggers

\- [ ] Clear phases with gate conditions

\- [ ] Red Flags section with "If you catch yourself thinking" pattern

\- [ ] Common Rationalizations table with Excuse | Reality format

\- [ ] Quick Reference table for one-glance summary

\- [ ] Key Principles or Summary section

### Language Checklist

\- [ ] Uses MUST, NEVER, ALWAYS, REQUIRED appropriately

\- [ ] No soft language (should, consider, try to, might)

\- [ ] Includes at least 3 "Violating the letter" type phrases

\- [ ] Red flags end with "ALL of these mean: STOP"

\- [ ] Each rationalization has a direct, no-hedge rebuttal

### Content Checklist

\- [ ] Iron Law is ONE clear rule (not multiple)

\- [ ] Red Flags include time-pressure and overconfidence thoughts

\- [ ] Rationalizations table has at least 5 entries

\- [ ] Success criteria are measurable, not vague

\- [ ] Examples are concrete and actionable

---

## Output Location

Save the generated instruction to:

\- **For skills:** `.claude/plugins/[plugin-name]/skills/[skill-name]/SKILL.md`

\- **For commands:** `.claude/commands/[command-name].md`

\- **For standalone:** `docs/instructions/[name].md` or user-specified path

---

## Execution

Now create a bulletproof instruction for **$ARGUMENTS** following ALL components above.

Use TodoWrite to track each of the 9 components as you complete them.

Remember: **If you skip any component, the instruction will fail in production.**

```

## Conclusion

* The `/forge-prompt` custom command represents a synthesis of hard-won lessons from **Anthropic**'s official plugins and the battle-tested **Superpowers** framework.

* I built this tool because I was tired of writing instructions that **Claude** would ignore, rationalize around, or interpret too loosely.

* It addresses the fundamental challenge of **LLM** instruction design: **how do you write instructions that an AI will actually follow, even when it's tempted to take shortcuts?**

* The answer lies in strong language, explicit anti-rationalization tables, mandatory checklists, and Iron Laws that leave no room for interpretation.

* For developers serious about maximizing their productivity with **Claude Code**, mastering instruction design through tools like `/forge-prompt` is no longer optional—it's essential.

* Copy the complete template above, save it to your `.claude/commands/` directory, and start forging bulletproof instructions today.

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

