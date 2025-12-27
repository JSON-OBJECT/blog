# 컨텍스트 로트 가이드: Claude Code가 산만해지는 것을 막는 법

## 서론

* "처음 10단계는 천재적인데, 컨텍스트 윈도우가 포화되면 에이전트가 그냥... 표류한다." **Reddit** 사용자의 이 관찰은 **Claude Code** 실무자들이 **컨텍스트 로트(Context Rot)**라고 부르는 현상을 정확히 포착한다. 긴 세션 동안 **AI** 코딩 에이전트가 정보 회상 능력과 일관된 의사결정 능력을 점진적으로 잃어가는 현상이다. [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/agents_turn_into_goldfish_after_50_steps_how_are/)

* 커뮤니티는 이를 "금붕어 증후군"이라고 부른다. 처음 몇 번의 대화에서는 뛰어난 기억력을 보여주다가, 어느 순간부터 파일 경로를 잊고, 존재하지 않는 모듈에서 import하고, 몇 분 전에 내린 결정을 뒤집기 시작한다. **Claude Code**의 버그가 아니다. **대규모 언어 모델(LLM)**의 근본적인 아키텍처 제약이다.

* 2025년 12월 현재, 은탄환은 없다. 대신 다양한 엔지니어링 접근법의 생태계가 성장하고 있다. **Anthropic**의 공식 **컨텍스트 컴팩션(Context Compaction)**과 **서브에이전트** 아키텍처부터, 커뮤니티가 개발한 **Beads**와 **Memory MCP** 서버까지. 숙련된 엔지니어들은 시행착오를 통해 각자의 답을 찾고 있고, 업계는 새로운 학문 영역인 **컨텍스트 엔지니어링**으로 수렴하고 있다.

## 컨텍스트 로트의 해부학

### 컨텍스트 로트란 정확히 무엇인가?

