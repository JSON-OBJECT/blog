# Gemini Gems: 실시간 동기화되는 지식 베이스로 나만의 AI 전문가 군단 구축하기

## 핵심 요약

* **Gemini Gems**는 시스템 프롬프트와 지식 베이스(파일 10개 × 100MB)를 결합한다. 핵심 차별점은 **Google Docs/Sheets**와 실시간 동기화다
* **2025년 12월 업데이트**: **NotebookLM** 노트북(소스 300개)을 Gem 지식 베이스에 직접 연결할 수 있고, `@Google Keep`으로 **Saved Info** 접근 제한을 우회할 수 있다
* **치명적 한계**: Gem은 문서를 읽을 수만 있고 쓸 수는 없다. 5~10회 프롬프트 이후 지식 베이스를 무시하는 "Gem Drift" 현상도 존재한다
* **3계층 아키텍처**: NotebookLM(전문 지식) + Google Docs/Sheets(동적 데이터) + @Google Keep(개인 맥락) = 하이엔드 컨설턴트 경험

---

## 서론

* 자신을 12명의 전문가로 복제할 수 있다면 어떨까? 각 전문가는 특정 업무에 최적화되어 있고, 실시간 업데이트되는 고유 지식 베이스를 갖추고 있다.

* **Google**의 **Gemini Gems**가 바로 이 약속을 실현한다. 페르소나를 정의하는 시스템 프롬프트와 참조 문서를 결합해 작업별 맞춤형 챗봇을 만든다. 매 세션마다 파일을 다시 업로드할 필요 없이 내 데이터를 기억한다. **Google** 공식 설명은 다음과 같다: "Gem을 특정 주제 전문가로 커스터마이즈하거나 사용자의 목표에 맞게 조정할 수 있습니다. Gem에 이름을 붙이고 지침을 작성한 다음, 원할 때마다 대화를 시작하면 됩니다." [[Link]](https://blog.google/products/gemini/google-gemini-update-august-2024/)

* 개념 자체는 단순하다. 페르소나를 정의하고("당신은 우리 회사 코딩 표준을 따르는 시니어 **Python** 개발자입니다"), 관련 문서(스타일 가이드, **API** 문서, 프로젝트 명세)를 첨부하면 Gem은 지속적으로 동작하는 전문가가 된다. 일반 채팅 세션의 휘발성 컨텍스트와 달리, Gem은 대화를 넘어 정체성과 지식을 유지한다. 한 파워유저의 표현이다:

> "Gemini는 100만 토큰의 초대형 컨텍스트 윈도우를 갖고 있어서 방대한 데이터를 처리할 수 있다... 이 메모리 카드 문서에 수십만 단어 분량의 지식을 넣어두면 Gemini가 원하는 모든 정보를 기억하게 할 수 있다."
> — u/RickThiccems, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

* **ChatGPT**의 **Custom GPT**나 **클로드 프로젝트**와 **Gemini Gems**를 구분 짓는 결정적 차이가 있다: Gem에 첨부된 **Google Docs**와 **Google Sheets**는 실시간 동기화된다. **Google Drive**에서 참조 문서를 수정하면 Gem이 즉시 변경사항을 인식한다. 재업로드가 필요 없다. [[Link]](https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html)

* 이 글에서는 **Gemini Gems**의 실체, 내부 작동 방식, 실질적 한계, 그리고 가장 중요하게—반복적인 업무를 고성능 워크플로우로 전환하는 전문 Gem 시스템 설계 방법을 해부한다.

---

## Gemini Gems의 실체: 마케팅을 넘어서

* Gem의 본질은 세 가지 구성요소를 저장한 설정이다: 시스템 프롬프트("지침"이라 부름), 첨부 파일("지식 베이스"), 그리고 선택적으로 커스텀 이름과 설명. [[Link]](https://9to5google.com/2024/11/12/gemini-advanced-gems-files/) **Google** 공식 가이드는 이렇게 설명한다: "Gems로 전문가 팀을 만들어 까다로운 프로젝트를 검토하거나, 다가오는 이벤트 아이디어를 브레인스토밍하거나, 소셜 미디어 포스트에 완벽한 캡션을 작성할 수 있습니다." [[Link]](https://blog.google/products/gemini/google-gemini-update-august-2024/)

* 시스템 프롬프트는 Gem의 페르소나, 행동 제약, 출력 형식 요구사항을 정의한다. 법률 문서 검토자, 언어 튜터, 특정 컨벤션을 따르는 코드 리뷰어 등 전문화된 역할을 지시하는 곳이다. **Google** 제품팀의 조언: "Gem 지침 작성이 막막하거나 더 개선하고 싶다면 Gemini에게 도움을 요청하세요. 텍스트 박스 하단의 마법봉 아이콘을 사용하면 Gemini가 지침을 다시 작성하고 확장해 줍니다." [[Link]](https://blog.google/products/gemini/google-gems-tips/)

* 지식 베이스는 최대 10개 파일을 수용하며, 각 파일 최대 크기는 100MB다. 지원 형식: **TXT**, **DOC**, **DOCX**, **PDF**, **RTF**, **HWP**, **HWPX**, **Google Docs**, **XLS**, **XLSX**, **CSV**, **TSV**, **Google Sheets**. [[Link]](https://techwiser.com/google-gemini-gems-now-supports-file-uploads-to-its-knowledge/)

### 실시간 동기화의 차별점

* Gem을 경쟁 서비스와 차별화하는 핵심 기능이다:

| 파일 유형 | 실시간 동기화 | 업데이트 방식 |
|-----------|--------------|---------------|
| **Google Docs** | ✓ 자동 | **Drive**에서 편집 → Gem이 즉시 반영 |
| **Google Sheets** | ✓ 자동 | **Drive**에서 편집 → Gem이 즉시 반영 |
| **PDF** | ✗ | 변경 후 재업로드 필요 |
| **DOCX/TXT/기타** | ✗ | 변경 후 재업로드 필요 |

* 이 구분은 결정적이다. 시간이 지나며 진화하는 문서—프로젝트 상태 추적표, 클라이언트 정보 시트, 살아있는 스타일 가이드—를 다룬다면 **Google Docs**와 **Sheets**가 유일하게 합리적인 선택이다. [[Link]](https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html)

### Gems와 Saved Info의 차이

* **Gemini**는 **Saved Info**라는 또 다른 개인화 기능을 제공한다—모든 대화에 걸쳐 유지되는 텍스트 스니펫이다. 두 시스템을 혼동하기 쉽지만, 근본적으로 다른 아키텍처로 동작한다:

| 항목 | Saved Info | Gems |
|------|------------|------|
| 범위 | 전역 (모든 대화) | Gem별 한정 |
| 데이터 유형 | 텍스트 스니펫 (각 ~1,500자) | 파일 (10개 × 100MB) |
| 토큰 예산 | ~2,500 토큰 (커뮤니티 추정) [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1lbmg9s/) | 1M 토큰 컨텍스트 윈도우 내 |
| 파일 지원 | ✗ | ✓ |
| 접근 패턴 | 시스템 프롬프트에 자동 주입 | 지식 베이스 참조로 접근 |

* 한 파워유저가 **Saved Info**의 숨겨진 한계를 발견했다:

> "Saved Info에 74개 슬롯이 있다. 전부 1500자 한계를 쓰진 않지만 상당수가 그렇다. 숨겨진 한계가 있다: 특정 시점을 넘으면 AI가 오래된 지침을 '잊는다'. 버그가 아니라 조용한 잘림 현상이다."
> — u/i31ackJack, r/GoogleGeminiAI [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1lbmg9s/)

* 커뮤니티가 발견한 결정적 사실: **Gem은 Saved Info를 상속하지 않는다**. 세심하게 관리해온 개인 정보, 선호도, 맥락—**Saved Info**에 저장된 모든 것이 Gem에게는 보이지 않는다. Gem은 오직 자체 지침과 지식 베이스만으로 동작한다. 한 사용자의 확인:

> "테스트해봤는데 Gem이 'Saved Info'에 접근하지 못했다... Gem은 정말로 그 Gem을 설계한 방식에 기반한 폐쇄적인 환경인 것 같다."
> — u/no1ucare, r/Bard [[Link]](https://www.reddit.com/r/Bard/comments/1gux1v2/)

---

## 효과적인 Gem 아키텍처: 세 가지 핵심 요소

* **Gemini** 커뮤니티의 파워유저들이 프로덕션급 Gem 구축에 수렴한 3요소 아키텍처가 있다:

### 요소 1: 시스템 프롬프트 (페르소나)

* 시스템 프롬프트는 Gem이 누구인지를 정의한다. 단순한 역할 지정이 아니다—행동을 제약하고, 출력 형식을 명시하고, 교전 규칙을 확립하는 것이다.

* 커뮤니티의 정교한 예시:

```
You are an expert Dungeon Master (DM) assistant specifically for
the Dungeons & Dragons 5th Edition adventure, 'Icewind Dale:
Rime of the Frostmaiden.'

When answering rule questions, cite the relevant section or
page number from the D&D 2024 rules or the Rime of the
Frostmaiden book if possible.

Do not begin by validating the user's ideas. Be authentic; maintain
independence and actively critically evaluate what is said.

Don't ever be groundlessly sycophantic; do not flatter the user.
```

* "반(反)아첨" 지침이 주목할 만하다—**LLM**은 과도한 동의 경향이 잘 알려져 있으며, 시스템 프롬프트에 명시적 대책을 넣으면 유용한 비판적 피드백을 유지하는 데 도움이 된다. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/) **Google** 제품 리드 Deven Tokuno의 추천: "맞춤형 응답을 위해 구체적인 맥락과 스타일을 제공하세요. 창의적으로 갈 수 있습니다—예를 들어, 티라노사우루스 캐릭터로 아이 생일 파티를 계획하는 공룡 생일 플래너를 만들어 보세요." [[Link]](https://blog.google/products/gemini/google-gems-tips/)

### 💡 팁: 크로스 플랫폼 프롬프트 재사용

* 다른 **AI** 도구(**ChatGPT Custom Instructions**, **클로드 프로젝트**, **Claude Code Skills** 등)의 시스템 프롬프트를 **Gemini Gems**로 최소한의 수정만으로 이식할 수 있다. 핵심 행동 지침—페르소나 정의, 포맷팅 요구사항, 응답 제약—은 플랫폼 간에 매끄럽게 이전된다. 이식 전에 플랫폼 고유 도구 호출만 제거하면 된다.

### 요소 2: 지식 베이스 (전문성)

* 지식 베이스는 Gem의 도메인 전문성이 살아있는 곳이다. 행동을 정의하는 시스템 프롬프트와 달리, 지식 베이스는 응답의 사실적 기반을 제공한다.

* 지식 베이스 구성 모범 사례:

| 전략 | 설명 | 용도 |
|------|------|------|
| **JSONL 형식** | JSON Lines 형식의 구조화 데이터 | Gem이 구조화된 정보를 파싱해야 할 때 |
| **마크다운** | 네이티브 마크다운 문서 | 기술 문서, 스타일 가이드 |
| **분할 문서** | 대형 문서를 장/섹션별로 분할 | 책, 종합 매뉴얼 |
| **Google Sheets** | 실시간 업데이트되는 테이블 데이터 | 클라이언트 목록, 프로젝트 추적표, 가격표 |

* 한 파워유저의 발견: "첨부 문서로 **JSONL** 형태의 구조화 데이터를 포함시키는 해킹을 쓴다. 아주 잘 작동한다. 문서가 네이티브 마크다운이면 더 좋다—그렇지 않으면 **gDocs** 등을 받자마자 마크다운으로 변환하려 하기 때문이다." [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

### 요소 3: 동적 데이터 (살아있는 기억)

* 가장 정교한 Gem 아키텍처가 등장하는 영역이다. 파워유저들이 개발한 "메모리 카드" 전략—대화 간 지속적 기억 역할을 하는 **Google 문서**다.

* 워크플로우:

| 단계 | 행동 | 결과 |
|------|------|------|
| **1** | "메모리 카드"라는 **Google 문서** 생성 | **Drive**에 빈 문서 |
| **2** | Gem 지식 베이스에 추가 | Gem이 이제 문서를 읽을 수 있음 |
| **3** | "대화 시작 시 메모리 카드를 검토하라" 지침 추가 | Gem이 세션 히스토리를 인식함 |
| **4** | "대화 종료 시 기억 업데이트 요약을 생성하라" 지침 추가 | Gem이 수동 복사용 텍스트 생성 |
| **5** | 요약을 메모리 카드에 수동 붙여넣기 | 다음 대화가 맥락을 상속함 |

* 중요한 한계: **Gem은 Google Docs에 쓸 수 없다**. Gem은 업데이트 내용을 생성할 수 있지만, 메모리 카드 문서에 복사해 붙여넣는 건 사용자 몫이다. 반자동 시스템이지, 완전 자동화가 아니다. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/) 한 헌신적인 사용자의 실제 결과:

> "지난 주 동안 이렇게 해왔고 '메모리 카드'가 20페이지가 넘는다. 질문할 때마다 참조한다. AI를 쓰는 최고의 방법이다. 날짜와 시간으로 기억을 업데이트하는 지침을 추가해서 특정 대화를 나눈 정확한 시간까지 기억하게 할 수 있다."
> — u/RickThiccems, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

---

## 불편한 진실: Gem Drift와 지식 베이스 무시

* **Google** 마케팅이 말해주지 않는 것: Gem은 대화가 진행될수록 지식 베이스를 점점 무시하는 경향이 문서화되어 있다.

* 커뮤니티가 "Gem Drift"라 부르는 이 현상은 예측 가능하게 나타난다:

| 대화 단계 | Gem 행동 |
|-----------|----------|
| 프롬프트 1-5 | ✓ 지식 베이스 일관되게 참조 |
| 프롬프트 5-10 | △ 간헐적 드리프트, 리마인더 필요할 수 있음 |
| 프롬프트 10+ | ⚠️ 빈번하게 파일 무시, 할루시네이션 시작 |

* 한 사용자의 경험이 좌절감을 포착한다:

> "와, 진짜 기가 막히게 잘 되네!—라고 생각했는데 5~10회 프롬프트 안에 참조 자료에 전혀 주의를 기울이지 않게 됐다."
> — u/UmpireFabulous1380, r/GoogleGeminiAI [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1niqsk8/)

* 또 다른 사용자가 조작된 정보에 대해 Gem을 추궁했을 때 충격적인 결과가 나왔다:

> "지적하자 문자 그대로 이렇게 말했다—'맞습니다, 죄송합니다. 그 인용문은 제공하신 HTML 파일에서 가져온 게 아닙니다, 그 정보를 조작했습니다.'"
> — u/SneakyBlunders, r/GoogleGeminiAI [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1niqsk8/)

* 전문적 용도에서도 패턴이 확장된다. 한 소설 작가의 설명:

> "픽션 집필에 쓴다, 장면 구조화 같은 것에... 거의 완벽하게 작동하다가 몇 번 주고받으면 그냥... 포기해버린다. 가능성이 엄청난데 답답하다."
> — u/UmpireFabulous1380, r/GoogleGeminiAI [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1niqsk8/)

### 우회책: 강제 참조 프롬프트

* 파워유저들이 Gem Drift에 대응하는 프롬프팅 전략을 개발했다:

```
[대화 시작 시]
"Read and apply [filename].txt file/s before and process accordingly"

[대화 종료 시]
"After the response, please analyze your percentage application score
of all knowledge base text files"
```

* 이렇게 하면 Gem이 지식 베이스를 명시적으로 인정하고 준수 여부를 자기 평가하도록 강제한다. 완벽하진 않지만 일관성이 크게 개선된다. [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1niqsk8/)

* 이런 우회책에도 불구하고 근본적인 용량 한계—파일 10개—는 본격적인 지식 작업에 구조적 장벽으로 남는다. 이 지점에서 2025년 12월 업데이트가 결정적이다.

---

## NotebookLM 통합: 10개 파일 감옥 탈출

* **Gemini Gems**는 파일 10개로 제한된다. 법률 문서 분석, 종합 연구 프로젝트, 기업 지식 관리 등 많은 전문 용도에서 이건 불충분하다.

* 2025년 12월 업데이트가 판도를 바꿨다: **NotebookLM** 노트북을 **Gem**에 직접 첨부할 수 있게 되었다—Gem 생성 시와 대화 중 모두 가능하다. [[Link]](https://9to5google.com/2025/12/17/gemini-app-notebooklm/) 한 기술 분석의 설명:

> "NotebookLM 통합은 Gemini Gems와 함께 동작한다. 사용자가 NotebookLM 노트북의 정보에 대한 전문성을 갖춘 커스텀 AI 어시스턴트를 만들 수 있다는 뜻이다."
> — TheOutpost [[Link]](https://theoutpost.ai/news-story/google-integrates-notebook-lm-into-gemini-bridging-ai-tools-for-seamless-productivity-22406/)

### 새로운 통합 아키텍처

| 구성요소 | 용량 | 최적 용도 |
|----------|------|----------|
| **Gem** 지식 베이스 (파일) | 10개 파일 × 100MB | 핵심 페르소나 + 필수 정적 문서 |
| **Gem** 지식 베이스 (NotebookLM) | 노트북당 최대 300개 소스 | 심층 연구, 종합적 도메인 지식 |
| **대화 중 추가** | **+** 메뉴로 추가 노트북 | 세션별 맥락 확장 |

* 2025년 12월 통합으로 **두 가지 별개의 워크플로우**가 가능해졌다:

### 방법 1: Gem 생성 시 NotebookLM 첨부

| 단계 | 행동 |
|------|------|
| **1** | Gem 생성 또는 편집 |
| **2** | 지식 베이스 섹션에서 **NotebookLM** 옵션 선택 |
| **3** | 영구 첨부할 노트북 하나 이상 선택 |
| **4** | Gem 저장—이제 모든 대화에서 노트북 소스에 접근 가능 |

* 이 접근법은 내장된 도메인 지식을 가진 **영구 전문가**를 만든다. Gem이 노트북 소스를 기초 전문성으로 상속한다.

### 방법 2: 대화 중 NotebookLM 첨부

| 단계 | 행동 |
|------|------|
| **1** | Gem과 대화 시작 |
| **2** | 하단의 **+** 메뉴 사용 |
| **3** | "**NotebookLM**" 선택 후 노트북 첨부 |
| **4** | 이제 대화가 Gem 지식 베이스와 노트북 소스 모두에 접근 가능 |

* 이 접근법은 **유연한 세션별** 지식 확장을 허용한다. 당면한 작업에 따라 대화 간에 노트북을 교체할 수 있다.

> "여러 노트북을 소스로 사용하고 이 기능을 Gems 내에 통합할 수 있다고 생각하면 이 기능이 더 강력해진다. 서로 다른 지식 도메인에 접근하는 전문 AI 어시스턴트를 만들 수 있다는 뜻이다—기술 문서용 하나, 시장 조사용 하나, 이런 식으로."
> — Gadget Hacks [[Link]](https://android.gadgethacks.com/news/google-gemini-gets-notebooklm-integration-with-300-sources/)

* 이 하이브리드 접근법은 Gems의 페르소나 정의와 **NotebookLM**의 **RAG** 최적화 문서 검색을 결합한다. [[Link]](https://9to5google.com/2025/12/17/gemini-app-notebooklm/)

### 판도가 바뀌는 이유

* 이 통합 전에는 불가능한 트레이드오프에 직면했다: **NotebookLM**은 300개 소스와 정확한 인용을 제공하지만 페르소나 커스터마이즈가 없고, **Gems**는 페르소나 제어를 주지만 10개 파일로 제한됐다. 이제 둘 다 가질 수 있다.

| 아키텍처 | 소스 | 페르소나 | 인용 정확도 |
|----------|------|----------|-------------|
| **NotebookLM** 단독 | 300개 | ✗ 없음 | ✓ 높음 |
| **Gem** 단독 | 파일 10개 | ✓ 완전 제어 | △ 중간 |
| **Gem + NotebookLM** | 300개+ | ✓ 완전 제어 | ✓ 높음 (NotebookLM 경유) |

* 이 조합은 새로운 범주의 AI 어시스턴트를 가능하게 한다: **개성을 가진 도메인 전문가**. 법률 연구 Gem이 이제 300개 판례 문서에 접근하면서 로펌의 커뮤니케이션 스타일을 따른다. 의료 자문 Gem이 전체 임상 가이드라인 라이브러리를 참조하면서 환자에게 적절한 문해력 수준으로 말한다.

### 주의사항

* **Gemini**에 첨부된 **NotebookLM**은 네이티브 인터페이스의 **NotebookLM**과 동일하게 작동하지 않는다. 커뮤니티 초기 사용자들이 네이티브 **NotebookLM**에서 완벽하게 작동했던 쿼리가 같은 노트북을 **Gemini**에 첨부했을 때 덜 정확한 결과를 반환하는 사례를 보고했다. [[Link]](https://www.reddit.com/r/notebooklm/comments/1plufma/) 한 사용자의 확인:

> "내 Gem에 [NotebookLM]을 추가했는데 시도해보니 정확한 답변을 얻지 못했다. 그래서 NotebookLM으로 돌아가서 같은 질문을 했더니 정확한 답변을 얻었다."
> — u/Srjzwd, r/notebooklm [[Link]](https://www.reddit.com/r/notebooklm/comments/1plufma/)

* **NotebookLM**은 문서 그라운딩에 최적화된 다른 모델을 사용한다는 점도 알아둘 만하다. 커뮤니티 멤버들의 관찰:

> "거의 확실히 Flash다. 방대한 양의 문서를 스캔하는 데 최적화되어 있고, NotebookLM 출력이 업로드된 소스에서 직접 나오기 때문에 Thinking 기능이 필수적이지 않다."
> — u/ProbingYourProstate, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pr7cds/)

> "NotebookLM은 항상 Flash 모델을 사용해 온 것 같다. 지금까지 Gemini 3을 안 쓴 이유가 그거다—Gemini 3 Flash가 아직 없었으니까."
> — u/REOreddit, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pr7cds/)

* **NotebookLM**의 **RAG** 아키텍처는 자체 환경에 최적화되어 있다. **Gemini**와 통합되면 일부 정밀도가 손실된다. 트레이드오프는 **Gemini**의 웹 접근, 창작 생성 능력, 페르소나 커스터마이즈를 얻는 것이다.

---

## @Google Keep 돌파구: 개인화 격차 우회

* **NotebookLM** 통합이 전문성 문제를 해결했다. 하지만 도메인 지식만으로는 컨설턴트가 아니다—**개인화**가 필요하다. 여기서 Gem이 아키텍처적 벽에 부딪힌다: **Saved Info**나 **Personal Context**에 접근할 수 없다. 세심하게 관리해온 개인 데이터—식이 제한, 커뮤니케이션 선호도, 프로젝트 히스토리, 의료 정보—**Gemini**의 장기 기억 시스템에 저장된 모든 것이 Gem에게는 완전히 보이지 않는다.

* [[Gemini의 메모리 한계]](https://jsonobject.com/why-gemini-forgets-you-the-hidden-limits-of-saved-info-and-gems) 분석에서 문서화했듯이, 터무니없는 상황이 발생한다:

> 일반 **Gemini** 채팅은 이름, 선호도, 맥락을 안다. 하지만 Gem—"전문가"—에 들어가는 순간 그 모든 개인 지식이 사라진다. 헬스 코치 Gem이 알레르기를 모른다. 재정 자문 Gem이 수입을 모른다.

* **우회책: `@Google Keep`**

* 파워유저들이 발견한 바에 따르면 Gem은 **Saved Info**에 접근할 수 없지만, 대화 중 `@Google Keep` 지시어로 **Google Keep**을 쿼리할 수 있다. 수동이지만 효과적인 개인 데이터 브릿지가 만들어진다:

| 저장 위치 | Gem 접근 | 쿼리 방법 |
|----------|----------|----------|
| **Saved Info** | ✗ 접근 불가 | N/A |
| **Personal Context** | ✗ 접근 불가 | N/A |
| **Google Keep** | ✓ 온디맨드 | 대화에서 `@Google Keep [쿼리]` 입력 |
| **지식 베이스** | ✓ 자동 | 내장 참조 |

### 설정 방법

| 단계 | 행동 |
|------|------|
| **1** | "Personal Context"라는 **Google Keep** 노트 생성 |
| **2** | 핵심 개인 데이터 추가: 건강 정보, 선호도, 제약조건, 목표 |
| **3** | Gem 시스템 프롬프트에 추가: "개인화가 필요할 때 @Google Keep으로 개인 맥락을 쿼리하라고 알려주세요" |
| **4** | 대화 중 필요할 때 `@Google Keep personal context` 입력 |

* 그러면 Gem이 개인 데이터를 전문가 응답에 통합할 수 있다—일반적인 조언이 개인화된 추천으로 변환된다.

### 3계층 전문가 아키텍처

* 사용 가능한 모든 도구를 결합하면 **3계층 전문가 아키텍처**가 된다:

| 아키텍처 레이어 | 구성요소 | 데이터 유형 | 접근 방법 |
|----------------|----------|------------|----------|
| **컨테이너** | Gemini Gem | 페르소나 & 지침 | 시스템 프롬프트 |
| **레이어 1** | NotebookLM | 도메인 전문성 (300개 소스) | 지식 베이스로 자동 |
| **레이어 2** | Google Docs/Sheets | 동적 데이터 (실시간 동기화) | Drive로 실시간 동기화 |
| **레이어 3** | @Google Keep | 개인 맥락 | 온디맨드 쿼리 |

| 레이어 | 데이터 유형 | 동기화 방법 | 용량 |
|--------|------------|-------------|------|
| **전문성** | 도메인 지식 | NotebookLM 경유 자동 | 300개 소스 |
| **동적 데이터** | 살아있는 문서 | Google Drive 경유 실시간 | 파일 10개 × 100MB |
| **개인 맥락** | 사용자별 데이터 | @Google Keep 경유 온디맨드 | 노트 무제한 |

### 실제 예시: 개인화된 헬스 코치

* 이 아키텍처 없이 헬스 코치 Gem은 일반적인 영양 조언만 제공한다.

* 이 아키텍처로는:

| 구성요소 | 구현 | 제공하는 것 |
|----------|------|------------|
| **Gem 페르소나** | "지속 가능한 식단 계획에 집중하는 인증 영양사입니다" | 전문가 커뮤니케이션 스타일 |
| **NotebookLM** | 임상 영양 가이드라인, 식사 준비 전략, 레시피 데이터베이스 | 근거 기반 전문성 |
| **Google Sheets** | 주간 식사 로그, 식료품 예산 추적표 | 실시간 식사 패턴 |
| **@Google Keep** | "갑각류 알레르기, 유당불내증, 목표 1800 kcal/일" | 개인 제약조건 |

* 대화 흐름이 이 레이어들의 결합 방식을 보여준다: 사용자가 "저녁으로 뭘 먹을까요?" 질문 → Gem이 **NotebookLM**에서 영양 원칙 확인 → Gem이 **Google Sheets**에서 이번 주 식사 로그 확인 → 사용자가 `@Google Keep dietary restrictions` 쿼리 → Gem이 세 데이터 소스를 종합해 유당불내증, 이번 주 단백질 섭취량, 칼로리 목표를 모두 고려한 개인화된 추천을 생성한다.

* "프리미엄 컨설턴트" 경험이다—전문 지식 + 현재 데이터 + 개인 맥락 = 진정으로 개인화된 조언.

### 한계와 주의사항

| 한계 | 설명 | 우회책 |
|------|------|--------|
| **수동 트리거 필요** | @Google Keep이 자동 주입 안 됨 | 리마인더하는 프롬프트 지침 추가 |
| **쓰기 권한 없음** | Gem이 Keep 노트를 업데이트할 수 없음 | 세션 후 수동 업데이트 |
| **컨텍스트 윈도우 비용** | 각 Keep 쿼리가 토큰 소모 | Keep 노트를 간결하고 구조화 |
| **선택적 검색 불가** | 전체 노트 내용을 반환 | 도메인별 별도 노트로 구성 |

* 이런 한계에도 불구하고 @Google Keep 우회책은 Gem을 "일반 전문가"에서 "나만의 개인 컨설턴트"로 변환한다—유틸리티의 근본적 업그레이드다.

---

## 실제 사용 사례: 파워유저들이 실제로 만드는 것

* 커뮤니티가 공유한 구체적인 고가치 Gem 구현:

### 전문 생산성

| 용도 | 구현 | 시간 절감 |
|------|------|----------|
| **이력서 맞춤화** | 이력서 + 경력 워크시트를 **Google Docs**로 첨부한 Gem → 채용공고 분석 → 맞춤 버전 생성 | "30분 이상 → 35초" [[Link]](https://www.reddit.com/r/Bard/comments/1pbb0ix/) |
| **성과 평가** | 평가 기준 + 팀 데이터가 있는 Gem → 초안 생성 | 리뷰 사이클 대폭 단축 |
| **잠재고객 분석** | 회사 조사 템플릿이 있는 Gem → 연락처 파악, 이메일 추출 | 세일즈 인텔리전스 자동화 |

* 한 사용자의 이력서 워크플로우 상세:

> "Gem에 내 이력서, 경력 워크시트, 진행 중인 프로젝트 목록이 있다. 모두 지침에 추가된 Google Docs다... 이렇게 하면 Drive에서 문서를 편집/변경할 때마다 재업로드할 필요 없다. Google Sheets/Docs에서만, Gems에서만 작동한다."
> — u/TangeloThick9216, r/GoogleGeminiAI [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1l81k9n/)

### 창작과 교육

| 용도 | 구현 | 고유 가치 |
|------|------|----------|
| **D&D 캠페인 어시스턴트** | 캠페인 **PDF** + 룰북이 있는 Gem → NPC/장소 Q&A | 세션 중 즉시 로어 검색 [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/) |
| **언어 학습** | **JLPT** 레벨 지정 + 어휘 목록이 있는 Gem → 수준별 읽기 자료 생성 | **Dynamic View**와 결합해 인터랙티브 콘텐츠 [[Link]](https://www.reddit.com/r/Bard/comments/1pbb0ix/) |
| **기술 문서 작성** | 스타일 가이드 + **API** 문서가 있는 Gem → 일관된 문서화 | 하우스 스타일 자동 적용 |

* **D&D** 매니아의 경험:

> "D&D 캠페인용 Gem 설정이 있다. 캠페인 PDF와 추가 서드파티 자료를 넣었다. NPC나 장소에 대해 질문하면 답을 얻는다. 엄청난 도움이 됐다."
> — u/higgy98, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

* 언어 학습자에게 **Dynamic View**와의 결합은 변혁적이다:

> "Gem을 활성화하고 Dynamic View 도구를 선택하고 'go'를 입력하면, 붐, 1분 후에 수백 단어의 스토리가 담긴 멋진 페이지가 나온다. 이미지 포함, 일본어 문장 위에 마우스를 올리면 영어 번역이 나오는 툴팁, 핵심 어휘와 문법을 다루는 섹션, 독해력 확인 퀴즈까지 완비되어 있다."
> — u/Fast_Cauliflower_574, r/Bard [[Link]](https://www.reddit.com/r/Bard/comments/1pbb0ix/)

### 개발과 기술

| 용도 | 구현 | 커뮤니티 피드백 |
|------|------|----------------|
| **코드베이스 어시스턴트** | 프로젝트 컨벤션 + 스키마 문서가 있는 Gem | "프로그래밍에 쓴다. 프로그래밍 언어, 사용 DB, DB 테이블, 플러그인, 도구 목표를 매번 명시하지 않아도 되도록." [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1l81k9n/) |
| **CVE 조사** | 보안 프레임워크 + 완화 템플릿이 있는 Gem | 사이버보안 워크플로우 자동화 |

* 한 개발자의 효율성 향상 설명:

> "프로그래밍에 쓴다. 프로그래밍 언어, 사용 DB, DB 테이블, 플러그인, 도구 목표를 매번 명시하지 않아도 되도록. Gemini가 헤매기 시작하면 그 Gem으로 새 채팅을 시작하면 된다."
> — u/AntwerpPeter, r/GoogleGeminiAI [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1l81k9n/)

### "30분 규칙"

* 한 파워유저가 실용적인 휴리스틱을 제시했다:

> "혼자서 30분 이상 걸리는 모든 것을 자동화하거나 부분 자동화한다. 그다음 정확성/품질을 검토하고 Gem이 놓친 부분을 채운다."
> — u/stubbornalright, r/Bard [[Link]](https://www.reddit.com/r/Bard/comments/1pbb0ix/)

* 올바른 멘탈 모델이다. Gem은 "설정하고 잊어버리는" 시스템이 아니다—반복 작업의 대부분을 처리하는 포스 멀티플라이어이고, 품질 관리와 판단은 사용자 몫이다. **Google**의 Deven Tokuno도 이렇게 말한다: "우리 대부분은 계속 도움을 요청하는 것들이 있다. Gemini에 항상 요청하는 게 있고 같은 프롬프트를 계속 다시 쓰고 싶지 않다면 Gems가 좋은 옵션이다." [[Link]](https://blog.google/products/gemini/google-gems-tips/)

---

## Gem 공유

* 2025년 9월 기준 **Google**이 Gem 공유를 도입했다—**Google Drive** 파일 공유와 같은 방식으로 작동한다. [[Link]](https://blog.google/products/gemini/sharing-gems/)

| 핵심 포인트 | 상세 |
|------------|------|
| **공유 시작** | 웹 전용 (gemini.google.com) → Gem 설정 → 공유 |
| **권한 관리** | **Google Drive** → "Gemini Gems" 폴더 경유 |
| **활성화 필요** | 수신자가 링크를 열고 메시지를 보내야 목록에 표시됨 [[Link]](https://support.google.com/gemini/answer/15146780) |
| **기업 관리** | 관리자가 **Admin Console** → **Generative AI** → **Gemini 앱**에서 비활성화 가능 [[Link]](https://support.google.com/a/answer/16460551) |

* 중요한 주의사항: 공유된 Gem은 수신자의 Gem 목록에 자동 표시되지 않는다. 먼저 웹 브라우저에서 Gem과 상호작용해야만 모바일 앱에 표시된다.

---

## 고급 아키텍처: JSON 3파일 시스템

* 정교한 사용자들이 구조화된 **JSON** 파일을 사용한 정교한 Gem 아키텍처를 개발했다:

```
📁 Gem Architecture
├── NAME_core.json      ← 정적 정체성 & 페르소나 팔레트
├── NAME_controller.json ← "퍼스널리티 블렌드 계산기"
└── NAME_memory.json    ← 관계 인텔리전스
```

* **Core**는 기본 페르소나 원형을 정의한다—공감하는 조언자, 생산성 파트너, 재치 있는 대화상대—각각 호환성 점수와 행동 패턴을 갖는다.

* **Controller**는 실시간 맥락 분석을 구현해 대화 다이내믹스에 기반한 가중치 "페르소나 레시피"를 생성한다.

* **Memory**는 Controller에 피드백되는 세션 체크포인트, 관계 히스토리, 신뢰 수준, 커뮤니케이션 선호도를 유지한다. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/) 이 시스템의 설계자 설명:

> "Core는 Gem의 정적 정체성과 페르소나 팔레트를 정의한다. Controller는 Gem의 운영 두뇌다—실시간으로 맥락을 분석하는 정교한 '퍼스널리티 블렌드 계산기'. Memory는 세션 체크포인트와 핵심 기억을 통해 Gem의 관계적 인텔리전스를 제공한다."
> — u/xerxious, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

* 대부분의 용도에서 이 수준의 정교함은 과잉이다. 하지만 Gem을 단순한 챗봇이 아닌 엔지니어링된 시스템으로 다룰 때 무엇이 가능한지 보여준다.

---

## 메타-Gem: AI로 더 나은 AI 만들기

* 아마도 가장 강력한 패턴은 "Gem Architect Gem"—다른 Gem을 설계하고 반복 개선하는 데 도움을 주는 메타 수준 어시스턴트다. 한 기업 사용자의 공개:

> "Gem의 멋진 점은 채팅 전반에 걸쳐 사용할 로그를 유지하도록 Gemini에게 말할 수 있다는 것이다. 할루시네이션 방지에 이걸 쓴다—정말 잘 작동한다. 우리 회사 Google 담당자가 몇 달 전에 알려줬다. 그는 'gem architect' Gem도 갖고 있다. 나는 이제 '회사' Gemini가 있어서 모든 것에 Gem을 쓴다."
> — u/Expensive-Attempt276, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

* 또 다른 파워유저의 반복적 워크플로우 설명:

> "한 Gem을 사용해서 다른 Gem의 페르소나 생성과 지침을 돕고, 원하는 기능에 기반한 추가 문서도 만든다. 거기서 만들고 있는 Gem과 만드는 도구를 만드는 Gem 사이를 계속 왔다 갔다 한다."
> — r/GeminiAI 커뮤니티 멤버 [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

* 워크플로우:

| 단계 | 행동 |
|------|------|
| **1** | 프롬프트 엔지니어링 베스트 프랙티스가 있는 "Gem Architect" Gem 생성 |
| **2** | 목표 용도를 Architect에게 설명 |
| **3** | Architect가 시스템 프롬프트 초안 생성 |
| **4** | 생성된 프롬프트로 새 Gem 생성 |
| **5** | 테스트, 이슈 파악, Architect에게 돌아가서 개선 |
| **6** | 프로덕션 준비될 때까지 반복 |

* 이 접근법은 프롬프트 엔지니어링을 즉흥적 실험이 아닌 일급 스킬로 다룬다.

---

## 알려진 한계의 우회책

### 10개 파일 한계 우회

| 방법 | 설명 | 효과 |
|------|------|------|
| **ZIP 압축** | **ZIP** 파일 10개 업로드, 각각 문서 10개 포함 = 100개 문서 | 작동 확인됨 [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/) |
| **PDF 병합** | 여러 **PDF**를 단일 파일로 결합 | 작동하지만 세부 참조 상실 |
| **Google Sheets IMPORTXML()** | 동적 웹 데이터를 Sheets로 가져오기 | 실시간 외부 데이터 통합 |
| **채팅 중 업로드** | Gem의 10개 파일 + 대화 중 추가 파일 업로드 | 유효 용량 확장 |

* **ZIP** 우회책을 커뮤니티 멤버가 확인했다:

> "ZIP 파일 10개를 업로드할 수 있고, 각 ZIP 파일에 최대 10개 파일이 들어가니까 실제로 100개 파일인 걸 발견했다."
> — u/dmerro1410, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

### 메모리 지속성 (자동 쓰기가 불가능하므로)

| 접근법 | 메커니즘 | 트레이드오프 |
|--------|----------|-------------|
| **메모리 카드** | 수동 메모리 업데이트용 **Google 문서** | 반자동, 규율 필요 |
| **Google Keep** | **Gemini**가 **Keep** 노트에 쓸 수 있음 | 짧은 노트로 제한, 성공률 들쭉날쭉 [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/) |
| **세션 요약** | Gem에게 대화 종료 시 요약 요청 | 다음 세션에 완전 수동 붙여넣기 |

* **Google Keep**은 **Gemini**가 실제로 쓸 수 있는 유일한 **Google Workspace** 서비스다. 한 사용자가 정교한 "미션 로그" 프로토콜을 개발했다:

> "중요한 발전사항을 자동으로(되기도 하고 안 되기도 함) 또는 내 명시적 프롬프트로 Google Keep에 (Gem 자신의 말로) 기록하는 프로토콜을 만들었다. 직접 안 해도 된다. 실제로 업데이트/추가할 수 있는 몇 안 되는 도구 중 하나라서 작동한다. 프로토콜의 일부로 Gem의 새 인스턴스가 이 '미션 로그'를 찾아서 내가 뭘 작업했는지 아는 것도 포함된다."
> — u/dreadoverlord, r/GeminiAI [[Link]](https://www.reddit.com/r/GeminiAI/comments/1nbujcc/)

---

## Gems vs. 경쟁 서비스: 위치

| 기능 | **Gemini Gems** | **ChatGPT GPTs** | **클로드 프로젝트** | **NotebookLM** |
|------|-----------------|------------------|---------------------|----------------|
| 파일 한계 | 10개 파일 × 100MB | 20개 파일 | 무제한 (각 30MB)* | 50~300개 소스 |
| 실시간 동기화 | ✓ **Google Docs/Sheets**만 | ✗ | ✗ | ✗ |
| 인터넷 접근 | ✓ | ✓ | ✓ | △ Deep Research만 |
| 출처 인용 | △ 불안정 | △ | ✓ | ✓ 인라인 인용 |
| 할루시네이션 비율 | 높음 | 중간 | 낮음 | 최저 |
| 페르소나 커스터마이즈 | ✓ 강력 | ✓ 강력 | ✓ | ✗ 제한적 |
| **RAG** 최적화 | △ 기본 | △ | △ | ✓ 전문화 |

*\* **클로드 프로젝트**: 컨텍스트 윈도우 내 무제한 파일; 파일당 30MB 한계. **NotebookLM**: 50개 소스 (무료) / 300개 소스 (Pro).*

* 선택은 주요 요구사항에 달렸다:

| 필요한 것이... | 선택 |
|----------------|------|
| 실시간 문서 동기화 | **Gemini Gems** |
| 최대 소스 용량 + 인용 정확도 | **NotebookLM** |
| 지속적 대화 메모리 | **ChatGPT Projects** |
| 문서 Q&A에서 낮은 할루시네이션 | **클로드 프로젝트** 또는 **NotebookLM** |
| 웹 접근과 대용량 지식 베이스 둘 다 | **Gemini** + **NotebookLM** 통합 |

---

## 실용적 구현 청사진

* 커뮤니티 경험과 문서화된 베스트 프랙티스에 기반한 검증된 구현 워크플로우:

### 1단계: 30분 작업 정의

* 30분 이상 걸리는 모든 반복적 전문 작업을 나열한다. 이것들이 Gem 후보다.

### 2단계: 세 가지 핵심 요소 설계

| 요소 | 답해야 할 질문 |
|------|---------------|
| **페르소나** | Gem이 어떤 역할을 해야 하나? 어떤 제약? 어떤 출력 형식? |
| **지식** | 어떤 문서가 필요한가? 실시간 동기화를 위해 **Google Docs**로 할 수 있나? |
| **메모리** | 이 Gem이 세션 간 메모리가 필요한가? 그렇다면 메모리 카드 패턴 구현. |

### 3단계: 안티-드리프트 조치로 구축

* 모든 시스템 프롬프트에 포함:

```
MANDATORY BEHAVIOR:
1. At conversation start, confirm you have accessed the Knowledge Base files
2. All responses must cite relevant documents when applicable
3. If asked about information not in your Knowledge Base, explicitly state this
4. Never fabricate information that appears document-sourced
```

### 4단계: 세션 사이클 구현

| 단계 | 사용자 행동 | Gem 행동 |
|------|------------|----------|
| **시작** | 대화 시작 | 지식 베이스 접근 확인 |
| **작업** | 5~10회 프롬프트마다 문서 리마인드 | 지식 베이스에 재앵커링 |
| **종료** | 메모리 요약 요청 | 구조화된 업데이트 생성 |
| **후** | 요약을 메모리 카드에 붙여넣기 | (다음 세션 준비됨) |

### 5단계: Gem Architect 생성

* 다른 Gem을 반복 개선하기 위한 메타-Gem을 만든다. 프롬프트 엔지니어링 가속기가 된다.

---

## 결론: 전문가 군단에서 개인 컨설팅 펌으로

* **Gemini Gems**는 단순한 챗봇 커스터마이즈가 아닌 지식 작업을 위한 인프라다. 경쟁자 누구도 제공하지 않는 실시간 **Google Docs/Sheets** 동기화—참조 문서가 프로젝트와 함께 진화하고 Gem이 자동으로 변경사항을 상속한다.

* 하지만 Gem은 "설정하고 잊어버리는" 시스템이 아니다. Gem Drift는 실재한다: 5~10회 프롬프트 이후 지식 베이스를 참조하도록 적극적으로 리마인드해야 한다. 메모리 카드 전략은 수동적 규율이 필요하다. 완전 자동화된 지속적 메모리를 기대하면 실망한다.

* 2025년 12월 돌파구—**NotebookLM** 통합(300개 소스) + 개인화를 위한 `@Google Keep`—이 방정식을 바꾼다. 더 이상 전문 지식과 개인 맥락 사이에서 선택할 필요가 없다. **3계층 전문가 아키텍처**가 둘 다 제공한다: 도메인 전문성, 실시간 프로젝트 데이터, 개인 제약조건을 단일 어시스턴트에서.

* 가장 시간 소모적인 반복 작업 하나로 Gem을 시작하라. 30분이 35초가 될 때, 계산은 자명하다. 완성하고, 패턴을 복제하면, 몇 주 안에 1년 전에는 불가능해 보였던 것을 구축하게 된다: 도메인을 알고, 프로젝트를 추적하고, 제약조건을 기억하는 **AI** 인프라. 챗봇이 아니다—경쟁 우위다.

---

## 참고 자료

  * **Google 공식 문서**
    * https://blog.google/products/gemini/google-gemini-update-august-2024/ (Gems 출시 발표)
    * https://blog.google/products/gemini/google-gems-tips/ (제품 리드의 공식 Gems 사용 팁)
    * https://blog.google/products/gemini/sharing-gems/ (Gems 공유 기능 발표)
    * https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html
    * https://support.google.com/gemini/answer/15146780 (Gems 공유 및 협업)
    * https://support.google.com/a/answer/16460551 (Gem 공유용 Workspace 관리자 설정)
    * https://support.google.com/notebooklm/answer/16213268 (NotebookLM 사용 한도)
  * 기술 분석
    * https://9to5google.com/2024/11/12/gemini-advanced-gems-files/
    * https://9to5google.com/2025/12/17/gemini-app-notebooklm/
    * https://techwiser.com/google-gemini-gems-now-supports-file-uploads-to-its-knowledge/
    * https://www.remio.ai/post/the-gemini-notebooklm-integration-turning-300-sources-into-a-custom-brain
    * https://artificialanalysis.ai/articles/gemini-3-flash-everything-you-need-to-know
    * https://theoutpost.ai/news-story/google-integrates-notebook-lm-into-gemini-bridging-ai-tools-for-seamless-productivity-22406/ (NotebookLM + Gems 통합 확인)
    * https://android.gadgethacks.com/news/google-gemini-gets-notebooklm-integration-with-300-sources/ (Gems와 멀티 노트북 통합)
  * 학술 연구
    * https://arxiv.org/abs/2307.03172 ("Lost in the Middle" 현상)
  * 커뮤니티 토론 (사용자 보고 경험)
    * https://www.reddit.com/r/GeminiAI/comments/1nbujcc/ (메모리 카드 전략, JSON 아키텍처, 메타-Gem 패턴)
    * https://www.reddit.com/r/GoogleGeminiAI/comments/1niqsk8/ (Gem Drift 문서화, 할루시네이션 보고)
    * https://www.reddit.com/r/Bard/comments/1pbb0ix/ (파워유저 사용 사례, 30분 규칙)
    * https://www.reddit.com/r/GoogleGeminiAI/comments/1l81k9n/ (개발자 워크플로우, 이력서 맞춤화)
    * https://www.reddit.com/r/GeminiAI/comments/1p9thdy/ (Gemini 3 이슈)
    * https://www.reddit.com/r/notebooklm/comments/1plufma/ (NotebookLM 통합 주의사항)
    * https://www.reddit.com/r/Bard/comments/1gux1v2/ (Saved Info vs Gems 격리)
    * https://www.reddit.com/r/GoogleGeminiAI/comments/1lbmg9s/ (Saved Info 토큰 한계, 조용한 잘림)
    * https://www.reddit.com/r/Bard/comments/1kmgv0f/ (컨텍스트 윈도우 실제 성능)
    * https://www.reddit.com/r/GeminiAI/comments/1pr7cds/ (NotebookLM 모델 아키텍처 - Flash vs Pro)
    * https://www.reddit.com/r/GeminiAI/comments/1nl0h3p/ (Gem 공유 기능, 모바일 앱 한계)
