# Source Grounding in the LLM Era: Why Claude Code's Power Users Choose Brave Search MCP

## TL;DR

* **Same engine, different controls**: **Claude Code**'s **WebSearch** and **Brave Search MCP** share the identical **Brave Search** backend—confirmed through `BraveSearchParams` discovery [[TechCrunch]](https://techcrunch.com/2025/03/21/anthropic-appears-to-be-using-brave-to-power-web-searches-for-its-claude-chatbot/) and 86.7% result correlation [[TryProfound]](https://www.tryprofound.com/blog/what-is-claude-web-search-explained)
* **The parameter gap**: Built-in **WebSearch** lacks `freshness` filter, `count` control, and `offset` pagination—**Brave MCP** offers all three plus 5 specialized search tools
* **The 125-character trap**: **WebFetch** summarizes pages through **Haiku 3.5** with a strict quote limit, potentially losing critical context [[Mikhail Shilkov]](https://mikhail.io/2025/10/claude-code-web-tools/)
* **Context overhead solved**: **MCP Tool Search** (**January 2026**) reduced overhead by up to 85%—the "**MCP** servers are too heavy" argument is now obsolete [[VentureBeat]](https://venturebeat.com/orchestration/claude-code-just-got-updated-with-one-of-the-most-requested-user-features)

---

## Introduction

* In early 2023, a **New York** lawyer submitted a legal brief to federal court citing six case precedents—complete with docket numbers, dates, and legal reasoning. Every citation looked impeccable. There was just one problem: none of those cases existed. [[Reuters]](https://www.reuters.com/legal/new-york-lawyers-sanctioned-using-fake-chatgpt-cases-legal-brief-2023-06-22/)

* The lawyer had used **ChatGPT** to research case law. The **AI** generated what appeared to be authoritative legal citations, but they were fabrications—hallucinations dressed in the costume of credibility. Judge P. Kevin Castel sanctioned both attorneys in **Mata v. Avianca**, marking a watershed moment in how the legal profession views **AI**-generated content. [[Forbes]](https://www.forbes.com/sites/mollybohannon/2023/06/08/lawyer-used-chatgpt-in-court-and-cited-fake-cases-a-judge-is-considering-sanctions/)

* **Mata v. Avianca** was the beginning, not the end. In **February 2024**, **Air Canada** was ordered by a **British Columbia** tribunal to honor a refund policy that never existed—because the airline's **AI** chatbot had fabricated it. A grieving passenger asked about bereavement fares; the chatbot confidently explained a retroactive discount policy that **Air Canada** had never offered. When the passenger demanded the promised refund, the airline argued its own chatbot was "a separate legal entity" not bound by company policy. The tribunal disagreed. [[BBC]](https://www.bbc.com/travel/article/20240222-air-canada-chatbot-misinformation-what-travellers-should-know)

* These incidents crystallize the fundamental limitation of **Large Language Models**. **LLMs** are, at their core, sophisticated pattern-matching engines. They predict the next most probable token based on training data. They do not verify. They do not fact-check. They generate text that *sounds* authoritative regardless of whether it *is* authoritative.

* The industry euphemistically calls this phenomenon "hallucination." A more accurate term would be "confident fabrication."

* This is where **source grounding** enters the picture—and why your choice of search tools inside **Claude Code** matters far more than you might think.

---

## What Is Source Grounding and Why Does It Matter?

* **Source grounding** is the practice of anchoring an **LLM**'s responses to verifiable external information sources. Think of it as dropping an anchor to prevent a ship from drifting into open ocean. Without grounding, the model's responses float freely, untethered from reality.

* The metaphor is precise: an ungrounded **LLM** is a ship without an anchor, drifting wherever the currents of probabilistic inference take it.

| State | Metaphor | Result |
|-------|----------|--------|
| **LLM** alone | Anchorless vessel | Hallucination risk |
| **LLM** + search grounding | Anchored vessel | Factual responses |

* **Google**'s **Gemini** introduced "Grounding with Google Search" in 2024, allowing the model to fetch real-time web results before generating responses. [[Google Developers Blog]](https://developers.googleblog.com/en/gemini-api-and-ai-studio-now-offer-grounding-with-google-search/) **Anthropic** followed suit, integrating web search capabilities into **Claude**. Both companies recognize the same fundamental truth: models need external anchors to stay accurate.

* As **AWS** documentation explains: "By grounding the generation process in factual information from reliable sources, **RAG** can reduce the likelihood of hallucinating incorrect or made-up content, thereby enhancing the factual accuracy and reliability of the generated responses." [[AWS]](https://aws.amazon.com/blogs/machine-learning/reducing-hallucinations-in-large-language-models-with-custom-intervention-using-amazon-bedrock-agents/)

* The stakes are higher in 2026 than ever before. **Claude Opus 4.5**'s training data cutoff is **August 2025**. [[Anthropic Support]](https://support.claude.com/en/articles/8114494-how-up-to-date-is-claude-s-training-data) As I write this on **January 28, 2026**, there's at least a five-month gap in the model's knowledge. Framework updates, **API** changes, security vulnerabilities, acquisitions—all may be invisible to the model unless it can search the web.

* This brings us to the core question: **Claude Code** offers two paths to web search—its built-in **WebSearch** tool and the **Brave Search MCP**. Both use the same search engine under the hood. So why does the choice matter?

---

## Same Engine, Different Controls

* In **March 2025**, software engineer **Antonio Zugaldia** discovered that **Anthropic** had added "**Brave Search**" to its subprocessor list. Programmer **Simon Willison** confirmed this by finding that search results in **Claude** and **Brave** returned identical citations, and discovered a `BraveSearchParams` parameter in **Claude**'s web search function. [[TechCrunch]](https://techcrunch.com/2025/03/21/anthropic-appears-to-be-using-brave-to-power-web-searches-for-its-claude-chatbot/) Subsequent independent analysis by **TryProfound** quantified this overlap at 86.7% (13 out of 15 results matching). [[TryProfound]](https://www.tryprofound.com/blog/what-is-claude-web-search-explained)

* **TechCrunch** independently confirmed the finding:

> "Anthropic appears to be using Brave to power web searches for its Claude chatbot. Claude's web search function contains a 'BraveSearchParams' parameter."
> — Kyle Wiggers, TechCrunch [[Link]](https://techcrunch.com/2025/03/21/anthropic-appears-to-be-using-brave-to-power-web-searches-for-its-claude-chatbot/)

* The conclusion is unambiguous: **Claude Code**'s built-in **WebSearch** and the **Brave Search MCP** share the same **Brave Search** backend. Search quality is identical at the engine level.

* So why do power users bother configuring **Brave Search MCP** separately?

* Consider a navigation analogy: both tools use the same satellite data, but one is a basic car **GPS** showing "turn left in 500m" while the other is an aircraft instrument panel displaying altitude, heading, wind speed, fuel consumption, and weather radar.

* Same data source, radically different precision. The satellite being identical doesn't make the instruments identical.

---

## Feature Comparison: The Parameters That Make the Difference

### Claude Code Built-in WebSearch: Simplicity at a Cost

* **Claude Code**'s **WebSearch** tool, as documented in its system prompt and **Anthropic**'s official documentation, accepts remarkably few parameters: [[Claude Docs]](https://docs.claude.com/en/docs/agents-and-tools/tool-use/web-search-tool)

```typescript
interface WebSearchTool {
  query: string;              // Required, minimum 2 characters
  allowed_domains?: string[]; // Optional domain allowlist
  blocked_domains?: string[]; // Optional domain blocklist
  user_location?: {           // Optional location for localized results
    type: "approximate";
    city?: string;
    region?: string;
    country?: string;
    timezone?: string;
  };
}
```

* That's it.

| Parameter | Description | Supported |
|-----------|-------------|-----------|
| `query` | Search query | ✅ |
| `allowed_domains` | Include only specific domains | ✅ |
| `blocked_domains` | Exclude specific domains | ✅ |
| `user_location` | Localize search results (city/region/country) | ✅ |
| `freshness` | Time filter (24h/7d/30d/1y) | ❌ |
| `count` | Number of results | ❌ |
| `offset` | Pagination | ❌ |

* Want to find "**LLM** papers published in Q1 2024"? You cannot specify a date range—the parameter doesn't exist.

* Need "**AI** news from the last 24 hours"? You can try adding "today" to your query string, but precise time filtering is not guaranteed.

* Require 20 search results instead of the default? Not configurable.

* Need the second page of results? Pagination is unsupported.

### Brave Search MCP: Precision Control

* The **Brave Search MCP**, by contrast, exposes the full power of the **Brave Search API** through five specialized tools: [[Brave Search API]](https://brave.com/search/api/)

| Tool | Purpose | Key Parameters |
|------|---------|----------------|
| `brave_web_search` | General web search | `freshness`, `count` (1-20), `offset` (max 9) |
| `brave_news_search` | News-specific search | `freshness` (pd/pw/pm/py) |
| `brave_image_search` | Image search | `count` (1-20) |
| `brave_video_search` | Video search | `freshness` |
| `brave_local_search` | Local business search | Location-based |

* The `freshness` parameter alone demonstrates the gap:

```json
{
  "pd": "Past Day (24 hours)",
  "pw": "Past Week (7 days)",
  "pm": "Past Month (31 days)",
  "py": "Past Year (365 days)",
  "YYYY-MM-DDtoYYYY-MM-DD": "Custom date range"
}
```

* To search for "**LLM** trends from January through June 2024":

```json
{
  "query": "LLM trends",
  "freshness": "2024-01-01to2024-06-30"
}
```

* This query is impossible with built-in **WebSearch**.

### Real-World Scenario Comparison

| Scenario | Built-in WebSearch | Brave Search MCP |
|----------|-------------------|------------------|
| "AI news from past 24 hours" | ⚠️ "AI news today" query (imprecise) | ✅ `brave_news_search(freshness="pd")` |
| "Tech trends from H1 2024" | ❌ Impossible | ✅ Custom date range supported |
| "Restaurants near Gangnam Station" | ⚠️ Generic web results | ✅ `brave_local_search` with reviews/hours |
| "React 18 tutorial videos" | ❌ Not supported | ✅ `brave_video_search` |
| "Need 20 search results" | ❌ Fixed count | ✅ `count: 20` |
| "Next page of results" | ❌ No pagination | ✅ `offset` parameter |

---

## The Hidden Bottleneck: The 125-Character Trap

### Discovery #1: The WebFetch 125-Character Constraint

* **Claude Code**'s web functionality operates in two stages:

| Tool | Function | Output |
|------|----------|--------|
| **WebSearch** | Finds URLs matching query | URL list + titles |
| **WebFetch** | Analyzes specific URL content | **Haiku 3.5** summary with 125-char quotes |

* Technical analyst **Mikhail Shilkov** documented this architecture:

> "WebFetch sends page content to Haiku 3.5 for summarization. It runs with an empty system prompt and enforces a strict 125-character maximum for quotes from any source document."
> — **Mikhail Shilkov** [[Link]](https://mikhail.io/2025/10/claude-code-web-tools/)

* **125 characters**. Shorter than a tweet. This entire sentence you're reading right now is already 89 characters—add one **URL** and you've hit the limit.

* What does this mean in practice? Consider a **Kubernetes** Pod specification from official documentation. A typical explanation runs 300+ characters: "A Pod is the smallest deployable unit in Kubernetes, representing a group of one or more containers with shared storage and network resources, and a specification for how to run the containers." The 125-character limit truncates this to: "A Pod is the smallest deployable unit in Kubernetes, representing a group of one or more containers"—losing the critical details about shared storage and network namespaces that define Pod behavior.

* For deep research requiring full context from source pages, this summarization layer can strip critical details. **Brave Search MCP** returns search results directly without this intermediate summarization step.

### Discovery #2: MCP Tool Search Changes the Equation

* "But doesn't running another **MCP** server bloat my context?" A fair concern—until mid-**January 2026**.

* **Anthropic** released **MCP Tool Search**, addressing one of **Claude Code**'s most-requested features:

> "Claude Code detects when your MCP tool descriptions would use more than 10% of context. When triggered, tools are loaded via search instead of preloaded."
> — **VentureBeat** [[Link]](https://venturebeat.com/orchestration/claude-code-just-got-updated-with-one-of-the-most-requested-user-features)

* The impact (based on **Anthropic** engineering and user reports):
  - **Up to 85% reduction** in token overhead according to **Anthropic**'s official benchmarks [[Cyrus]](https://www.atcyrus.com/stories/mcp-tool-search-claude-code-context-pollution-guide)
  - **66,000 tokens → ~8,500 tokens** in real-world scenarios [[Medium]](https://medium.com/@joe.njenga/claude-code-just-cut-mcp-context-bloat-by-46-9-51k-tokens-down-to-8-5k-with-new-tool-search-ddf9e905f734) (individual developer experience)
  - Up to **95% context usage reduction** when running multiple **MCP** servers [[Personal Blog]](https://juanjofuchs.github.io/ai-development/2026/01/20/maximizing-claude-code-subscription.html) (individual developer experience)

* The "**MCP** servers are too heavy" argument is now obsolete. The context overhead concern for running **Brave Search MCP** alongside other **MCP** servers has been dramatically reduced.

### Discovery #3: The Token Efficiency Question

* Community discussions highlight the nuances between both approaches:

> "Something I didn't realise at first with Claude's built in web search is there's two capabilities. Web_search and web_fetch. The first only gets snippet results from the search and the url, not the full web page contents. The second, can retrieve the full page contents, but only if given a full url either from a web_search result or if given the url directly from the user."
> — u/dshipp, r/ClaudeAI [[Reddit]](https://www.reddit.com/r/ClaudeAI/comments/1l1g21l/)

* This two-step architecture has implications for token efficiency. The logic:

- **Built-in WebSearch**: **Claude** generates search queries and processes results—token consumption throughout
- **Brave MCP**: Search executes via external **API**—potentially lower token overhead

* While **WebSearch** is "free" for **Max** subscribers, token limits still exist. **January 2026** saw widespread user complaints about hitting limits faster:

> "Since 1st Jan I have been hitting limits twice as fast with less code generation and far less token consumption."
> — u/Tasty-Specific-5224, r/ClaudeCode [[Reddit]](https://www.reddit.com/r/ClaudeCode/comments/1q2prvg/anthropic_has_secretly_halved_the_usage_in_max/)

* When "free" searches accelerate your path to rate limits, external **API** calls may offer practical advantages.

### Discovery #4: The Expanding MCP Ecosystem

* The search **MCP** ecosystem has expanded significantly in **January 2026**, signaling a broader trend: developers are choosing external tools over built-in defaults.

* **Kindly MCP** emerged as a specialized option:

> "Standard search MCPs usually fail here. They either return insufficient snippets or dump raw HTML full of navigation bars and ads that confuse the LLM and waste context window. Kindly solves this by being smarter about retrieval, not just search."
> — u/Quirky_Category5725, r/LocalLLaMA [[Reddit]](https://www.reddit.com/r/LocalLLaMA/comments/1q6khuh/)

* **Google AI Mode MCP** gained traction for token efficiency:

> "You ask Claude a question → Claude queries Google AI Mode → Google searches and synthesizes dozens of sources → Claude gets one clean Markdown answer with inline citations → minimal token usage."
> — u/PleasePrompto, r/ClaudeAI [[Reddit]](https://www.reddit.com/r/ClaudeAI/comments/1q6mmwy/)

* The market is evolving beyond "search" toward integrated "search + retrieval + synthesis" pipelines. **Brave Search MCP** represents this shift: external tools offering precision that built-in defaults cannot match.

---

## Making the Choice: When Each Tool Shines

### Pricing Comparison

| Scenario | Built-in WebSearch | Brave MCP (Base AI) |
|----------|-------------------|------------------|
| **Max 5x** subscriber ($100/month), 1,000 searches/month | $0 (included) | $5 |
| **Max 5x** subscriber ($100/month), 10,000 searches/month | $0 (included) | $50 |
| **Anthropic API** direct, 1,000 searches/month | $10 | $5 |

* Sources: **Anthropic** Pricing [[Link]](https://www.anthropic.com/pricing) ($10/1K searches for **API** web search tool), **Brave Search API** [[Link]](https://brave.com/search/api/) ($5/1K requests for Base AI tier)

* On pure cost, **Max** subscribers get **WebSearch** for free. If that were the entire story, this article would end here.

* But cost isn't everything—and neither is capability. **Brave Search MCP** carries its own tradeoffs: **API** key management adds security responsibility, monthly costs accumulate for heavy users, and initial **JSON** configuration isn't trivial for non-developers. These friction costs are real.

* There's also a more fundamental consideration: **Brave Search** itself may not match **Google**'s quality for certain queries. Community feedback consistently notes this gap for technical searches:

> "Especially when looking for results regarding Linux commands/config, Brave has been noticeably worse than Google. I had to google a few things because I literally did not find a solution to my problem on Brave."
> — u/Beosar, r/degoogle [[Reddit]](https://www.reddit.com/r/degoogle/comments/1jlbwsg/)

* The **Brave Search MCP** gives you more control over a search engine that may return less relevant results for specialized technical queries. More parameters over mediocre results is still mediocre results with better filtering. For highly technical research, consider whether **Brave**'s index covers your domain adequately.

* **Brave Search** is particularly well-suited for privacy-focused queries and general web content. However, for highly specialized technical domains—especially **Linux** system administration, niche programming frameworks, or academic research—users may find **Google**'s index more comprehensive. This is a search engine quality consideration, not an **MCP** vs **WebSearch** distinction—both tools use **Brave**'s index.

* The question isn't which tool is "better." It's which tradeoffs align with your workflow.

### When Brave Search MCP Is the Right Choice

| Situation | Reason |
|-----------|--------|
| Date range filtering required | `freshness` parameter (built-in unsupported) |
| News/image/video/local search | 5 specialized tools (built-in offers web only) |
| Result count control needed | `count` parameter (built-in is fixed) |
| Pagination required | `offset` parameter (built-in unsupported). Note: Brave API `offset` max is 9, allowing up to 200 results total |
| Using **AWS Bedrock** | Built-in **WebSearch** unsupported on Bedrock |
| Using **Google Vertex AI** | Built-in **WebSearch** supported, but requires beta header (`anthropic-beta: web-search-2025-03-05`) [[Google Cloud]](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/partner-models/claude/web-search) |
| Token limit pressure | External **API** may reduce token overhead |

### Quick Decision Guide

| Your Situation | Recommended Tool | Why |
|----------------|------------------|-----|
| Casual information lookup, **Max** subscriber | **WebSearch** | Free, zero setup |
| Date range filtering required | **Brave MCP** | `freshness` parameter |
| News/image/video/local search | **Brave MCP** | 5 specialized tools |
| **AWS Bedrock** backend | **Brave MCP** | **WebSearch** unsupported on **Bedrock** |
| **Google Vertex AI** backend | Either works | **WebSearch** supported with beta header |
| Token limit pressure | **Brave MCP** | External **API** reduces overhead |
| Hate managing **API** keys | **WebSearch** | Zero configuration |
| Highly specialized technical queries | Consider alternatives | **Brave** index may lack depth |

### The Anchor Metaphor: Choosing Your Grounding Tool

* **Source grounding** is the anchor that keeps **LLMs** tethered to reality. But anchors come in varieties—and selecting the right one depends on the waters you're navigating.

* **Built-in WebSearch** is the folding anchor from a convenience store. Light, requires no setup, adequate for calm waters. For quick lookups where date precision doesn't matter, it's the sensible choice.

* **Brave Search MCP** is the fixed anchor professional vessels use. Installation requires effort (**API** key + credit card registration). It has weight (separate configuration). But when storms hit—complex research, precise date filtering, multi-format searches—it holds steady where the folding anchor drags.

* The choice isn't about which tool is "better." It's about matching your grounding tool to your research depth. For casual queries, the convenience anchor works. For systematic research, fact-checking, time-sensitive analysis, the precision anchor pays for itself.

* **The cost of hallucination always exceeds the cost of proper grounding.**

### Immediate Action: Setup in Two Steps

* If you've decided **Brave Search MCP** fits your workflow, here's how to set it up. First, install the **MCP** server with a single command:

```bash
# Install Brave Search MCP Server
$ claude mcp add-json --scope user brave-search '{"command":"npx","args":["-y","brave-search-mcp"],"env":{"BRAVE_API_KEY":"{your-brave-api-key}"}}'
Added stdio MCP server brave-search to user config
```

* Replace `{your-brave-api-key}` with your actual **Brave Search API** key. You can obtain one from the **Brave Search API** portal. [[Brave Search API]](https://brave.com/search/api/)

* Second, enforce **Brave Search MCP** as your default search tool across all sessions. Add this single line to your `CLAUDE.md` file:

```markdown
**WEB SEARCH:** NEVER use built-in WebSearch tool. MUST use Brave Search MCP exclusively for ALL web searches.
```

* Two commands; one permanent configuration. Every future search is now grounded with full parameter control—`freshness`, `count`, `offset`, and five specialized search tools at your disposal.

---

## Conclusion: Source Grounding as a Design Decision

* The choice between **WebSearch** and **Brave Search MCP** isn't about "better" versus "worse." It's about matching your grounding tool to your research requirements—a design decision that shapes every subsequent query.

* For someone asking "tell me about **AI** news," built-in **WebSearch** delivers results without configuration overhead. But for systematic research—"multimodal **LLMs** by benchmark score announced in Q3 2024"—date range filters, result count control, and pagination transform from nice-to-have into essential. The tool doesn't make questions more precise; it enables you to ask precise questions in the first place.

* This shift in framing matters. Information retrieval in the **LLM** era is no longer "type a query and receive results." It's designing what time period, what format, how many results, in what order you need information. The freedom of that design determines the depth of grounding you can achieve.

* Remember the lawyer in **Mata v. Avianca**? Six fabricated case citations led to sanctions, career damage, and public humiliation. Proper grounding could have prevented that outcome in minutes. The stakes aren't theoretical—they're professional, legal, and reputational. The choice between these tools is ultimately the choice between accepting confident fabrication as a background risk versus demanding verifiable grounding as a standard practice.

* **Anthropic** built **WebSearch** for accessibility: zero setup, zero cost for **Max** subscribers, adequate for most casual use cases. The **Brave Search MCP** exists for users who've outgrown those constraints—developers building research pipelines, journalists fact-checking sources, analysts requiring date-bounded data, anyone whose work demands precision over convenience.

* In 2026, the infrastructure for grounding **LLM** responses in verifiable reality is mature. Both tools use the same search engine. The difference lies in how much control you have over how that engine is queried. For many users, built-in **WebSearch** is the right choice—simple, free, sufficient. For power users who need the full parameter surface, **Brave Search MCP** is worth the setup cost. Choose the tool that matches your depth.

---

## References

* **Official Documentation**
  * [https://www.anthropic.com/pricing](https://www.anthropic.com/pricing) — **Anthropic** pricing tiers and **Max** subscription details
  * [https://docs.claude.com/en/docs/agents-and-tools/tool-use/web-search-tool](https://docs.claude.com/en/docs/agents-and-tools/tool-use/web-search-tool) — **Claude Code WebSearch** official specification
  * [https://brave.com/search/api/](https://brave.com/search/api/) — **Brave Search API** pricing and parameters
  * [https://docs.cloud.google.com/vertex-ai/generative-ai/docs/partner-models/claude/web-search](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/partner-models/claude/web-search) — **Google Vertex AI** web search with **Claude** (beta header requirement)
* **Technical Analysis**
  * [https://mikhail.io/2025/10/claude-code-web-tools/](https://mikhail.io/2025/10/claude-code-web-tools/) — **WebFetch/WebSearch** internals including 125-char quote limit
  * [https://techcrunch.com/2025/03/21/anthropic-appears-to-be-using-brave-to-power-web-searches-for-its-claude-chatbot/](https://techcrunch.com/2025/03/21/anthropic-appears-to-be-using-brave-to-power-web-searches-for-its-claude-chatbot/) — **Brave** backend confirmation
  * [https://venturebeat.com/orchestration/claude-code-just-got-updated-with-one-of-the-most-requested-user-features](https://venturebeat.com/orchestration/claude-code-just-got-updated-with-one-of-the-most-requested-user-features) — **MCP Tool Search** context reduction announcement
  * [https://www.atcyrus.com/stories/mcp-tool-search-claude-code-context-pollution-guide](https://www.atcyrus.com/stories/mcp-tool-search-claude-code-context-pollution-guide) — **MCP Tool Search** detailed analysis with **Anthropic** benchmark data
* **LLM Grounding & RAG**
  * [https://aws.amazon.com/blogs/machine-learning/reducing-hallucinations-in-large-language-models-with-custom-intervention-using-amazon-bedrock-agents/](https://aws.amazon.com/blogs/machine-learning/reducing-hallucinations-in-large-language-models-with-custom-intervention-using-amazon-bedrock-agents/) — **AWS** hallucination reduction via **RAG**
  * [https://developers.googleblog.com/en/gemini-api-and-ai-studio-now-offer-grounding-with-google-search/](https://developers.googleblog.com/en/gemini-api-and-ai-studio-now-offer-grounding-with-google-search/) — **Google Gemini** grounding feature
* **Legal Case Documentation**
  * [https://www.reuters.com/legal/new-york-lawyers-sanctioned-using-fake-chatgpt-cases-legal-brief-2023-06-22/](https://www.reuters.com/legal/new-york-lawyers-sanctioned-using-fake-chatgpt-cases-legal-brief-2023-06-22/) — **Mata v. Avianca** sanctions ruling
  * [https://www.forbes.com/sites/mollybohannon/2023/06/08/lawyer-used-chatgpt-in-court-and-cited-fake-cases-a-judge-is-considering-sanctions/](https://www.forbes.com/sites/mollybohannon/2023/06/08/lawyer-used-chatgpt-in-court-and-cited-fake-cases-a-judge-is-considering-sanctions/) — **Mata v. Avianca** background
  * [https://www.bbc.com/travel/article/20240222-air-canada-chatbot-misinformation-what-travellers-should-know](https://www.bbc.com/travel/article/20240222-air-canada-chatbot-misinformation-what-travellers-should-know) — **Air Canada** chatbot tribunal ruling
* **Community Discussions** (user-reported experiences)
  * [https://www.reddit.com/r/ClaudeCode/comments/1q2prvg/](https://www.reddit.com/r/ClaudeCode/comments/1q2prvg/) — Token limit complaints (**January 2026**)
  * [https://www.reddit.com/r/ClaudeAI/comments/1l1g21l/](https://www.reddit.com/r/ClaudeAI/comments/1l1g21l/) — **WebSearch** vs **MCP** tool discussion
  * [https://www.reddit.com/r/LocalLLaMA/comments/1q6khuh/](https://www.reddit.com/r/LocalLLaMA/comments/1q6khuh/) — **Kindly MCP** search retrieval
  * [https://www.reddit.com/r/ClaudeAI/comments/1q6mmwy/](https://www.reddit.com/r/ClaudeAI/comments/1q6mmwy/) — **Google AI Mode MCP** discussion
  * [https://www.reddit.com/r/degoogle/comments/1jlbwsg/](https://www.reddit.com/r/degoogle/comments/1jlbwsg/) — **Brave** vs **Google** search quality comparison
  * [https://www.tryprofound.com/blog/what-is-claude-web-search-explained](https://www.tryprofound.com/blog/what-is-claude-web-search-explained) — 86.7% **Brave** correlation analysis