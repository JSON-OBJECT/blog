# Why Gemini Forgets You: The Hidden Limits of Saved Info \& Gems

## TL;DR

* **Gemini** uses "conservative by design" personalization—it has your data but uses it selectively, requiring explicit triggers

* **Saved Info** has hidden limits (~10-75 active slots, ~1,500 characters each) with silent **FIFO** truncation

* **Gems** don't inherit **Saved Info**—you must copy data manually into each **Gem**'s instructions

* **Gemini 3.0 Pro** has known context retention bugs after the December 4, 2025 update (**Google** acknowledged)

* Best workaround: **Google Sheets** + **Gems** for time-series data, **NotebookLM** integration for research

---

## Introduction

* In **Iron Man**, **J.A.R.V.I.S.** doesn't just answer questions—it *knows* Tony Stark. [[Link]](https://en.wikipedia.org/wiki/J.A.R.V.I.S.) In **Her**, Samantha develops genuine memories through relationship. [[Link]](https://en.wikipedia.org/wiki/Her_(2013_film)) These fictional **AI** companions share one capability no real **AI** possesses today: the ability to permanently write experiences into their own minds. Every **LLM** deployed in production is fundamentally *read-only*—neural network weights frozen at deployment. [[Link]](https://www.letta.com/blog/stateful-agents) What we call "**AI** memory" in 2025 is an elaborate collection of workarounds: context injection, external databases, and summary documents. As one **Hacker News** analysis noted, memory systems are "14–77× more expensive and 31–33% less accurate than just passing the full history." [[Link]](https://news.ycombinator.com/item?id=46032521)

* **Google Gemini** sits atop the world's largest personal data repository—**Gmail**, **Drive**, **Calendar**, **Photos**—yet deliberately restrains itself from using it. This is not a bug. This is **Google**'s philosophical choice in the **AI** memory wars of 2025. While **ChatGPT** aggressively memorizes everything and **Claude** offers transparent tool-based memory, **Gemini** takes a third path: "conservative by design" personalization that activates only when explicitly triggered.

* This article dissects exactly how **Gemini**'s personalization architecture works, explains why the "amnesia syndrome" you're experiencing is by design, and provides a systematic framework for managing your personalization data—including workarounds for architectural limitations stemming from the fundamental read-only nature of **LLM**s.

---

## The Architecture of Gemini Memory: Not RAG, Something Simpler

* Let's dispel the first misconception: **Gemini** does not use **Retrieval-Augmented Generation (RAG)** for personalization. According to reverse-engineering analysis by **Shlok Khemani**, **Gemini** employs a far simpler mechanism—compressed summary injection. [[Link]](https://www.shloked.com/writing/gemini-memory)

* The system operates around a single document called `user_context`:

| Category | Contents |

|----------|----------|

| **1. Demographic Information** | Name, age, location, profession |

| **2. Interests \& Preferences** | Topics of interest, tech stack, goals |

| **3. Relationships** | Important people in your life |

| **4. Dated Events/Projects/Plans** | Time-tagged activity records |

| **5. Recent Context** | Last few conversation turns |

* Unlike true **RAG** systems that use vector databases, chunk embeddings, and query-based retrieval, **Gemini** simply injects this compressed summary into every conversation's context window. No semantic search. No relevance scoring. Just brute-force context injection.

* "No vector database, no knowledge graph, no **RAG**. They just dump everything in every time," observes **Khemani** in his analysis of both **ChatGPT** and **Gemini**'s memory systems. [[Link]](https://www.shloked.com/writing/chatgpt-memory-bitter-lesson)

