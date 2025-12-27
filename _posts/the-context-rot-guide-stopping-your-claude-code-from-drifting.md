# The Context Rot Guide: Stopping Your Claude Code from Drifting

## Introduction

* "The first 10 steps are genius, but once the context window gets saturated, the agent just... drifts." This observation from a **Reddit** user perfectly captures what **Claude Code** practitioners call **Context Rot** — the phenomenon where **AI** coding agents progressively lose their ability to recall information and make coherent decisions during long sessions. [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/agents_turn_into_goldfish_after_50_steps_how_are/)

* The community has colorfully named this the "goldfish syndrome" — your agent remembers brilliantly for the first few exchanges, then starts forgetting file paths, importing from non-existent modules, and reversing decisions it made minutes earlier. This isn't a bug in **Claude Code**; it's a fundamental architectural constraint of **Large Language Models**(**LLMs**).

* As of December 2025, there is no silver bullet solution. What exists instead is a growing ecosystem of engineering approaches — from **Anthropic**'s official **Context Compaction** and **Subagent** architectures to community-developed tools like **Beads** and **Memory MCP** servers. Experienced engineers are finding their own answers through trial and error, while the industry converges on a new discipline: **Context Engineering**.

## The Anatomy of Context Rot

### What Exactly Is Context Rot?

* **Context Rot** refers to the progressive degradation of an **LLM**'s performance as its input token count increases. [[Link]](https://research.trychroma.com/context-rot) The term was first coined on **Hacker News** in June 2025 and was academically established by **Chroma Research** in their July 2025 technical report.

* The phenomenon manifests in several related symptoms:

| Term | Definition |

|------|------------|

| **Context Rot** | Performance degradation as input tokens increase |

| **Context Drift** | Agent deviating from original goals over extended sessions |

| **Lost in the Middle** | Failure to retrieve information located in the middle of context |

| **Goldfish Syndrome** | Community metaphor: "forgetting what happened 3 seconds ago" |

### The Mathematical Reality: O(n²) Attention Complexity

