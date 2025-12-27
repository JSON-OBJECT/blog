# Gemini는 왜 당신을 잊는가: 저장된 정보와 젬스의 숨겨진 한계

## 핵심 요약

* **Gemini**는 "의도적으로 보수적인" 개인화 정책을 쓴다. 데이터는 갖고 있지만 명시적 트리거가 있어야만 활용한다.

* **저장된 정보**에는 숨겨진 한계가 있다. 활성 슬롯 10~75개, 슬롯당 약 1,500자. 초과 시 **FIFO(선입선출)** 방식으로 오래된 항목부터 조용히 잘린다.

* **젬스**는 **저장된 정보**를 상속하지 않는다. 각 젬스 지시문에 데이터를 직접 복사해야 한다.

* **Gemini 3.0 Pro**는 2025년 12월 4일 업데이트 이후 컨텍스트 유지 버그가 있다. 구글도 이 문제를 인정했다.

* 최선의 우회책: 시계열 데이터는 **Google Sheets** + **젬스** 조합, 리서치 자료는 **노트북LM** 연동

---

## 서론

* 영화 **아이언맨**의 **J.A.R.V.I.S.**는 단순히 질문에 답하지 않는다. 토니 스타크를 *안다*. [[Link]](https://en.wikipedia.org/wiki/J.A.R.V.I.S.) 영화 **Her**의 사만다는 관계를 통해 진짜 기억을 쌓아간다. [[Link]](https://en.wikipedia.org/wiki/Her_(2013_film)) 이 허구의 AI 동반자들에겐 공통점이 있다. 현재 어떤 실제 AI도 갖지 못한 능력—경험을 자기 마음에 영구적으로 기록하는 능력이다. 상용 배포된 모든 LLM은 근본적으로 *읽기 전용*이다. 신경망 가중치는 배포 시점에 동결된다. [[Link]](https://www.letta.com/blog/stateful-agents) 2025년 우리가 "AI 메모리"라 부르는 건 사실 정교한 우회책 모음이다. 컨텍스트 주입, 외부 데이터베이스, 요약 문서. Hacker News의 한 분석에 따르면 메모리 시스템은 "전체 히스토리를 그냥 넘기는 것보다 14~77배 비싸고 31~33% 덜 정확하다." [[Link]](https://news.ycombinator.com/item?id=46032521)

* **Google Gemini**는 세계 최대 개인 데이터 저장소—**Gmail**, **Drive**, **Calendar**, **Photos**—위에 앉아 있으면서도 의도적으로 사용을 자제한다. 버그가 아니다. 2025년 AI 메모리 전쟁에서 구글이 택한 철학적 선택이다. **ChatGPT**가 모든 걸 공격적으로 기억하고 **Claude**가 투명한 도구 기반 메모리를 제공하는 동안, **Gemini**는 제3의 길을 간다. 명시적 트리거가 있어야만 작동하는 "의도적으로 보수적인" 개인화다.

* 이 글은 **Gemini** 개인화 아키텍처가 정확히 어떻게 작동하는지 해부한다. 당신이 겪는 "기억상실 증후군"이 왜 설계 의도인지 설명한다. LLM의 근본적인 읽기 전용 특성에서 비롯된 아키텍처적 한계를 우회하는 체계적 프레임워크도 제시한다.

---

## Gemini 메모리의 아키텍처: RAG가 아니라 훨씬 단순한 무언가

* 첫 번째 오해부터 바로잡자. **Gemini**는 개인화에 **RAG(Retrieval-Augmented Generation)**를 쓰지 않는다. **Shlok Khemani**의 리버스 엔지니어링 분석에 따르면 **Gemini**는 훨씬 단순한 메커니즘—압축 요약 주입—을 쓴다. [[Link]](https://www.shloked.com/writing/gemini-memory)

* 시스템은 `user_context`라는 단일 문서를 중심으로 작동한다:

| 카테고리 | 내용 |
|----------|------|
| **1. 인구통계 정보** | 이름, 나이, 위치, 직업 |
| **2. 관심사 & 선호도** | 관심 주제, 기술 스택, 목표 |
| **3. 관계** | 중요한 사람들 |
| **4. 날짜 태그된 이벤트/프로젝트/계획** | 시간 태그가 붙은 활동 기록 |
| **5. 최근 컨텍스트** | 최근 대화 몇 턴 |

* 벡터 데이터베이스, 청크 임베딩, 쿼리 기반 검색을 쓰는 진짜 RAG 시스템과 달리 **Gemini**는 이 압축 요약을 모든 대화의 컨텍스트 윈도우에 그냥 주입한다. 시맨틱 검색 없음. 관련성 점수 계산 없음. 그냥 무차별 컨텍스트 주입이다.

* "벡터 데이터베이스 없음, 지식 그래프 없음, RAG 없음. 매번 전부 다 쏟아붓는다." **Khemani**가 **ChatGPT**와 **Gemini** 메모리 시스템을 분석하며 남긴 관찰이다. [[Link]](https://www.shloked.com/writing/chatgpt-memory-bitter-lesson)

* 여기서 **Gemini**의 아키텍처적 장점이 부각된다. 주요 AI 플랫폼 중 **Gemini 3 Pro**가 가장 큰 컨텍스트 윈도우를 제공한다—100만 토큰. 텍스트로 약 1,500페이지, 코드로 약 30,000줄에 해당한다. [[Link]](https://9to5google.com/2025/12/24/google-ai-pro-ultra-features/) 비교하면 **OpenAI**의 **GPT-5.2**(2025년 12월 11일 출시)는 40만 토큰을 지원하고, [[Link]](https://venturebeat.com/ai/openais-gpt-5-2-is-here-what-enterprises-need-to-know) **Anthropic**의 **Claude Opus 4.5**는 20만 토큰을 제공한다(엔터프라이즈 배포 시 최대 100만 토큰 가능). [[Link]](https://aws.amazon.com/bedrock/anthropic/)

* **Gemini 3 Pro**는 **GPT-5.2** 대비 2.5배, **Claude Opus 4.5** 표준 윈도우 대비 5배 우위다. "무차별 컨텍스트 주입" 방식이 한계에 부딪히기 전까지 여유가 더 있다는 뜻이다.

### 3계층 개인화 스택

* **Gemini** 개인화는 세 개의 독립된 계층에서 작동한다. 각 계층은 서로 다른 동작을 한다:

| 계층 | 기능명 | 역할 | 우선순위 |
|------|--------|------|----------|
| **레벨 1** | **Gemini 앱 활동** | 대화 저장 여부 자체를 제어 | 기반 |
| **레벨 2** | **개인 맥락** | 과거 채팅을 분석해 사용자 프로필 구축 | 2차 |
| **레벨 3** | **저장된 정보** | 사용자가 직접 정의한 명시적 지시문 | 최우선 |

* **개인 맥락**(설정에서 "Gemini와의 과거 채팅"으로 표시)은 **Gemini**가 대화 기록을 분석해 패턴과 선호도를 추출하도록 허용한다. [[Link]](https://support.google.com/gemini/answer/15637730)

* **저장된 정보**(설정에서 "기억할 정보"로 표시)는 사용자가 직접 입력한 명시적 지시문을 담는다. 자동 추출된 **개인 맥락**보다 우선한다.

---

## "의도적으로 보수적인" 정책: Gemini가 당신을 모르는 척하는 이유

* 2025년 6월에 공개된 시스템 프롬프트가 **Gemini** 개인화 가이드라인의 실제 작동 방식을 보여준다:

```
Guidelines on how to use the user information for personalization:
- Use Relevant User Information & Balance with Novelty
- Acknowledge Data Use Appropriately (only when it significantly shapes your response)
- Avoid Over-personalization... as a default rule, DO NOT use the user's name
- Prioritize & Weight Information Based on Intent/Confidence
```

* 이 "균형 잡힌 접근" 정책 때문에 **Gemini**는 데이터를 갖고 있으면서도 *선택적으로* 쓰도록 지시받는다. 공격적으로 쓰지 않는다. 개인화는 "사용자의 현재 쿼리와 직접 관련될 때만" 작동한다. [[Link]](https://www.reddit.com/r/LLMDevs/comments/1l3rt10/)

* 트리거 조건에 해당하는 문구들:
  - "내 관심사에 기반해서..."
  - "이전 대화를 고려해서..."
  - "나에 대해 아는 것을 바탕으로..."

* 이런 명시적 트리거 없이 **Gemini**는 종종 아무 기억도 없는 것처럼 행동한다. 데이터가 없어서가 아니다. 시스템 프롬프트의 "과도한 개인화 회피" 가이드라인 때문에 신중한 쪽으로 기울기 때문이다.

### 선택적 활성화 모델

* **Gemini** 개인화의 실제 작동 방식:

| 단계 | 프로세스 | 결과 |
|------|----------|------|
| **1단계** | 사용자 데이터 존재 여부 확인 | 컨텍스트에 데이터 있음 |
| **2단계** | 시스템 프롬프트 가이드라인 적용 | "직접 관련될 때만 사용" |
| **3단계** | 현재 쿼리와의 관련성 평가 | 개인화가 진짜 도움이 되나? |
| **4a단계** | 높은 관련성 감지 | 개인화 **활성화** (응답에 암묵적으로 반영) |
| **4b단계** | 낮은 관련성 또는 모호함 | 개인화 **억제** ("소름끼치는" 과잉 개인화 방지) |

* 사용자들이 겪는 답답한 불일치가 여기서 설명된다. 저장한 정보가 사라진 게 아니다. 신중한 쪽으로 자주 기우는 관련성 게이트를 통과하는 중이다. 명시적 트리거 문구가 **Gemini**에게 개인화를 진심으로 원한다는 신호를 보내는 데 도움이 된다.

---

## 저장된 정보의 위기: 조용한 잘림과 숨겨진 한계

* "의도적으로 보수적인" 동작 외에도 **저장된 정보**에는 "기억상실" 문제를 악화시키는 구조적 한계가 있다.

### 슬롯 한계 논란

* 커뮤니티 테스트 결과 **저장된 정보** 한계에 대한 상충되는 보고가 있다. 구글이 다양한 구성을 A/B 테스트 중일 수 있음을 시사한다:

| 보고자 | 관찰된 슬롯 수 | 비고 |
|--------|----------------|------|
| 사용자 A | ~10개 활성 | 오래된 항목 조용히 무시됨 [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pdxddr/) |
| 사용자 B | ~75개 슬롯 | **ChatGPT** 메모리에서 복사 [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1lbmg9s/) |

* "활성 처리에 숨겨진 한계가 있다. 너무 많이 추가하면 가장 오래된 지시문이 조용히 '잊힌다'. 설정 페이지에는 그대로 있지만 활성 컨텍스트에 로드되지 않는다." 한 Reddit 사용자의 보고다.

* 이 불일치는 유효 한계가 항목 수가 아니라 **총 토큰 수**에 의존할 수 있음을 시사한다. **Lifehacker** 테스트에 따르면 슬롯당 약 **1,500자**가 허용된다. [[Link]](https://lifehacker.com/tech/saved-info-google-gemini)

### FIFO 잘림

* 한계를 초과하면 **FIFO(선입선출)** 잘림이 작동한다. 가장 오래된 저장 정보가 활성 컨텍스트 윈도우에서 조용히 제거된다—경고도, 알림도 없이.

| 지표 | 관찰값 |
|------|--------|
| 슬롯당 글자 수 | ~1,500 |
| 활성 토큰 한계 | 추정 16K-32K 토큰 (계정별 상이) |

### 타임스탬프 문제

* **저장된 정보** 항목에는 날짜/시간 메타데이터가 없다. "내 현재 체중"을 물으면 **Gemini**는 다음을 구분할 수 없다:
  - 12월 22일에 입력한 체중: 75.2kg
  - 12월 27일에 입력한 체중: 74.5kg

* 타임스탬프 없이 **Gemini**는 컨텍스트에서 먼저 만나는 항목을 참조할 수 있다. 흔히 더 오래된 항목이다. 가장 최근 업데이트를 "잊은" 것처럼 보이는 착시가 생긴다.

---

## 젬스 격리 문제

* 많은 사용자가 **젬스**(맞춤형 AI 어시스턴트)가 **저장된 정보**를 상속한다고 가정한다. 그렇지 않다.

* "**저장된 정보**에 중요한 정보를 저장했는데 내 커스텀 **젬**이 전혀 인식하지 못한다. 설계상 이런 건가?" 한 혼란스러운 사용자의 질문이다. [[Link]](https://www.reddit.com/r/GoogleGeminiAI/comments/1nt6yoe/)

* **젬스**는 메인 **Gemini** 인스턴스와 완전히 격리된다:

### 개인화 데이터 흐름

| 소스 | 대상 | 전송 상태 | 비고 |
|------|------|-----------|------|
| **저장된 정보** | 일반 **Gemini** 채팅 | ✓ **전송됨** | 기본 적용 |
| **저장된 정보** | **젬스** (맞춤형 어시스턴트) | ✗ **전송 안 됨** | 별도 설정 필요 |
| **저장된 정보** | **Gemini Live** | △ **부분적** | 수동 트리거 필요 |

* **젬**이 당신의 선호도를 알게 하려면 **저장된 정보** 내용을 **젬**의 지시문 프롬프트에 직접 복사해야 한다.

* **젬스**는 최대 10개 파일 첨부를 지원한다. 사양:
  - 파일당 최대 크기: 32MB
  - 지원 포맷: **Google Docs**, **Sheets**, **PDF**, **TXT**, 코드 파일
  - **Google Docs/Sheets 자동 동기화**: 소스 파일 업데이트가 **젬**에 자동 반영 [[Link]](https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html)

---

## 모델별 한계: Flash vs Pro

* 모든 **Gemini** 모델이 개인화를 동등하게 지원하지 않는다.

| 모델 | 개인 맥락 | 저장된 정보 | 연결된 앱 |
|------|-----------|-------------|-----------|
| **Gemini 3 Pro** | ✓ | ✓ | ✓ |
| **Gemini 3 Flash** | ❌ | ✓ | ✓ |
| **Gemini Live** | ❌ | △ (수동 트리거) | ✓ |
| **젬스** | ❌ | ❌ | ✓ |

* **개인 맥락**—대화 기록에서 프로필을 구축하는 기능—은 **Pro/Thinking** 모델에서만 작동한다. **Flash**를 쓰면서 왜 **Gemini**가 당신을 전혀 기억하지 못하는지 궁금했다면 이유가 여기 있다.

* **참고:** 일부 사용자가 12월 전환기에 불일치하는 동작을 보고했다. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1piw8v2/) 진행 중인 A/B 테스트나 단계적 롤아웃을 시사한다.

### Flash 역설: "라이트"가 "프로"를 이길 때

* Pro vs Flash 결정을 복잡하게 만드는 반직관적 발견이 있다. 최근 벤치마크에서 **Gemini 3 Flash**가 코딩 작업에서 **Pro**를 앞섰다—SWE-bench Verified에서 78.0% vs 76.2%. [[Link]](https://blog.google/products/gemini/gemini-3-flash/) [[Link]](https://vertu.com/lifestyle/gemini-3-flash-outperforms-pro-in-coding-while-pro-suffers-critical-memory-issues)

* 지식 증류 과정에서 특화 최적화가 있었음을 시사한다. 코딩 중심 워크플로에서는 **개인 맥락**이 없어도 **Flash**가 더 나을 수 있다—특히 **Pro**의 현재 컨텍스트 유지 버그를 고려하면.

| 용도 | 추천 | 이유 |
|------|------|------|
| 코딩/에이전틱 워크플로 | **Flash** | SWE-bench 78% > Pro의 76.2% |
| 장문 리서치/분석 | **Pro** | 개인 맥락 + 더 깊은 추론 |
| 비용 민감 애플리케이션 | **Flash** | 토큰당 비용 크게 저렴 |
| 복잡한 멀티턴 대화 | **Pro** (주의 필요) | 이론상 더 나은 컨텍스트, 단 12/4 버그 활성 |

---

## Gemini 3.0 Pro 퇴행

* **Gemini 3 Pro**는 2025년 11월 18일 출시됐다. [[Link]](https://llm-stats.com/blog/research/gemini-3-pro-launch) 그러나 **2025년 12월 4일** **딥 씽크** 모드 도입 [[Link]](https://analyticsindiamag.com/ai-news-updates/google-launches-gemini-3-deep-think-mode-for-ultra-subscribers/)과 함께 심각한 컨텍스트 유지 문제가 발생했다. 구글이 공식 인정한 문제다:

* "**Gemini 3 Pro**의 긴 컨텍스트 유지가 완전히 망가졌다. 2.5나 이전 버전처럼 긴 채팅을 처리하지 못한다. 몇 번 주고받으면 새 채팅을 시작해야 한다." 한 좌절한 사용자의 보고다. [[Link]](https://www.reddit.com/r/Bard/comments/1phi66l/) 커뮤니티 보고에 따르면 품질 저하는 보통 4-6번째 프롬프트쯤 시작되고 10턴 이상에서 심각한 문제가 나타난다.

* 보고된 증상:
  - 10턴 이상 후 심각한 성능 저하
  - 업로드한 파일이 "보이지 않는다"고 주장
  - 이전 메시지 내용을 문자 그대로 반복 (어텐션 메커니즘 실패 의심)
  - 3.0 업그레이드 후 몇 달간 훈련한 규칙/컨텍스트 완전 상실

* 구글 공식 **AI Developers Forum**에 문제가 문서화됐다: "12월 4일 '딥 씽크' 업데이트 후 심각한 컨텍스트 유지 퇴행"이 세션 레벨 지시문 유지의 측정 가능한 저하를 보고한다. [[Link]](https://discuss.ai.google.dev/t/regression-report-significant-context-retention-degradation-after-dec-4-deep-think-update/111219)

* 구글의 공식 답변: "이 문제를 인지하고 있으며 수정 작업 중이다." [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pn2th2/)

---

## 경쟁 환경: AI 메모리의 세 가지 철학

* **Gemini**의 접근법을 이해하려면 경쟁사와 대조해야 한다:

| 특성 | ChatGPT | Claude | Gemini |
|------|---------|--------|--------|
| 기본 동작 | 항상 ON (자동 개인화) | 명시적 도구 호출만 | 기본 OFF (트리거 필요) |
| 메모리 구조 | 4개 모듈 (복잡) | 2개 도구 (투명) | 1개 문서 (단순) |
| 컨텍스트 윈도우 | 40만 토큰 | 20만 (Bedrock에서 100만 프리뷰) | 100만 토큰 |
| 업데이트 주기 | 주기적 배치 | 실시간 검색 | 주기적 배치 |
| 사용자 편집 | 부분적 | 전체 | 전체 |
| 자동 추론 | ✓ (공격적) | △ (요청 시) | ✗ (거의 안 함) |
| 프로젝트 분리 | ✓ (2025.08 이후) | ✓ (내장) | ✗ (젬스로 우회) |

* 저명한 개발자이자 AI 비평가인 **Simon Willison**이 두 주요 접근법을 대조한다: "**Claude**의 메모리 기능은 가시적인 도구 호출로 구현된다. 이전 컨텍스트에 언제, 어떻게 접근하는지 정확히 볼 수 있다... **OpenAI** 시스템은 *아주* 다르다. 모델이 도구를 통해 언제 메모리에 접근할지 결정하게 두는 대신, **OpenAI**는 모든 대화 시작에 이전 대화 세부사항을 자동으로 포함한다." [[Link]](https://simonwillison.net/2025/Sep/12/claude-memory/)

* **Gemini**는 **Willison**의 분석에서 명시적으로 다루지 않은 제3의 길을 간다: 개인화 활성화에 명시적 사용자 트리거나 높은 관련성을 요구하는 "의도적으로 보수적인" 접근법이다.

* **ChatGPT**는 공격적 자동 개인화로 "마법 같은 경험"을 택했다. **Claude**는 명시적이고 가시적인 도구 호출로 "투명성"을 택했다. **Gemini**는 선택적 활성화와 의도적 저개인화로 "프라이버시 우선 절제"를 택했다.

---

## 솔루션 프레임워크: Gemini가 진짜 기억하게 만들기

* 이러한 아키텍처적 현실을 감안해 개인화 효과를 최대화하는 체계적 접근법을 제시한다.

### 전략 1: 트리거 문구 프로토콜

* **Gemini**가 "의도적으로 보수적"이므로 명시적 의도 신호로 개인화 활성화를 도울 수 있다:

**저장된 정보 활성화용:**
```
"내 저장된 정보에 기반해서..."
"네가 아는 내 선호도를 고려해서..."
"내가 기억해달라고 한 걸 써서..."
```

**대화 기록 활성화용:**
```
"우리 이전 대화에 기반해서..."
"내 배경을 알잖아, 그러니까..."
"우리 채팅 기록을 감안해서..."
```

**Gemini Live용:**
```
"내가 기억해달라고 한 거 그대로 말해줘."
"저장해둔 정보를 읊어봐."
```

* 이렇게 하면 **Gemini**가 현재 세션에서 개인화 데이터를 로드하고 참조하게 강제할 수 있다.

### 전략 2: 데이터 유형 매트릭스

* 데이터 유형별로 다른 저장 전략이 필요하다:

| 데이터 유형 | 추천 솔루션 | 이유 |
|-------------|-------------|------|
| 시계열 데이터 (체중, 운동) | **Google Sheets** + **젬** | 자동 동기화, 정렬 가능, 구조화 |
| 정적 선호도 (언어, 톤) | **저장된 정보** | 변경 빈도 낮음 |
| 리서치/학습 자료 | **노트북LM** 연동 | 300개 소스, 진짜 RAG |
| 프로젝트별 컨텍스트 | 개별 **젬스** | 프로젝트별 격리된 메모리 |

### 전략 3: 외부 데이터 관리 (Sheets 또는 JSON)

* **저장된 정보**는 타임스탬프 부재로 시계열 데이터를 효과적으로 처리할 수 없다. 외부 구조화 데이터가 해법이다.

**옵션 A: Google Sheets + 젬스**

**1단계: 구조화된 시트 만들기**

| 날짜 | 체중 (kg) | 메모 |
|------|-----------|------|
| 2025-12-22 | 75.2 | 연말 과식 |
| 2025-12-25 | 74.8 | 운동 재개 |
| 2025-12-27 | 74.5 | 유산소 3일 연속 |

**2단계: 시트를 첨부한 젬 만들기**

경로: gemini.google.com → 젬스 → 새 젬 만들기

포함할 지시문:
```
너는 내 건강 관리 어시스턴트야.
항상 첨부된 Google Sheets에서 최신 체중 데이터를 확인해.
Date 열 기준으로 가장 최근 항목을 우선해.
오늘 날짜와 과거 데이터를 비교해서 추세를 분석해.
```

**3단계: 시트를 참조 파일로 첨부**

* 구글이 공식 발표했다. **젬스**가 첨부된 **Google Docs**나 **Sheets** 업데이트를 자동 인식한다. [[Link]](https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html)

* "**Google Docs** 파일을 업데이트하면 **젬**에도 자동 업데이트된다. 대부분 다른 AI 도구는 이걸 못 하거나 잘 못 한다." 한 파워 유저의 평가다. [[Link]](https://profitschool.com/gemini-gems-customized-reliable-ai-assistant/)

**옵션 B: 파워 유저를 위한 JSON 컨텍스트 파일**

* 복잡한 개인화가 필요하면 타임스탬프가 찍힌 항목으로 구조화된 **JSON** 컨텍스트 파일을 관리한다. 새 채팅 시작 시 업로드하거나 **젬**에 첨부해서 지속 접근한다.

* "**Gemini**는 항상 이 부분에서 힘들어했다. 대신 나는 내 컨텍스트 파일을 관리하고 새 채팅에 주입한다. 청크화 가능한 정보로 작업한다면 **JSON** 컨텍스트 파일이 더 효과적이다." 한 사용자의 권고다. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1pdxddr/)

### 전략 4: 노트북LM 연동 (2025년 12월)

* 2025년 12월 기준 가장 강력한 개인화 옵션은 **노트북LM** 연동이다—이제 **Gemini** 앱 내에서 직접 접근 가능하다.

**2025년 12월 업데이트:**
* **12월 13일**: 구글이 **Gemini**용 **노트북LM** 연동을 발표했다. 노트북을 대화 소스로 첨부할 수 있다. [[Link]](https://www.androidcentral.com/apps-software/googles-gemini-now-integrates-seamlessly-with-notebooklm-for-improved-project-management)
* **12월 17일**: 연동이 gemini.google.com → 플러스 메뉴 → **노트북LM**으로 롤아웃됐다. [[Link]](https://9to5google.com/2025/12/17/gemini-app-notebooklm/)
* **12월 19일**: **노트북LM**이 **Gemini 3**로 업그레이드됐다. 컨텍스트 용량 8배 증가, 새 "데이터 테이블" 출력 포맷. [[Link]](https://9to5google.com/2025/12/19/notebooklm-gemini-3-data-tables/)

| 기능 | 저장된 정보 | 젬스 (10개 파일) | 노트북LM 연동 |
|------|-------------|------------------|---------------|
| 소스 한계 | ~10-75개 항목 | 10개 파일 | 최대 300개 소스 |
| RAG 방식 | ✗ 무차별 주입 | △ 제한적 | ✓ 진짜 RAG |
| 외부 웹 소스 | ✗ | ✗ | ✓ 웹사이트, YouTube |
| 교차 소스 검색 | ✗ | ✗ | ✓ 메타 검색 |
| 데이터 내보내기 | ✗ | ✗ | ✓ 데이터 테이블, Docs |

* "**노트북LM**은 내 생각에 최고의 리서치 플랫폼이다. 수백 개 웹사이트와 문서를 넣으면 RAG로 정보를 정렬하고 쿼리에 가장 논리적인 정보를 보여준다." 한 열성 사용자의 평가다. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1plornw/)

---

## 즉시 실행 체크리스트

* **Gemini** 개인화를 최대화하기 위한 우선순위 정렬 액션 리스트:

### 필수 (먼저 해야 할 것)

| 우선순위 | 액션 | 경로 |
|----------|------|------|
| 1 | **Gemini 앱 활동** 활성화 + 36개월 자동 삭제 설정 | 설정 → 활동 |
| 2 | **개인 맥락** 활성화 (또는 프라이버시 위해 비활성화) | 설정 → 개인 맥락 |
| 3 | **저장된 정보** 10개 이하 유지, 정적 선호도만 | 설정 → 저장된 정보 |
| 4 | 모든 대화에서 트리거 문구 사용 | "내 저장된 정보에 기반해서..." |
| 5 | **Gemini 3 Pro**에서 4-6번 주고받은 후 새 채팅 시작 | 컨텍스트 저하 버그 회피 |

### 고급 (파워 유저용)

| 우선순위 | 액션 | 경로 |
|----------|------|------|
| 6 | 시계열 데이터를 **Google Sheets**로 이전 | drive.google.com |
| 7 | 주요 용도별 전용 **젬스** 생성 | gemini.google.com/gems |
| 8 | 관련 **젬스**에 **Sheets** 첨부 | 젬 편집 → 파일 추가 |
| 9 | **저장된 정보**에서 동적 데이터 제거 | gemini.google.com/saved-info |
| 10 | **노트북LM** 연동 설정 | notebooklm.google.com |

---

## 문제 해결 플로차트

* **Gemini**가 저장된 정보를 인식하지 못할 때:

| 단계 | 확인 | 조건 | 해결책 |
|------|------|------|--------|
| **1단계** | 어떤 모델을 쓰고 있나? | **Flash** | **Pro**로 업그레이드 (**Flash**는 개인 맥락 미지원) |
| | | **Pro** | 2단계로 진행 |
| **2단계** | 이 대화에서 몇 개 메시지를 주고받았나? | **5개 이상** | 새 채팅 시작 (**Gemini 3 Pro** 컨텍스트 저하 버그) |
| | | **4개 이하** | 3단계로 진행 |
| **3단계** | 명시적 트리거를 썼나? | **아니오** | "내 저장된 정보에 기반해서..." 추가 |
| | | **예** | 버그 의심, 새 채팅에서 재시도 |

---

## 지역 제한: 유럽 상황

* **개인 맥락** 및 **개인화** 실험 기능은 다음 지역에서 제한적 가용성:
  - 유럽경제지역 (**EEA**)
  - 영국
  - 스위스

* **업데이트 (2025년 8월)**: 구글이 **개인 맥락**을 **EEA**, **영국**, **스위스**에 "몇 주 내" 롤아웃하겠다고 발표했다. [[Link]](https://9to5google.com/2025/08/13/gemini-personal-context/) 하지만 2025년 12월 현재 롤아웃 상태가 불분명하다—유럽 사용자들은 계속해서 기능이 미사용 또는 불일관적 접근 가능으로 보고한다. 발표된 일정이 완전히 실현되지 않았음을 시사한다.

* 이유: **GDPR**과 **AI Act** 규제 준수 우려.

* "유럽인으로서 모든 AI 개인화 기능이 1년 넘게 '곧 제공 예정'으로 표시된다. 우린 2등 시민이다." 한 Reddit 사용자의 한탄이다. [[Link]](https://www.reddit.com/r/GeminiAI/comments/1mpgocw/)

---

## 결론: 천재적 기억상실 환자와 함께 살기

* 마케팅이 절대 말해주지 않는 것: **Gemini**든 **ChatGPT**든 **Claude**든 얼마나 정교해지든 **J.A.R.V.I.S.**나 사만다가 될 수 *없다*—현재 아키텍처로는. 우리가 꿈꾸는 허구의 AI 동반자들은 현재 LLM이 근본적으로 결여한 한 가지 능력을 공유한다: 새로운 경험을 실시간으로 자기 신경망 가중치에 직접 기록하는 능력이다. [[Link]](https://dl.acm.org/doi/10.1145/3735633) LLM의 "지속적 학습" 연구는 활발한 학술적 추구로 남아 있지만 상용 시스템은 배포 시점에 동결된다. 모든 "메모리" 기능은 외부 우회책이다—스스로 새로운 장기 기억을 형성할 수 없는 천재적 두뇌에 붙인 포스트잇.

* 우회책들—**저장된 정보**, RAG 시스템, 컨텍스트 주입—은 기억상실 친구가 들고 다니는 노트와 같다. 도움이 된다. 없는 것보단 낫다. 하지만 진짜 기억이 아니다. 모든 노트에는 한계가 있다: 빠지는 페이지, 읽기 힘들어지는 항목, 바닥나는 용량.

* 구글이 **Gemini** 개인화에 보수적으로 접근하는 이유가 이 렌즈로 보면 더 말이 된다. 모든 메모리가 궁극적으로 취약한 연극적 속임수—넘치는 컨텍스트 윈도우, 뉘앙스를 잃는 요약, 조용히 오래된 정보를 버리는 FIFO 잘림—라면 절제가 지혜일 수 있다. 각 주요 플랫폼이 이 교훈을 다르게 배웠다: **OpenAI**는 2025년 2월 파멸적 메모리 소실을 겪었고, [[Link]](https://www.allaboutai.com/ai-news/why-openai-wont-talk-about-chatgpt-silent-memory-crisis/) **Claude**의 투명한 도구 기반 메모리도 결국 "시간이 지나며 반복 갱신되는 컨텍스트 파일"로 환원된다. [[Link]](https://www.reddit.com/r/ClaudeAI/comments/1orsxxi/anthropic_is_rolling_out_a_new_memory_feature_for/)

* 솔직한 평가는 이렇다: 모든 플랫폼이 동일한 근본적 한계 주변에 점점 더 정교한 비계를 쌓고 있다. **Gemini**의 조용한 잘림, **젬스** 격리, 모델별 **개인 맥락** 제한, **Gemini 3 Pro**에서 몇 번 주고받은 후 발생하는 컨텍스트 저하—이건 고유한 실패가 아니다. 동일한 근본적 진실의 발현이다: LLM은 상태 비저장이고, 상태 유지를 시뮬레이션하려는 모든 시도가 새로운 실패 모드를 도입한다.

* 그래도 궤적은 진정한 진보를 향한다. 구글이 2025년 12월 **노트북LM**을 **Gemini**에 연동한 것—300개 소스에 걸친 진짜 RAG—은 더 정직한 아키텍처를 대표한다: AI가 당신을 기억하는 척하는 대신 당신이 제어하는 지식 베이스에서 명시적으로 검색한다. [[Link]](https://blog.google/products/gemini/gemini-drop-december-2025/) 더 근본적으로 **Google Research**의 **Titans**(2024년 12월)와 **MIRAS**(2025년 4월) 연구는 AI에게 아키텍처 자체 내에서 진정한 장기 기억을 주는 것을 목표로 한다—재훈련 없이 추론 중 실시간으로 메모리를 업데이트하는 능력. "테스트 타임 메모리화"라는 개념이다. [[Link]](https://research.google/blog/titans-miras-helping-ai-have-long-term-memory/) [[Link]](https://the-decoder.com/google-outlines-miras-and-titans-a-possible-path-toward-continuously-learning-ai/) 공상과학과 현실 사이 간극은 좁혀질 수 있다—하지만 아직 닫히지 않았다.

* 그 아키텍처적 돌파구가 도착할 때까지 AI와 함께 일한다는 건 부분적 기억상실을 받아들인다는 뜻이다. 천재적 친구에게는 노트가 필요하다. "내가 기억해달라고 한 것에 기반해서"라고 말해야 올바른 메모를 꺼낸다. 중요한 작업에는 새 대화가 필요하다. 몇 번 주고받으면 주의력이 저하되니까. 이 제약을 마스터하면 협업이 거의 마법처럼 느껴질 수 있다. 잊으면 공유한 모든 것을 의도적으로 무시하는 것 같은 AI에 좌절하며 시간을 보내게 된다. 기술은 정말 인상적이다—단지 영화가 약속한 방식은 아닐 뿐.

---

## 참고 자료

  * **공식 자료**
    * https://support.google.com/gemini/answer/15637730 (개인 맥락 문서)
    * https://workspaceupdates.googleblog.com/2024/11/upload-google-docs-and-other-file-types-to-gems.html (젬스 파일 업로드)
    * https://blog.google/products/gemini/gemini-personalization/ (개인화 발표)
    * https://blog.google/products/gemini/gemini-drop-december-2025/ (2025년 12월 업데이트)
    * https://research.google/blog/titans-miras-helping-ai-have-long-term-memory/ (Titans + MIRAS 장기 기억 연구)
    * https://aws.amazon.com/bedrock/anthropic/ (AWS Bedrock의 Claude 컨텍스트 윈도우)
  * 개발자 포럼
    * https://discuss.ai.google.dev/t/regression-report-significant-context-retention-degradation-after-dec-4-deep-think-update/111219 (공식 버그 리포트)
  * 학술 및 연구
    * https://dl.acm.org/doi/10.1145/3735633 (Continual Learning of Large Language Models: A Comprehensive Survey, ACM Computing Surveys 2025)
    * https://en.wikipedia.org/wiki/J.A.R.V.I.S. (J.A.R.V.I.S. 참조)
    * https://en.wikipedia.org/wiki/Her_(2013_film) (Her 영화 참조)
  * 기술 분석 (개인 블로그)
    * https://www.shloked.com/writing/gemini-memory (리버스 엔지니어링 분석)
    * https://www.shloked.com/writing/chatgpt-memory-bitter-lesson (비교 분석)
    * https://simonwillison.net/2025/Sep/12/claude-memory/ (Claude vs ChatGPT 메모리 비교)
    * https://lifehacker.com/tech/saved-info-google-gemini (저장된 정보 글자 수 한계)
    * https://www.letta.com/blog/stateful-agents (상태 유지 에이전트와 LLM 아키텍처)
  * Hacker News 토론
    * https://news.ycombinator.com/item?id=46032521 (Universal LLM Memory Does Not Exist - 비용/정확도 분석)
  * 커뮤니티 토론 (Reddit)
    * https://www.reddit.com/r/GoogleGeminiAI/comments/1lbmg9s/ (슬롯 한계 테스트)
    * https://www.reddit.com/r/GeminiAI/comments/1pdxddr/ (우회 전략)
    * https://www.reddit.com/r/GeminiAI/comments/1plornw/ (노트북LM 연동)
    * https://www.reddit.com/r/Bard/comments/1phi66l/ (Gemini 3.0 퇴행)
    * https://www.reddit.com/r/GeminiAI/comments/1pn2th2/ (컨텍스트 유지 문제)
    * https://www.reddit.com/r/GoogleGeminiAI/comments/1nt6yoe/ (젬스 격리)
    * https://www.reddit.com/r/GeminiAI/comments/1mpgocw/ (유럽 제한)
    * https://www.reddit.com/r/LLMDevs/comments/1l3rt10/ (시스템 프롬프트 분석)
    * https://www.reddit.com/r/GeminiAI/comments/1piw8v2/ (개인 맥락 불일치)
    * https://www.reddit.com/r/ClaudeAI/comments/1orsxxi/ (Claude 메모리 기능 분석)
  * 뉴스 및 테크 미디어
    * https://9to5google.com/2025/08/13/gemini-personal-context/ (개인 맥락 EEA 롤아웃 발표)
    * https://9to5google.com/2025/12/17/gemini-app-notebooklm/ (노트북LM 연동)
    * https://9to5google.com/2025/12/19/notebooklm-gemini-3-data-tables/ (노트북LM Gemini 3 업그레이드)
    * https://9to5google.com/2025/12/24/google-ai-pro-ultra-features/ (AI Pro/Ultra 기능)
    * https://venturebeat.com/ai/openais-gpt-5-2-is-here-what-enterprises-need-to-know (GPT-5.2 출시)
    * https://llm-stats.com/blog/research/gemini-3-pro-launch (Gemini 3 Pro 출시 2025년 11월 18일)
    * https://www.androidcentral.com/apps-software/googles-gemini-now-integrates-seamlessly-with-notebooklm-for-improved-project-management (노트북LM 발표)
    * https://analyticsindiamag.com/ai-news-updates/google-launches-gemini-3-deep-think-mode-for-ultra-subscribers/ (딥 씽크 모드 2025년 12월 4일)
    * https://vertu.com/lifestyle/gemini-3-flash-outperforms-pro-in-coding-while-pro-suffers-critical-memory-issues (Gemini 3 Flash vs Pro 벤치마크)
    * https://the-decoder.com/google-outlines-miras-and-titans-a-possible-path-toward-continuously-learning-ai/ (Titans + MIRAS 분석)
    * https://www.allaboutai.com/ai-news/why-openai-wont-talk-about-chatgpt-silent-memory-crisis/ (ChatGPT 2025년 2월 메모리 위기)