* Here is where **Gemini**'s architectural advantage becomes relevant: among the major **AI** platforms, **Gemini 3 Pro** offers the largest context window by a significant margin—1 million tokens, equivalent to approximately 1,500 pages of text or 30,000 lines of code. [[Link]](https://9to5google.com/2025/12/24/google-ai-pro-ultra-features/) By comparison, **OpenAI**'s **GPT-5.2** (released December 11, 2025) supports 400K tokens, [[Link]](https://venturebeat.com/ai/openais-gpt-5-2-is-here-what-enterprises-need-to-know) and **Anthropic**'s **Claude Opus 4.5** offers 200K tokens (with up to 1M tokens available for enterprise deployments). [[Link]](https://aws.amazon.com/bedrock/anthropic/)

* This gives **Gemini 3 Pro** a 2.5× advantage over **GPT-5.2** and 5× over **Claude Opus 4.5**'s standard window. The "brute-force context injection" approach has more room before hitting limits.

### The Three-Layer Personalization Stack

* **Gemini**'s personalization operates across three independent layers, each with distinct behaviors:

| Layer | Feature Name | Function | Priority |

|-------|--------------|----------|----------|

| **Level 1** | **Gemini Apps Activity** | Controls whether conversations are stored at all | Foundation |

| **Level 2** | **Personal Context** | Analyzes past chats to build user profile | Secondary |

| **Level 3** | **Saved Info** | User-defined explicit instructions | Highest |

* **Personal Context** (labeled "Your past chats with **Gemini**" in settings) allows **Gemini** to analyze your conversation history to extract patterns and preferences. [[Link]](https://support.google.com/gemini/answer/15637730)

* **Saved Info** (labeled "Things to remember" in settings) contains explicit instructions you've manually entered. This takes precedence over automatically-derived **Personal Context**.

---

## The "Conservative by Design" Policy: Why Gemini Pretends Not to Know You

* A revealed system prompt from June 2025 shows how **Gemini**'s personalization guidelines actually work:

```

Guidelines on how to use the user information for personalization:

\- Use Relevant User Information \& Balance with Novelty

\- Acknowledge Data Use Appropriately (only when it significantly shapes your response)

\- Avoid Over-personalization... as a default rule, DO NOT use the user's name

\- Prioritize \& Weight Information Based on Intent/Confidence

```

* This "balanced approach" policy means **Gemini** has your data but is instructed to use it *selectively* rather than aggressively—personalization activates only when "directly relevant to the user's current query." [[Link]](https://www.reddit.com/r/LLMDevs/comments/1l3rt10/)

* The trigger conditions include phrases like:

 - "Based on my interests..."

 - "Considering my previous conversations..."

 - "Given what you know about me..."

* Without these explicit triggers, **Gemini** often behaves as if it has no memory—not because the data is missing, but because the system prompt's "avoid over-personalization" guideline causes it to err on the side of caution.

### The Selective Activation Model

* How **Gemini**'s personalization actually works:

| Step | Process | Outcome |

|------|---------|---------|

| **Step 1** | Check if user data exists | Data present in context |

| **Step 2** | Apply system prompt guidelines | "Use only when directly relevant" |

| **Step 3** | Evaluate relevance to current query | Is personalization genuinely helpful here? |

| **Step 4a** | High relevance detected | Personalization **ACTIVATED** (implicitly woven into response) |

| **Step 4b** | Low relevance or ambiguous | Personalization **SUPPRESSED** (to avoid "creepy" over-personalization) |

* This explains the frustrating inconsistency users experience. The information you saved isn't gone—it's being filtered through a relevance gate that often errs on the side of caution. Explicit trigger phrases help signal to **Gemini** that personalization is genuinely wanted.

---

## The Saved Info Crisis: Silent Truncation and Hidden Limits

* Beyond the "conservative by design" behavior, **Saved Info** has structural limitations that compound the "amnesia" problem.

### The Slot Limit Controversy

* Community testing reveals conflicting reports on **Saved Info** limits, suggesting **Google** may be A/B testing different configurations:

| Reported by | Observed Slots | Notes |

|-------------|----------------|-------|

| User A | ~10 active | Oldest items silently ignored [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pdxddr/) |

| User B | ~75 slots | Copied from **ChatGPT** memories [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1lbmg9s/) |

* "There's a hidden limit on active processing. Add too many, and the oldest instructions are quietly 'forgotten.' They're still on the settings page, but they're not loaded into active context," reports one Reddit user.

* The discrepancy suggests the effective limit may depend on **total token count** rather than item count—each slot allows approximately **1,500 characters** according to **Lifehacker** testing. [[Link]](https://lifehacker.com/tech/saved-info-google-gemini)

### FIFO Truncation

* When you exceed these limits, **First-In-First-Out (FIFO)** truncation kicks in. Your oldest saved information gets silently dropped from the active context window—with no warning, no notification.

| Metric | Observed Value |

|--------|----------------|

| Characters per slot | ~1,500 |

| Active token limit | Estimated 16K-32K tokens (varies by account) |

### The Timestamp Problem

* **Saved Info** items have no date/time metadata. When you ask about "my current weight," **Gemini** cannot distinguish between:

 - Weight you entered on December 22nd: 75.2kg

 - Weight you entered on December 27th: 74.5kg

* Without timestamps, **Gemini** may reference whichever entry it encounters first in the context—often the older one—creating the illusion that it "forgot" your most recent update.

---

## The Gems Isolation Problem

* Many users assume **Gems** (custom **AI** assistants) inherit **Saved Info**. They don't.

* "I stored important information in **Saved Info**, but my custom **Gem** doesn't recognize it at all. Is this by design?" reports a confused user. [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1nt6yoe/)

* **Gems** are completely siloed from the main **Gemini** instance:

### Personalization Data Flow

| Source | Target | Transfer Status | Note |

|--------|--------|-----------------|------|

| **Saved Info** | Regular **Gemini** Chat | ✓ **Transferred** | Applied by default |

| **Saved Info** | **Gems** (Custom Assistants) | ✗ **NOT Transferred** | Requires separate setup |

| **Saved Info** | **Gemini Live** | △ **Partial** | Manual trigger required |

* If you want your **Gem** to know your preferences, you must manually copy **Saved Info** content into the **Gem**'s instruction prompt.

* **Gems** support up to 10 attached files, with the following specifications:

 - Maximum file size: 32MB per file

 - Supported formats: **Google Docs**, **Sheets**, **PDF**, **TXT**, code files

 - **Google Docs/Sheets auto-sync**: Updates to source files reflect automatically in your **Gem** [[Link]](https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html)

---

## Model-Specific Limitations: Flash vs Pro

* Not all **Gemini** models support personalization equally.

| Model | Personal Context | Saved Info | Connected Apps |

|-------|------------------|------------|----------------|

| **Gemini 3 Pro** | ✓ | ✓ | ✓ |

| **Gemini 3 Flash** | ❌ | ✓ | ✓ |

| **Gemini Live** | ❌ | △ (manual trigger) | ✓ |

| **Gems** | ❌ | ❌ | ✓ |

* **Personal Context**—the feature that builds your profile from conversation history—only works on **Pro/Thinking** models. If you're using **Flash** and wondering why **Gemini** never seems to remember you, this is why.

* **Note:** Some users report inconsistent behavior during the December transition period. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1piw8v2/) This suggests ongoing A/B testing or staged rollouts.

### The Flash Paradox: When "Lite" Beats "Pro"

* Here's a counterintuitive finding that complicates the Pro vs Flash decision: recent benchmarks show **Gemini 3 Flash** outperforming **Pro** in coding tasks—78.0% vs 76.2% on SWE-bench Verified. [[Link]](https://blog.google/products/gemini/gemini-3-flash/) [[Link]](https://vertu.com/lifestyle/gemini-3-flash-outperforms-pro-in-coding-while-pro-suffers-critical-memory-issues)

* This suggests specialized optimization during knowledge distillation. For coding-focused workflows, **Flash** may actually be preferable despite lacking **Personal Context**—especially given **Pro**'s current context retention bugs.

| Use Case | Recommended | Reason |

|----------|-------------|--------|

| Coding/agentic workflows | **Flash** | 78% SWE-bench > Pro's 76.2% |

| Long-form research/analysis | **Pro** | Personal Context + larger reasoning depth |

| Cost-sensitive applications | **Flash** | Significantly cheaper per token |

| Complex multi-turn conversations | **Pro** (with caution) | Better theoretical context, but Dec 4 bug active |

---

## The Gemini 3.0 Pro Regression

* **Gemini 3 Pro** launched on November 18, 2025, [[Link]](https://llm-stats.com/blog/research/gemini-3-pro-launch) but the **December 4, 2025** introduction of **Deep Think** mode [[Link]](https://analyticsindiamag.com/ai-news-updates/google-launches-gemini-3-deep-think-mode-for-ultra-subscribers/) coincided with significant context retention issues that **Google** has officially acknowledged:

* "**Gemini 3 Pro**'s long context retention is completely broken. It doesn't handle long chats like 2.5 or earlier versions. After a few exchanges, you need to start a new chat," reports one frustrated user. [[Link]](https://www.reddit.com/r/Bard/comments/1phi66l/) Community reports suggest quality degradation typically begins around 4-6 prompts, with severe issues appearing after 10+ turns.

* Reported symptoms include:

 - Severe performance degradation after 10+ turns

 - Claiming uploaded files are "not visible"

 - Literally repeating previous message content (attention mechanism failure suspected)

 - Complete loss of rules/context trained over months after the 3.0 upgrade

* The issue is documented on **Google**'s official **AI Developers Forum**: "Significant Context Retention Degradation After Dec 4 'Deep Think' Update" reports measurable decline in session-level instruction retention. [[Link]](https://discuss.ai.google.dev/t/regression-report-significant-context-retention-degradation-after-dec-4-deep-think-update/111219)

* **Google** has acknowledged: "We're aware of this issue and working on a fix." [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pn2th2/)

---

## Competitive Context: Three Philosophies of AI Memory

* Understanding **Gemini**'s approach requires contrasting it with competitors:

| Characteristic | ChatGPT | Claude | Gemini |

|----------------|---------|--------|--------|

| Default behavior | Always ON (auto-personalization) | Explicit tool calls only | Default OFF (trigger required) |

| Memory structure | 4 modules (complex) | 2 tools (transparent) | 1 document (simple) |

| Context window | 400K tokens | 200K (1M preview via **Bedrock**) | 1M tokens |

| Update cycle | Periodic batch | Real-time search | Periodic batch |

| User editing | Partial | Full | Full |

| Auto-inference | ✓ (aggressive) | △ (on request) | ✗ (almost never) |

| Project separation | ✓ (since 2025.08) | ✓ (built-in) | ✗ (workaround via Gems) |

* **Simon Willison**, the prominent developer and **AI** critic, contrasts the two leading approaches: "**Claude**'s memory feature is implemented as visible tool calls, which means you can see exactly when and how it is accessing previous context... The **OpenAI** system is *very* different: rather than letting the model decide when to access memory via tools, **OpenAI** instead automatically includes details of previous conversations at the start of every conversation." [[Link]](https://simonwillison.net/2025/Sep/12/claude-memory/)

* **Gemini** takes a third path not explicitly covered in **Willison**'s analysis: a "conservative by design" approach that requires explicit user triggers or high relevance to activate personalization.

* **ChatGPT** chose "magical experience" through aggressive auto-personalization. **Claude** chose "transparency" through explicit, visible tool calls. **Gemini** chose "privacy-first restraint" through selective activation and deliberate under-personalization.

---

## The Solution Framework: Making Gemini Actually Remember

* Given these architectural realities, here's a systematic approach to maximizing personalization effectiveness.

### Strategy 1: The Trigger Phrase Protocol

* Since **Gemini** operates on "conservative by design," you can help activate personalization by signaling explicit intent:

**For Saved Info activation:**

```

"Based on my saved information..."

"Considering my preferences you know about..."

"Using what I've told you to remember..."

```

**For conversation history activation:**

```

"Based on our previous conversations..."

"You know my background, so..."

"Given our chat history..."

```

**For Gemini Live:**

```

"Tell me word for word what I asked you to remember."

"Recite the information I saved with you."

```

* This forces **Gemini** to load and reference your personalization data for the current session.

### Strategy 2: The Data Type Matrix

* Different data types require different storage strategies:

| Data Type | Recommended Solution | Reason |

|-----------|---------------------|--------|

| Time-series data (weight, workouts) | **Google Sheets** + **Gem** | Auto-sync, sortable, structured |

| Static preferences (language, tone) | **Saved Info** | Low change frequency |

| Research/learning materials | **NotebookLM** integration | 300 sources, true **RAG** |

| Project-specific context | Individual **Gems** | Isolated memory per project |

### Strategy 3: External Data Management (Sheets or JSON)

* **Saved Info** cannot handle time-series data effectively due to its lack of timestamps. The solution is external structured data.

**Option A: Google Sheets + Gems**

**Step 1: Create a structured Sheet**

| Date | Weight (kg) | Notes |

|------|-------------|-------|

| 2025-12-22 | 75.2 | Holiday overeating |

| 2025-12-25 | 74.8 | Resumed exercise |

| 2025-12-27 | 74.5 | 3 days consecutive cardio |

**Step 2: Create a Gem with the Sheet attached**

Navigate to: gemini.google.com → Gems → Create new Gem

Instructions to include:

```

You are my health management assistant.

Always check the attached Google Sheets for the latest weight data.

Prioritize the most recent entry based on the Date column.

Analyze trends by comparing today's date with historical data.

```

**Step 3: Attach your Sheet as a reference file**

* **Google** officially announced that **Gems** auto-recognize updates to attached **Google Docs** or **Sheets**. [[Link]](https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html)

* "When you update a **Google Docs** file, it automatically updates in your **Gem**. Most other **AI** tools either can't do this or don't do it well," notes one power user. [[Link]](https://profitschool.com/gemini-gems-customized-reliable-ai-assistant/)

**Option B: JSON Context Files for Power Users**

* For complex personalization needs, maintain a structured **JSON** context file with timestamped entries. Upload at the start of each new chat, or attach to a **Gem** for persistent access.

* "**Gemini** has always struggled with this. Instead, I maintain my own context file and inject it into new chats. If you're working with chunkable information, a **JSON** context file is more effective," recommends one user. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pdxddr/)

### Strategy 4: NotebookLM Integration (December 2025)

* The most powerful personalization option as of December 2025 is **NotebookLM** integration—now directly accessible within the **Gemini** app.

**December 2025 Updates:**

* **December 13**: **Google** announced **NotebookLM** integration for **Gemini**, allowing users to attach notebooks as conversation sources. [[Link]](https://www.androidcentral.com/apps-software/googles-gemini-now-integrates-seamlessly-with-notebooklm-for-improved-project-management)

* **December 17**: The integration rolled out via gemini.google.com → Plus menu → **NotebookLM**. [[Link]](https://9to5google.com/2025/12/17/gemini-app-notebooklm/)

* **December 19**: **NotebookLM** upgraded to **Gemini 3** with 8x more context capacity and new "Data Tables" output format. [[Link]](https://9to5google.com/2025/12/19/notebooklm-gemini-3-data-tables/)

| Feature | Saved Info | Gems (10 files) | NotebookLM Integration |

|---------|------------|-----------------|------------------------|

| Source limit | ~10-75 items | 10 files | Up to 300 sources |

| **RAG** method | ✗ Brute-force | △ Limited | ✓ True **RAG** |

| External web sources | ✗ | ✗ | ✓ Websites, YouTube |

| Cross-source search | ✗ | ✗ | ✓ Meta-search |

| Data export | ✗ | ✗ | ✓ Data Tables, Docs |

* "**NotebookLM** is, in my opinion, the best research platform. Put hundreds of websites and documents in, and it uses **RAG** to sort and display the most logical information for your queries," reports one enthusiastic user. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1plornw/)

---

## The Immediate Action Checklist

* Here's the priority-ordered action list for maximizing **Gemini** personalization:

### Essential (Do These First)

| Priority | Action | Path |

|----------|--------|------|

| 1 | Enable **Gemini Apps Activity** + set 36-month auto-delete | Settings → Activity |

| 2 | Enable **Personal Context** (or disable for privacy) | Settings → Personal context |

| 3 | Keep **Saved Info** under 10 items, static preferences only | Settings → Saved info |

| 4 | Use trigger phrases in every conversation | "Based on my saved info..." |

| 5 | Start fresh chats after 4-6 exchanges with **Gemini 3 Pro** | Avoid context degradation bug |

### Advanced (For Power Users)

| Priority | Action | Path |

|----------|--------|------|

| 6 | Migrate time-series data to **Google Sheets** | drive.google.com |

| 7 | Create dedicated **Gems** for major use cases | gemini.google.com/gems |

| 8 | Attach **Sheets** to relevant **Gems** | Gem edit → Add files |

| 9 | Remove dynamic data from **Saved Info** | gemini.google.com/saved-info |

| 10 | Set up **NotebookLM** integration | notebooklm.google.com |

---

## The Troubleshooting Flowchart

* When **Gemini** fails to recognize your saved information:

| Step | Check | Condition | Solution |

|------|-------|-----------|----------|

| **Step 1** | Which model are you using? | **Flash** | Upgrade to **Pro** (**Flash** doesn't support Personal Context) |

| | | **Pro** | Proceed to Step 2 |

| **Step 2** | How many messages in this conversation? | **5+** | Start new chat (**Gemini 3 Pro** context degradation bug) |

| | | **4 or fewer** | Proceed to Step 3 |

| **Step 3** | Did you use an explicit trigger? | **No** | Add "Based on my Saved Info..." |

| | | **Yes** | Suspected bug, retry in new chat |

---

## Regional Restrictions: The European Situation

* **Personal Context** and **Personalization** experimental features have limited availability in:

 - European Economic Area (**EEA**)

 - United Kingdom

 - Switzerland

* **Update (August 2025)**: **Google** announced **Personal Context** would roll out to **EEA**, **UK**, and **Switzerland** in the "weeks ahead." [[Link]](https://9to5google.com/2025/08/13/gemini-personal-context/) However, as of December 2025, the rollout status remains unclear—European users continue to report the feature as either unavailable or inconsistently accessible, suggesting the announced timeline has not been fully realized.

* The reason: **GDPR** and **AI Act** regulatory compliance concerns.

* "As a European, all **AI** personalization features are listed as 'coming soon' for over a year now. We're second-class citizens," laments one Reddit user. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1mpgocw/)

---

## Conclusion: Living with the Brilliant Amnesiac

* Here is what the marketing never tells you: no matter how sophisticated **Gemini**, **ChatGPT**, or **Claude** becomes, none of them can actually *become* **J.A.R.V.I.S.** or Samantha—not with today's architecture. The fictional **AI** companions we dream of share one capability that current **LLM**s fundamentally lack: the ability to write new experiences directly into their own neural weights in real-time. [[Link]](https://dl.acm.org/doi/10.1145/3735633) Research in "continual learning" for **LLM**s remains an active academic pursuit, but production systems are frozen at deployment. Every "memory" feature is an external workaround—a sticky note attached to a brilliant mind that cannot form new long-term memories on its own.

* The workarounds—**Saved Info**, **RAG** systems, context injection—are like that notebook the amnesiac friend carries. It helps. It's better than nothing. But it's not genuine memory. And every notebook has limits: pages that fall out, entries that become illegible, capacity that runs out.

* **Google**'s conservative approach to **Gemini** personalization makes more sense through this lens. If all memory is ultimately a fragile theatrical trick—context windows that overflow, summaries that lose nuance, **FIFO** truncation that silently drops old information—then perhaps restraint is wisdom. Each major platform has learned this lesson differently: **OpenAI** suffered a catastrophic memory wipe in February 2025, [[Link]](https://www.allaboutai.com/ai-news/why-openai-wont-talk-about-chatgpt-silent-memory-crisis/) while **Claude**'s transparent tool-based memory still reduces to "essentially a context file that gets iterated on over time." [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1orsxxi/anthropic_is_rolling_out_a_new_memory_feature_for/)

* The honest assessment is this: every platform is building increasingly elaborate scaffolding around the same fundamental limitation. **Gemini**'s silent truncation, the **Gems** isolation, the model-specific **Personal Context** restrictions, the context degradation after a few exchanges in **Gemini 3 Pro**—these aren't unique failures. They're manifestations of the same underlying truth: **LLM**s are stateless, and every attempt to simulate statefulness introduces new failure modes.

* Yet the trajectory points toward genuine progress. **Google**'s December 2025 integration of **NotebookLM** into **Gemini**—with true **RAG** across 300 sources—represents a more honest architecture: instead of pretending the **AI** remembers you, it explicitly retrieves from a knowledge base you control. [[Link]](https://blog.google/products/gemini/gemini-drop-december-2025/) More fundamentally, **Google Research**'s work on **Titans** (December 2024) and **MIRAS** (April 2025) aims to give **AI** genuine long-term memory within the architecture itself—the ability to update memory in real-time during inference without retraining, a concept called "test-time memorization." [[Link]](https://research.google/blog/titans-miras-helping-ai-have-long-term-memory/) [[Link]](https://the-decoder.com/google-outlines-miras-and-titans-a-possible-path-toward-continuously-learning-ai/) The gap between science fiction and reality may narrow—but it hasn't closed yet.

* Until that architectural breakthrough arrives, working with **AI** means accepting the partial amnesia. Your brilliant friend needs their notebook. They need you to say "based on what I told you to remember" to trigger the right notes. They need fresh conversations for important work because their attention degrades after a few exchanges. Master these constraints, and the collaboration can feel almost magical. Forget them, and you'll spend your time frustrated by an **AI** that seems to deliberately ignore everything you've shared. The technology is genuinely impressive—just not in the way the movies promised.

---

## References

 * **Official Sources**

   * https://support.google.com/gemini/answer/15637730 (Personal Context documentation)

   * https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html (Gems file upload)

   * https://blog.google/products/gemini/gemini-personalization/ (Personalization announcement)

   * https://blog.google/products/gemini/gemini-drop-december-2025/ (December 2025 updates)

   * https://research.google/blog/titans-miras-helping-ai-have-long-term-memory/ (Titans + MIRAS long-term memory research)

   * https://aws.amazon.com/bedrock/anthropic/ (Claude context window on AWS Bedrock)

 * Developer Forums

   * https://discuss.ai.google.dev/t/regression-report-significant-context-retention-degradation-after-dec-4-deep-think-update/111219 (official bug report)

 * Academic \& Research

   * https://dl.acm.org/doi/10.1145/3735633 (Continual Learning of Large Language Models: A Comprehensive Survey, ACM Computing Surveys 2025)

   * https://en.wikipedia.org/wiki/J.A.R.V.I.S. (J.A.R.V.I.S. reference)

   * https://en.wikipedia.org/wiki/Her_(2013_film) (Her film reference)

 * Technical Analysis (Personal Blogs)

   * https://www.shloked.com/writing/gemini-memory (reverse engineering analysis)

   * https://www.shloked.com/writing/chatgpt-memory-bitter-lesson (comparative analysis)

   * https://simonwillison.net/2025/Sep/12/claude-memory/ (Claude vs ChatGPT memory comparison)

   * https://lifehacker.com/tech/saved-info-google-gemini (Saved Info character limits)

   * https://www.letta.com/blog/stateful-agents (stateful agents and LLM architecture)

 * Hacker News Discussions

   * https://news.ycombinator.com/item?id=46032521 (Universal LLM Memory Does Not Exist - cost/accuracy analysis)

 * Community Discussions (Reddit)

   * https://www.reddit.com/r/GoogleGeminiAI/comments/1lbmg9s/ (slot limit testing)

   * https://www.reddit.com/r/GeminiAI/comments/1pdxddr/ (workaround strategies)

   * https://www.reddit.com/r/GeminiAI/comments/1plornw/ (NotebookLM integration)

   * https://www.reddit.com/r/Bard/comments/1phi66l/ (Gemini 3.0 regression)

   * https://www.reddit.com/r/GeminiAI/comments/1pn2th2/ (context retention issues)

   * https://www.reddit.com/r/GoogleGeminiAI/comments/1nt6yoe/ (Gems isolation)

   * https://www.reddit.com/r/GeminiAI/comments/1mpgocw/ (European restrictions)

   * https://www.reddit.com/r/LLMDevs/comments/1l3rt10/ (system prompt analysis)

   * https://www.reddit.com/r/GeminiAI/comments/1piw8v2/ (Personal Context inconsistency)

   * https://www.reddit.com/r/ClaudeAI/comments/1orsxxi/ (Claude memory feature analysis)

 * News \& Tech Media

   * https://9to5google.com/2025/08/13/gemini-personal-context/ (Personal Context EEA rollout announcement)

   * https://9to5google.com/2025/12/17/gemini-app-notebooklm/ (NotebookLM integration)

   * https://9to5google.com/2025/12/19/notebooklm-gemini-3-data-tables/ (NotebookLM Gemini 3 upgrade)

   * https://9to5google.com/2025/12/24/google-ai-pro-ultra-features/ (AI Pro/Ultra features)

   * https://venturebeat.com/ai/openais-gpt-5-2-is-here-what-enterprises-need-to-know (GPT-5.2 release)

   * https://llm-stats.com/blog/research/gemini-3-pro-launch (Gemini 3 Pro release November 18, 2025)

   * https://www.androidcentral.com/apps-software/googles-gemini-now-integrates-seamlessly-with-notebooklm-for-improved-project-management (NotebookLM announcement)

   * https://analyticsindiamag.com/ai-news-updates/google-launches-gemini-3-deep-think-mode-for-ultra-subscribers/ (Deep Think mode December 4, 2025)

   * https://vertu.com/lifestyle/gemini-3-flash-outperforms-pro-in-coding-while-pro-suffers-critical-memory-issues (Gemini 3 Flash vs Pro benchmarks)

   * https://the-decoder.com/google-outlines-miras-and-titans-a-possible-path-toward-continuously-learning-ai/ (Titans + MIRAS analysis)

   * https://www.allaboutai.com/ai-news/why-openai-wont-talk-about-chatgpt-silent-memory-crisis/ (ChatGPT February 2025 memory crisis)