* The root cause lies in the **Transformer** architecture itself. [[Link]](https://arxiv.org/abs/2209.04881) Self-attention requires computing pairwise relationships between all tokens, resulting in O(n²) computational complexity where n equals the number of tokens.

* For a 200K token context window, this means processing 40 billion pairwise relationships. [[Link]](https://d2l.ai/chapter_attention-mechanisms-and-transformers/self-attention-and-positional-encoding.html) **Anthropic**'s engineering documentation explicitly acknowledges this constraint:

> "LLMs have an 'attention budget' that they draw on when parsing large volumes of context. Every new token introduced depletes this budget by some amount."

> — [[Link]](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) **Anthropic** Engineering Blog (September 2025)

### Chroma Research: The Empirical Evidence

* **Chroma Research**'s July 2025 study tested 18 major **LLMs** including **GPT-4.1**, **Claude 4**, **Gemini 2.5**, and **Qwen3**. [[Link]](https://research.trychroma.com/context-rot) Their findings were sobering:

| Finding | Implication |

|---------|-------------|

| Non-uniform performance degradation | All models degrade as input length increases |

| Needle-Question semantic distance | Performance drops faster when questions differ semantically from answers |

| Distractor impact | Irrelevant information causes non-linear performance decay |

| Haystack structure matters | Logically structured text performs differently than shuffled text |

* Crucially, the research revealed that traditional **Needle-in-a-Haystack** (**NIAH**) benchmarks overestimate real-world performance because they only test simple lexical matching, not complex reasoning tasks.

### The "Lost in the Middle" Problem

* **Stanford** researchers first documented this phenomenon in 2023. [[Link]](https://arxiv.org/abs/2307.03172) **LLMs** exhibit a U-shaped attention pattern: they recall information well from the beginning and end of their context window, but struggle with content in the middle.

```

┌─────────────────────────────────────────────────────────┐

│  Beginning      │     Middle        │      End          │

│  (High Recall)  │   (Low Recall)    │  (High Recall)    │

└─────────────────────────────────────────────────────────┘

```

* This means that in a long **Claude Code** session, the instructions you gave early on (stored in **CLAUDE.md**) and your most recent requests are processed well, but everything in between becomes progressively harder for the model to access.

## How Context Rot Manifests in Claude Code

* **Reddit** users have documented specific failure patterns that occur after extended sessions:

| Symptom | User Description |

|---------|------------------|

| Circular editing | "Optimized with **Redis**, then switched to **Memcached** next session, then back to **Redis**" [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/) |

| Path amnesia | "Forgets file paths generated 5 minutes ago, imports from non-existent modules" [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/) |

| Config flip-flopping | "Port 3000 → 3001 → 3000 in consecutive changes" |

| Instruction drift | "Completely ignores **CLAUDE.md** directives late in context" |

| Premature completion | "Declares 'project complete' when only halfway done" |

* One user's observation went viral in the community: "**Claude Code** has the memory of a goldfish and the confidence of a 10x engineer." [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1mo15er/claude_code_has_the_memory_of_a_goldfish_and_the/)

## Anthropic's Official Solutions

### 1. Context Compaction

* **Claude Code** implements automatic context compaction when approaching context limits. [[Link]](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) The system summarizes conversation history, preserving:

 - Architectural decisions

 - Unresolved bugs

 - Implementation details

 - Recently accessed files (typically the last 5)

* Users can trigger manual compaction with `/compact [instructions]` to control what gets preserved. The limitation: aggressive compaction can lose subtle but important context.

### 2. Context Editing (September 2025)

* **Anthropic** introduced programmatic context editing in their **API**. [[Link]](https://platform.claude.com/docs/en/build-with-claude/context-editing) Developers can configure automatic cleanup rules:

```json

{

 "context_management": {

   "edits": [{

     "type": "clear_tool_uses_20250919",

     "trigger": { "type": "input_tokens", "value": 30000 },

     "keep": { "type": "tool_uses", "value": 3 }

   }]

 }

}

```

* This allows clearing old tool call results while maintaining conversation flow — a surgical approach compared to full compaction.

### 3. Subagent Architecture

* **Anthropic**'s recommended pattern for complex tasks involves delegating work to specialized subagents. [[Link]](https://platform.claude.com/docs/en/agent-sdk/subagents) Each subagent operates in its own context window and returns only summarized results to the main orchestrator.

```

┌─────────────────────────────────────────────────────┐

│                 Main Orchestrator                    │

│            (High-level planning + coordination)      │

└───────────┬─────────────┬─────────────┬─────────────┘

           │             │             │

           ▼             ▼             ▼

     ┌──────────┐  ┌──────────┐  ┌──────────┐

     │ Search   │  │ Implement│  │ Test     │

     │ Agent    │  │ Agent    │  │ Agent    │

     └──────────┘  └──────────┘  └──────────┘

          ↓             ↓             ↓

     Summary        Summary        Summary

     (1-2K tokens)  (1-2K tokens)  (1-2K tokens)

```

* The key insight: a subagent might consume 30,000 tokens exploring a codebase, but only 1,500 tokens of distilled results return to the main agent.

### 4. Long-Running Agent Harness (November 2025)

* **Anthropic**'s research on long-running agents identified four major failure modes and corresponding solutions. [[Link]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

| Failure Mode | Solution |

|--------------|----------|

| One-shotting (attempting everything at once) | Feature List file (**JSON** format with `passes: true/false`) |

| Undocumented state on context exhaustion | Git commits + Progress file mandatory |

| No end-to-end testing | Browser automation for **E2E** verification |

| Time wasted figuring out how to run app | Auto-generated `init.sh` script |

* Their **Two-Agent Harness** pattern separates concerns:

 1. **Initializer Agent**: Sets up environment (feature list, git repo, progress file)

 2. **Coding Agent**: Implements one feature per session, commits progress

## Community-Developed Solutions

### 1. AST-Based Project Map Injection

* The most technically elegant community solution involves injecting **Abstract Syntax Tree** (**AST**) maps at every turn. [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/)

> "I built a local tool that scans the AST and generates a compressed skeleton of the repo (just signatures and imports), and I force that into the system prompt."

> — u/Necessary-Ring-6060

* This approach offers several advantages over **RAG** (Retrieval-Augmented Generation):

 - **Deterministic**: No vector search uncertainty

 - **Structural accuracy**: Preserves code hierarchy that semantic search loses

 - **Hallucination prevention**: Agent sees the actual map, doesn't need to remember it

### 2. Beads: Agent-First Issue Tracker

* **Steve Yegge**'s **Beads** has emerged as a popular solution for multi-session context preservation. [[Link]](https://github.com/steveyegge/beads) Unlike **GitHub Issues**, **Beads** is designed specifically for implementation notes — decisions, blockers, and progress that agents need to reconstruct context.

```bash

bd init                    # Initialize in project

bd create "Implement auth" # Create task

bd update auth-001 --notes "COMPLETED: JWT. NEXT: Rate limiting"

```

* A three-week trial report from **Reddit**: [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1ov1z94/update_i_tried_beads_for_3_weeks_after_asking/)

> "The amnesia is gone. I'd spend considerable time re-explaining context after every compaction. Now Claude reconstructs full context automatically by reading bead notes."

> — u/lakshminp

### 3. Two-Tab Claude System

* Some practitioners maintain separate **Claude** instances for different concerns:

| Window 1 (Research/QA) | Window 2 (Developer) |

|------------------------|----------------------|

| Bug analysis | Implementation |

| File/line identification | Code writing |

| Uses 80-90% of context | Focused execution |

* Results from Window 1 feed Window 2 as distilled, actionable instructions.

### 4. /clear + Plan File Strategy

* The most accessible strategy requires no additional tooling:

1\. Create `PLAN.md` with checklist before starting

2\. Check off completed items as work progresses

3\. Run `/clear` to reset context

4\. Resume with "Continue with PLAN.md"

> "You have to give it step by step instructions of exactly what to do, and check the result at each step. Then /clear after each task is completed and tested to be working."

> — u/TotalBeginnerLol [[Link]](https://www.reddit.com/r/ClaudeCode/)

### 5. Memory MCP Servers

* The **Model Context Protocol** (**MCP**) ecosystem has spawned several memory-focused servers:

| Tool | Key Feature |

|------|-------------|

| **Serena MCP** | Semantic code search + language server integration [[Link]](https://github.com/oraios/serena) |

| **Basic Memory MCP** | Local markdown-based persistent memory |

| **Heimdall MCP** | "Remember context about X" command interface |

| **a24z-Memory** | File anchor-based note system |

### 6. Superpowers Plugin: The Comprehensive Solution

* **Jesse Vincent**'s (obra) **Superpowers** plugin bundles multiple context management techniques into a unified workflow system. [[Link]](https://github.com/obra/superpowers) Unlike piecemeal solutions, it provides a complete lifecycle from initial brainstorming to merged **PR**.

```bash

/plugin marketplace add obra/superpowers-marketplace

/plugin install superpowers@superpowers-marketplace

```

* **Core context management features**:

 - **Subagent-driven development**: Each task runs in isolated context, returning only summarized results

 - **Plan-file architecture**: Auto-generated `docs/plans/YYYY-MM-DD-<feature>.md` for session-independent continuity

 - **Automatic context handoff**: New sessions resume by reading plan files—no manual context reconstruction

 - **TDD enforcement**: The RED-GREEN-REFACTOR cycle becomes mandatory, not optional

* The session-independent workflow is particularly noteworthy:

```bash

# Session 1: Plan and save

> /superpowers:brainstorm Implement rate limiting

# Design saved to docs/plans/2025-12-26-rate-limiting.md

# Session 2 (any time later): Resume

> Read docs/plans and continue

# Superpowers auto-invokes executing-plans skill

```

* **Simon Willison**, **Django** co-creator, endorsed this approach:

> "**Jesse** is one of the most creative users of coding agents that I know. It's very much worth the investment of time to explore what he's shared." [[Link]](https://simonwillison.net/2025/Oct/10/superpowers/)

* The token efficiency is significant—core bootstrap loads under 2,000 tokens, with heavy work delegated to subagents that don't pollute the main context. [[Link]](https://bsky.app/profile/s.ly)

## Token Economics: The Cost of Fighting Context Rot

* **Anthropic**'s own data reveals significant token overhead for agent patterns: [[Link]](https://www.constellationr.com/blog-news/insights/anthropics-multi-agent-system-overview-must-read-cios)

| Interaction Type | Token Multiplier |

|------------------|------------------|

| Standard chatbot | 1x (baseline) |

| Single agent | ~4x |

| Multi-agent system | ~15x |

* This means multi-agent architectures — while effective against **Context Rot** — consume roughly 15 times more tokens than simple chat. For **Claude Pro/Max** subscribers, this can rapidly exhaust usage limits.

## Practical Recommendations

### Choose Your Strategy Based on Task Scope

| Scenario | Recommended Approach |

|----------|---------------------|

| Simple feature (1-2 hours) | Frequent `/clear` usage |

| Multi-session project | **Beads** + Progress files |

| Large-scale refactoring | Subagent architecture |

| Complex debugging | Two-tab system |

| Repetitive workflows | **CLAUDE.md** + Hooks |

### Anti-Patterns to Avoid

| Avoid | Do Instead |

|-------|------------|

| Single long session for all work | `/clear` after each completed unit |

| Pasting large text blocks | Use file reading tools |

| Vague instructions ("fix this") | Specify file, line, and exact problem |

| Relying solely on auto-compaction | Manually run `/compact [instructions]` |

| Overloading **CLAUDE.md** | Keep only universal, minimal guidelines |

### The Simple Is Best Approach: Let Superpowers Handle It

* For practitioners who prefer minimal tooling overhead, the instinct is to manually create **PLAN.md** files with checklists and status tracking. But there's a more elegant solution: `Superpowers` already implements this pattern with battle-tested workflows.

* Instead of managing plan files manually, **Superpowers** provides the complete infrastructure: [[Link]](https://github.com/obra/superpowers)

| Manual Approach | Superpowers Equivalent |

|-----------------|------------------------|

| Create `PLAN.md` manually | `/superpowers:write-plan` auto-generates `docs/plans/YYYY-MM-DD-<feature>.md` |

| Write checklist items yourself | Agent asks clarifying questions, then produces 2-5 minute tasks with exact file paths |

| Update status as work progresses | `executing-plans` skill tracks completion automatically |

| Remember to run `/clear` | Subagent architecture handles context isolation inherently |

| Resume with "Continue with PLAN.md" | New session: "Read docs/plans and continue" → auto-resumes |

* The workflow becomes remarkably simple:

```bash

# Session 1: Design and plan

> /superpowers:brainstorm Add user authentication to my app

# Answer questions one at a time → design saved to docs/plans/ → auto-commit

# Session 2 (hours or days later): Resume

> Read docs/plans and continue

# Superpowers auto-loads executing-plans → picks up exactly where you stopped

```

* This isn't just convenience—it's the same **session-independent development** pattern that **Anthropic**'s research team identified as essential for long-running agents, implemented as a plugin. [[Link]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

* The key insight: you don't need to reinvent the plan-file pattern. **Superpowers** has already refined it through adversarial testing and real-world usage by **Claude Code** practitioners.

## Conclusion: Context Engineering as the New Frontier

* **Context Rot** represents a fascinating inflection point in **AI** coding tools. The problem isn't solvable through raw compute or larger context windows — **Anthropic** themselves acknowledge that "context windows of all sizes will be subject to context pollution and information relevance concerns." [[Link]](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) The O(n²) attention complexity is architectural, not incidental.

* What we're witnessing is the emergence of **Context Engineering** as a distinct discipline. Where **Prompt Engineering** focused on crafting the right words, **Context Engineering** asks: "What is the minimal, highest-signal set of tokens that maximizes desired outcomes?" This requires thinking about information lifecycle, session boundaries, and external state persistence.

* The irony is rich: to make **AI** agents work on complex, long-running tasks, we're essentially building the same infrastructure that human engineering teams have developed over decades — issue trackers, progress files, documentation practices, and handoff protocols. The "goldfish" learns not by getting a better memory, but by writing things down.

* There is no single correct answer today. The field is actively evolving, with **Anthropic** shipping new capabilities quarterly and the community iterating on novel approaches. What works best depends on project complexity, personal workflow preferences, and tolerance for tooling overhead. For those seeking comprehensive solutions with minimal configuration, **Superpowers** stands out—it implements the plan-file pattern, subagent architecture, and session-independent continuity that **Anthropic**'s own research recommends, packaged as a single plugin. You don't need to manually create `PLAN.md` files or reinvent context management patterns; the infrastructure already exists. [[Link]](https://github.com/obra/superpowers)

* The engineers who thrive with **AI** coding agents will be those who internalize this reality: the context window is not infinite memory — it's expensive, degrading working memory. Managing it deliberately isn't a workaround; it's the core skill.

## References

* **Anthropic Engineering**

 * https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

 * https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents

* **Chroma Research**

 * https://research.trychroma.com/context-rot

* Academic Research

 * https://arxiv.org/abs/2307.03172 (**Stanford** "Lost in the Middle")

 * https://arxiv.org/abs/2209.04881 (Self-Attention Complexity)

* **Claude** Documentation

 * https://platform.claude.com/docs/en/build-with-claude/context-editing

 * https://platform.claude.com/docs/en/agent-sdk/subagents

* Community Tools

 * https://github.com/steveyegge/beads (**Beads** issue tracker)

 * https://github.com/obra/superpowers (**Superpowers** plugin)

 * https://github.com/oraios/serena (**Serena MCP**)

* **Superpowers** Expert Analysis

 * https://simonwillison.net/2025/Oct/10/superpowers/ (**Simon Willison** endorsement)

* Community Discussions (**Reddit**)

 * https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/ (Original "goldfish" discussion)

 * https://www.reddit.com/r/ClaudeCode/comments/1ov1z94/ (**Beads** 3-week review)