* **컨텍스트 로트**는 입력 토큰 수가 증가함에 따라 **LLM**의 성능이 점진적으로 저하되는 현상이다. [[Link]](https://research.trychroma.com/context-rot) 이 용어는 2025년 6월 **Hacker News**에서 처음 등장했고, 2025년 7월 **Chroma Research**의 기술 보고서에서 학술적으로 정립됐다.

* 이 현상은 여러 관련 증상으로 나타난다:

| 용어 | 정의 |
|------|------|
| **컨텍스트 로트** | 입력 토큰 증가에 따른 성능 저하 |
| **컨텍스트 드리프트** | 장시간 세션에서 에이전트가 원래 목표에서 이탈 |
| **Lost in the Middle** | 컨텍스트 중간에 위치한 정보 검색 실패 |
| **금붕어 증후군** | 커뮤니티 은유: "3초 전 일을 잊어버림" |

### 수학적 현실: O(n²) 어텐션 복잡도

* 근본 원인은 **트랜스포머** 아키텍처 자체에 있다. [[Link]](https://arxiv.org/abs/2209.04881) 셀프 어텐션은 모든 토큰 간의 쌍별 관계를 계산해야 하므로, 토큰 수 n에 대해 O(n²) 계산 복잡도가 발생한다.

* 200K 토큰 컨텍스트 윈도우의 경우, 400억 개의 쌍별 관계를 처리해야 한다. [[Link]](https://d2l.ai/chapter_attention-mechanisms-and-transformers/self-attention-and-positional-encoding.html) **Anthropic**의 엔지니어링 문서는 이 제약을 명시적으로 인정한다:

> "LLM은 대량의 컨텍스트를 파싱할 때 사용하는 '어텐션 예산'을 가진다. 새로운 토큰이 도입될 때마다 이 예산이 일정량 소모된다."
> — [[Link]](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) **Anthropic** 엔지니어링 블로그 (2025년 9월)

### Chroma Research: 실증적 증거

* **Chroma Research**의 2025년 7월 연구는 **GPT-4.1**, **Claude 4**, **Gemini 2.5**, **Qwen3**을 포함한 18개 주요 **LLM**을 테스트했다. [[Link]](https://research.trychroma.com/context-rot) 결과는 엄중했다:

| 발견 | 시사점 |
|------|--------|
| 비균일 성능 저하 | 모든 모델이 입력 길이 증가에 따라 성능 저하 |
| Needle-Question 의미적 거리 | 질문과 답변의 의미적 차이가 클수록 성능이 더 빨리 하락 |
| 방해 정보 영향 | 무관한 정보가 비선형적 성능 감소 유발 |
| 헤이스택 구조의 중요성 | 논리적으로 구조화된 텍스트와 뒤섞인 텍스트의 성능 차이 |

* 결정적으로, 이 연구는 전통적인 **Needle-in-a-Haystack(NIAH)** 벤치마크가 실제 성능을 과대평가한다는 사실을 밝혔다. 단순한 어휘 매칭만 테스트하고, 복잡한 추론 작업은 테스트하지 않기 때문이다.

### "Lost in the Middle" 문제

* **Stanford** 연구진이 2023년 이 현상을 처음 문서화했다. [[Link]](https://arxiv.org/abs/2307.03172) **LLM**은 U자형 어텐션 패턴을 보인다: 컨텍스트 윈도우의 시작과 끝에서는 정보를 잘 회상하지만, 중간 내용에는 어려움을 겪는다.

```
┌─────────────────────────────────────────────────────────┐
│  시작 부분      │     중간 부분      │      끝 부분       │
│  (높은 회상률)   │   (낮은 회상률)    │  (높은 회상률)     │
└─────────────────────────────────────────────────────────┘
```

* 긴 **Claude Code** 세션에서 초반에 준 지시사항(**CLAUDE.md**에 저장됨)과 가장 최근 요청은 잘 처리되지만, 그 사이의 모든 내용은 모델이 접근하기 점점 어려워진다.

## Claude Code에서 컨텍스트 로트가 나타나는 방식

* **Reddit** 사용자들이 장시간 세션 후 발생하는 구체적인 실패 패턴을 기록했다:

| 증상 | 사용자 설명 |
|------|-------------|
| 순환 편집 | "**Redis**로 최적화했다가, 다음 세션에서 **Memcached**로 바꿨다가, 다시 **Redis**로" [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/) |
| 경로 망각 | "5분 전에 생성한 파일 경로를 잊고, 존재하지 않는 모듈에서 import" [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/) |
| 설정 왔다갔다 | "연속 변경에서 포트 3000 → 3001 → 3000" |
| 지시사항 이탈 | "컨텍스트 후반에 **CLAUDE.md** 지시를 완전히 무시" |
| 조기 완료 선언 | "절반만 끝났는데 '프로젝트 완료' 선언" |

* 한 사용자의 관찰이 커뮤니티에서 화제가 됐다: "**Claude Code**는 금붕어의 기억력과 10배 개발자의 자신감을 가졌다." [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1mo15er/claude_code_has_the_memory_of_a_goldfish_and_the/)

## Anthropic의 공식 솔루션

### 1. 컨텍스트 컴팩션

* **Claude Code**는 컨텍스트 한계에 근접할 때 자동 컨텍스트 컴팩션을 실행한다. [[Link]](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) 시스템이 대화 이력을 요약하면서 다음 항목을 보존한다:

  - 아키텍처 결정사항
  - 미해결 버그
  - 구현 세부사항
  - 최근 접근한 파일 (일반적으로 마지막 5개)

* `/compact [instructions]` 명령으로 수동 컴팩션을 트리거해 보존 내용을 제어할 수 있다. 한계: 공격적인 컴팩션은 미묘하지만 중요한 컨텍스트를 잃을 수 있다.

### 2. 컨텍스트 편집 (2025년 9월)

* **Anthropic**이 **API**에 프로그래매틱 컨텍스트 편집 기능을 도입했다. [[Link]](https://platform.claude.com/docs/en/build-with-claude/context-editing) 개발자가 자동 정리 규칙을 설정할 수 있다:

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

* 대화 흐름을 유지하면서 오래된 도구 호출 결과를 삭제할 수 있다. 전체 컴팩션에 비해 외과적 접근법이다.

### 3. 서브에이전트 아키텍처

* **Anthropic**이 복잡한 작업에 권장하는 패턴은 전문화된 서브에이전트에 작업을 위임하는 방식이다. [[Link]](https://platform.claude.com/docs/en/agent-sdk/subagents) 각 서브에이전트는 자체 컨텍스트 윈도우에서 작동하고, 요약된 결과만 메인 오케스트레이터에 반환한다.

```
┌─────────────────────────────────────────────────────┐
│                 메인 오케스트레이터                    │
│            (고수준 계획 + 조율)                       │
└───────────┬─────────────┬─────────────┬─────────────┘
            │             │             │
            ▼             ▼             ▼
      ┌──────────┐  ┌──────────┐  ┌──────────┐
      │ 검색     │  │ 구현     │  │ 테스트   │
      │ 에이전트  │  │ 에이전트  │  │ 에이전트  │
      └──────────┘  └──────────┘  └──────────┘
           ↓             ↓             ↓
      요약           요약           요약
      (1-2K 토큰)    (1-2K 토큰)    (1-2K 토큰)
```

* 핵심 통찰: 서브에이전트가 코드베이스 탐색에 30,000 토큰을 소비하더라도, 정제된 결과 1,500 토큰만 메인 에이전트에 반환된다.

### 4. 장시간 실행 에이전트 하네스 (2025년 11월)

* **Anthropic**의 장시간 실행 에이전트 연구가 4가지 주요 실패 모드와 해당 솔루션을 식별했다. [[Link]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

| 실패 모드 | 솔루션 |
|-----------|--------|
| 원샷팅(모든 것을 한 번에 시도) | 기능 목록 파일 (`passes: true/false` 포함 **JSON** 형식) |
| 컨텍스트 소진 시 미문서화 상태 | Git 커밋 + 진행 파일 필수 |
| 엔드투엔드 테스트 부재 | **E2E** 검증을 위한 브라우저 자동화 |
| 앱 실행 방법 파악에 시간 낭비 | 자동 생성 `init.sh` 스크립트 |

* **Two-Agent Harness** 패턴이 관심사를 분리한다:
  1. **초기화 에이전트**: 환경 설정 (기능 목록, git 저장소, 진행 파일)
  2. **코딩 에이전트**: 세션당 하나의 기능 구현, 진행 상황 커밋

## 커뮤니티 개발 솔루션

### 1. AST 기반 프로젝트 맵 주입

* 가장 기술적으로 우아한 커뮤니티 솔루션은 매 턴마다 **추상 구문 트리(AST)** 맵을 주입하는 방식이다. [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/)

> "AST를 스캔해서 저장소의 압축된 스켈레톤(시그니처와 import만)을 생성하는 로컬 도구를 만들었고, 시스템 프롬프트에 강제 삽입한다."
> — u/Necessary-Ring-6060

* 이 접근법이 **RAG(검색 증강 생성)**보다 여러 장점을 제공한다:
  - **결정론적**: 벡터 검색 불확실성 없음
  - **구조적 정확성**: 시맨틱 검색이 놓치는 코드 계층 구조 보존
  - **환각 방지**: 에이전트가 실제 맵을 보므로 기억할 필요 없음

### 2. Beads: 에이전트 우선 이슈 트래커

* **Steve Yegge**의 **Beads**가 다중 세션 컨텍스트 보존을 위한 인기 솔루션으로 부상했다. [[Link]](https://github.com/steveyegge/beads) **GitHub Issues**와 달리 **Beads**는 구현 노트—결정, 차단 요소, 에이전트가 컨텍스트를 재구성하는 데 필요한 진행 상황—전용으로 설계됐다.

```bash
bd init                    # 프로젝트에서 초기화
bd create "Implement auth" # 작업 생성
bd update auth-001 --notes "COMPLETED: JWT. NEXT: Rate limiting"
```

* **Reddit**의 3주 사용 보고서: [[Link]](https://www.reddit.com/r/ClaudeCode/comments/1ov1z94/update_i_tried_beads_for_3_weeks_after_asking/)

> "기억상실이 사라졌다. 매번 컴팩션 후 컨텍스트를 다시 설명하느라 상당한 시간을 썼다. 이제 Claude가 bead 노트를 읽어서 전체 컨텍스트를 자동으로 재구성한다."
> — u/lakshminp

### 3. 투탭 Claude 시스템

* 일부 실무자들은 서로 다른 관심사를 위해 별도의 **Claude** 인스턴스를 유지한다:

| 윈도우 1 (리서치/QA) | 윈도우 2 (개발) |
|----------------------|-----------------|
| 버그 분석 | 구현 |
| 파일/라인 식별 | 코드 작성 |
| 컨텍스트의 80-90% 사용 | 집중 실행 |

* 윈도우 1의 결과가 윈도우 2에 정제된 실행 가능한 지시사항으로 전달된다.

### 4. /clear + Plan 파일 전략

* 추가 도구 없이 가장 접근하기 쉬운 전략:

1. 시작 전 체크리스트가 포함된 `PLAN.md` 생성
2. 작업 진행에 따라 완료 항목 체크
3. `/clear` 실행해 컨텍스트 초기화
4. "Continue with PLAN.md"로 재개

> "정확히 무엇을 해야 하는지 단계별 지시사항을 줘야 하고, 각 단계에서 결과를 확인해야 한다. 그리고 각 작업이 완료되고 작동 테스트가 끝나면 /clear."
> — u/TotalBeginnerLol [[Link]](https://www.reddit.com/r/ClaudeCode/)

### 5. Memory MCP 서버

* **Model Context Protocol(MCP)** 생태계에서 여러 메모리 중심 서버가 등장했다:

| 도구 | 핵심 기능 |
|------|-----------|
| **Serena MCP** | 시맨틱 코드 검색 + 언어 서버 통합 [[Link]](https://github.com/oraios/serena) |
| **Basic Memory MCP** | 로컬 마크다운 기반 영구 메모리 |
| **Heimdall MCP** | "Remember context about X" 명령 인터페이스 |
| **a24z-Memory** | 파일 앵커 기반 노트 시스템 |

### 6. Superpowers 플러그인: 종합 솔루션

* **Jesse Vincent**(obra)의 **Superpowers** 플러그인은 여러 컨텍스트 관리 기법을 통합 워크플로우 시스템으로 묶는다. [[Link]](https://github.com/obra/superpowers) 단편적인 솔루션과 달리, 초기 브레인스토밍부터 머지된 **PR**까지 완전한 라이프사이클을 제공한다.

```bash
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

* **핵심 컨텍스트 관리 기능**:
  - **서브에이전트 주도 개발**: 각 작업이 격리된 컨텍스트에서 실행되고, 요약된 결과만 반환
  - **플랜 파일 아키텍처**: 세션 독립적 연속성을 위한 `docs/plans/YYYY-MM-DD-<feature>.md` 자동 생성
  - **자동 컨텍스트 인계**: 새 세션이 플랜 파일을 읽어 재개—수동 컨텍스트 재구성 불필요
  - **TDD 강제**: RED-GREEN-REFACTOR 사이클이 선택이 아닌 필수가 됨

* 세션 독립적 워크플로우가 특히 주목할 만하다:

```bash
# 세션 1: 계획하고 저장
> /superpowers:brainstorm Implement rate limiting
# 설계가 docs/plans/2025-12-26-rate-limiting.md에 저장 → 자동 커밋

# 세션 2 (얼마 후든): 재개
> Read docs/plans and continue
# Superpowers가 executing-plans 스킬 자동 호출
```

* **Django** 공동 창시자 **Simon Willison**이 이 접근법을 지지했다:

> "**Jesse**는 내가 아는 가장 창의적인 코딩 에이전트 사용자 중 한 명이다. 그가 공유한 것을 탐구하는 데 시간을 투자할 가치가 충분하다." [[Link]](https://simonwillison.net/2025/Oct/10/superpowers/)

* 토큰 효율성이 상당하다—핵심 부트스트랩이 2,000 토큰 미만으로 로드되고, 무거운 작업은 메인 컨텍스트를 오염시키지 않는 서브에이전트에 위임된다. [[Link]](https://bsky.app/profile/s.ly)

## 토큰 경제학: 컨텍스트 로트와 싸우는 비용

* **Anthropic**의 자체 데이터가 에이전트 패턴의 상당한 토큰 오버헤드를 보여준다: [[Link]](https://www.constellationr.com/blog-news/insights/anthropics-multi-agent-system-overview-must-read-cios)

| 상호작용 유형 | 토큰 배수 |
|---------------|-----------|
| 표준 챗봇 | 1x (기준) |
| 단일 에이전트 | ~4x |
| 멀티 에이전트 시스템 | ~15x |

* 멀티 에이전트 아키텍처는 **컨텍스트 로트**에 효과적이지만, 단순 채팅보다 약 15배 많은 토큰을 소비한다. **Claude Pro/Max** 구독자의 경우 사용량 한도가 급격히 소진될 수 있다.

## 실용적 권장사항

### 작업 범위에 따른 전략 선택

| 시나리오 | 권장 접근법 |
|----------|-------------|
| 단순 기능 (1-2시간) | `/clear` 빈번하게 사용 |
| 다중 세션 프로젝트 | **Beads** + 진행 파일 |
| 대규모 리팩토링 | 서브에이전트 아키텍처 |
| 복잡한 디버깅 | 투탭 시스템 |
| 반복 워크플로우 | **CLAUDE.md** + Hooks |

### 피해야 할 안티패턴

| 피하기 | 대신 하기 |
|--------|-----------|
| 모든 작업에 단일 긴 세션 | 완료된 단위마다 `/clear` |
| 큰 텍스트 블록 붙여넣기 | 파일 읽기 도구 사용 |
| 모호한 지시사항 ("이거 고쳐") | 파일, 라인, 정확한 문제 명시 |
| 자동 컴팩션에만 의존 | `/compact [instructions]` 수동 실행 |
| **CLAUDE.md** 과부하 | 보편적이고 최소한의 가이드라인만 유지 |

### 심플 이즈 베스트: Superpowers에 맡기기

* 최소한의 도구 오버헤드를 선호하는 실무자들은 체크리스트와 상태 추적이 포함된 **PLAN.md** 파일을 수동으로 만들고 싶어한다. 하지만 더 우아한 솔루션이 있다: `Superpowers`가 이미 실전 검증된 워크플로우로 이 패턴을 구현한다.

* 플랜 파일을 수동으로 관리하는 대신, **Superpowers**가 완전한 인프라를 제공한다: [[Link]](https://github.com/obra/superpowers)

| 수동 접근법 | Superpowers 동등 기능 |
|-------------|----------------------|
| 수동으로 `PLAN.md` 생성 | `/superpowers:write-plan`이 `docs/plans/YYYY-MM-DD-<feature>.md` 자동 생성 |
| 체크리스트 항목 직접 작성 | 에이전트가 명확화 질문 후 정확한 파일 경로 포함 2-5분 작업 산출 |
| 작업 진행에 따라 상태 업데이트 | `executing-plans` 스킬이 완료 자동 추적 |
| `/clear` 실행 기억하기 | 서브에이전트 아키텍처가 컨텍스트 격리 본질적으로 처리 |
| "Continue with PLAN.md"로 재개 | 새 세션: "Read docs/plans and continue" → 자동 재개 |

* 워크플로우가 놀랍도록 단순해진다:

```bash
# 세션 1: 설계와 계획
> /superpowers:brainstorm Add user authentication to my app
# 질문에 하나씩 답변 → 설계가 docs/plans/에 저장 → 자동 커밋

# 세션 2 (몇 시간 또는 며칠 후): 재개
> Read docs/plans and continue
# Superpowers가 executing-plans 자동 로드 → 중단한 곳에서 정확히 재개
```

* 단순한 편의성이 아니다—**Anthropic** 연구팀이 장시간 실행 에이전트에 필수적이라고 식별한 **세션 독립적 개발** 패턴과 동일하며, 플러그인으로 구현됐다. [[Link]](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

* 핵심 통찰: 플랜 파일 패턴을 재발명할 필요가 없다. **Superpowers**가 이미 적대적 테스트와 **Claude Code** 실무자들의 실제 사용을 통해 정제했다.

## 결론: 새로운 프론티어로서의 컨텍스트 엔지니어링

* **컨텍스트 로트**는 **AI** 코딩 도구의 흥미로운 변곡점을 나타낸다. 원시 컴퓨팅 파워나 더 큰 컨텍스트 윈도우로 해결할 수 있는 문제가 아니다. **Anthropic** 스스로 "모든 크기의 컨텍스트 윈도우가 컨텍스트 오염과 정보 관련성 문제의 대상이 될 것"이라고 인정한다. [[Link]](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) O(n²) 어텐션 복잡도는 아키텍처적이지, 우연이 아니다.

* 우리가 목격하는 것은 **컨텍스트 엔지니어링**이라는 독립된 학문의 출현이다. **프롬프트 엔지니어링**이 올바른 단어를 조합하는 데 집중했다면, **컨텍스트 엔지니어링**은 다음을 묻는다: "원하는 결과를 극대화하는 최소한의, 최고 신호 토큰 집합은 무엇인가?" 이를 위해 정보 라이프사이클, 세션 경계, 외부 상태 영속성에 대한 사고가 필요하다.

* 아이러니가 풍부하다: **AI** 에이전트가 복잡하고 장시간 실행되는 작업을 수행하게 하려면, 인간 엔지니어링 팀이 수십 년에 걸쳐 개발한 것과 본질적으로 동일한 인프라를 구축하게 된다—이슈 트래커, 진행 파일, 문서화 관행, 인계 프로토콜. "금붕어"는 더 나은 기억력을 얻어서가 아니라, 기록하는 법을 배워서 학습한다.

* 오늘날 단일 정답은 없다. 분야가 활발히 진화하고 있고, **Anthropic**은 분기마다 새 기능을 출시하며, 커뮤니티는 새로운 접근법을 반복 실험한다. 최적의 방법은 프로젝트 복잡도, 개인 워크플로우 선호도, 도구 오버헤드 허용 범위에 따라 다르다. 최소한의 설정으로 종합적인 솔루션을 원한다면 **Superpowers**가 두드러진다—**Anthropic** 연구팀이 권장하는 플랜 파일 패턴, 서브에이전트 아키텍처, 세션 독립적 연속성을 단일 플러그인으로 구현한다. `PLAN.md` 파일을 수동으로 만들거나 컨텍스트 관리 패턴을 재발명할 필요가 없다. 인프라가 이미 존재한다. [[Link]](https://github.com/obra/superpowers)

* **AI** 코딩 에이전트와 함께 번성할 엔지니어는 이 현실을 내면화할 것이다: 컨텍스트 윈도우는 무한 메모리가 아니다—비싸고 성능이 저하되는 작업 메모리다. 의도적으로 관리하는 것은 우회책이 아니라 핵심 역량이다.

## 참고 자료

* **Anthropic Engineering**
  * https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
  * https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
* **Chroma Research**
  * https://research.trychroma.com/context-rot
* 학술 연구
  * https://arxiv.org/abs/2307.03172 (**Stanford** "Lost in the Middle")
  * https://arxiv.org/abs/2209.04881 (Self-Attention Complexity)
* **Claude** 문서
  * https://platform.claude.com/docs/en/build-with-claude/context-editing
  * https://platform.claude.com/docs/en/agent-sdk/subagents
* 커뮤니티 도구
  * https://github.com/steveyegge/beads (**Beads** 이슈 트래커)
  * https://github.com/obra/superpowers (**Superpowers** 플러그인)
  * https://github.com/oraios/serena (**Serena MCP**)
* **Superpowers** 전문가 분석
  * https://simonwillison.net/2025/Oct/10/superpowers/ (**Simon Willison** 추천)
* 커뮤니티 토론 (**Reddit**)
  * https://www.reddit.com/r/ClaudeCode/comments/1pv7ls3/ (원본 "금붕어" 토론)
  * https://www.reddit.com/r/ClaudeCode/comments/1ov1z94/ (**Beads** 3주 리뷰)
