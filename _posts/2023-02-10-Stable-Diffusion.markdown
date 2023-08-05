---
layout: post
title: "Stable Diffusion, 개념 및 사용법 정리"
author: "Taehyeong Lee"
tags: [Stable Diffusion]
---
### Stable Diffusion
  * `Stable Diffusion`은 입력된 텍스트를 이미지로 렌더링해주는 오픈 소스 딥 러닝 생성형 인공지능 모델이다. 경쟁사 대비 **PC** 수준의 컴퓨팅 파워로 충분히 결과물 생성이 가능한 것이 최대 장점으로, 컴퓨터만 있으면 누구든지 무료로 **AI** 이미지를 생성할 수 있다.

### AUTOMATIC1111’s Stable Diffusion Web UI
  * 오픈 소스의 생태계 특성상 정제되지 않은 **Stable Diffusion** 또한 도구들의 집합이라 개발 지식이 없으면 설치부터 쉽지 않다. `A1111 WebUI Easy Installer and Launcher`를 설치하면 **Stable Diffusion**을 실행하는데 필요한 파일들을 자동으로 설치해준다. [[다운로드 링크]](https://github.com/EmpireMediaScience/A1111-Web-UI-Installer)

### 요구 조건
  * **512x512** 해상도의 이미지를 온전하게 생성하기 위해서는 최소 **10GB** 이상의 **VRAM**을 가진 **NVIDIA GeForce RTX 3080** 이상의 그래픽 카드를 요구한다.

### Stable Diffusion Web UI 설치
  * 앞서 소개한 올인원 원클릭 인스톨러인 **A1111 WebUI Easy Installer and Launcher** 없이 직접 수작업으로 설치하는 방법이다. `AUTOMATIC1111 Stable Diffusion Web UI`는 **Stable Diffusion**을 브라우저에서 편리하게 사용할 수 있는 **UI**를 제공한다. **Git**, **Python**이 설치된 상태에서 아래 주소를 로컬에 클로닝한다.

```
$ git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui.git
```

  * 클로닝 후 `webui-user.bat`을 실행하면 로컬에 웹 서버가 기동되고, 브라우저에서 `http://127.0.0.1:7860` 주소로 접속하면 **Stable Diffusion Web UI**가 실행된다.

### Web UI Extension 설치
  * [ControlNet](https://github.com/Mikubill/sd-webui-controlnet.git): **TXT2IMG**의 결과물은 기본적으로 텍스트 프롬프트에 의존하기에 크리에이터가 결과물을 완벽히 통제하지 못한다. 그저 반복적으로 수많은 결과물을 생성하고 그 중에 마음에 드는 것을 찾는 것이 최선이었다. `ControlNet`를 사용하면 내가 원하는 구도의 이미지를 추가로 전달하여 동일한 패턴의 이미지를 생성할 수 있다.
  * [AnimateDiff](https://github.com/continue-revolution/sd-webui-animatediff.git): **TXT2IMG**의 결과물은 기본적으로 정적인 이미지이다. `AnimateDiff`를 사용하면 애니메이션 효과를 줘서 **GIF** 또는 **MP4** 등의 동적인 결과물을 생성할 수 있다. 최근 가장 대중적인 인기를 얻고 있다.

### 원하는 장르의 모델 설치
  * 같은 프롬프트를 입력해도 모델에 따라 결과물이 천차만별이다. 결과물의 완성도를 높이려면 특정 장르에 특화되어 트레이닝된 `Model` 또는 `Checkpoint` 파일을 다운로드해야 한다. 제작사가 직접 제작하여 무료 공개한 **SD 1.5/SDXL 1.0** 베이스 모델을 기반으로 유저들이 제작한 수많은 체크포인트가 존재한다. [Civitai](https://civitai.com)에 회원 가입하면 유저들이 제작한 다양한 모델의 무료 다운로드가 가능하다.
  * 다운로드가 완료된 파일은 `models\Stable-diffusion` 폴더에 복사하고 **Stable Diffusion**의 **Checkpoint** 메뉴에서 해당 파일을 선택한 상태로 이미지를 생성하면 해당 모델을 기반으로 이미지가 생성된다.
  * **SD 1.5** 기반의 인종 묘사를 원하면 [epiCPhotoGasm](https://civitai.com/models/132632?modelVersionId=201259) 체크포인트를 추천한다. [epiCRealismHelper](https://civitai.com/models/110334/epicrealismhelper) 로라와 병행 사용하면 실제 사진과 같은 이미지를 얻을 수 있다. (프롬프트에 `<lora:epiCRealismHelper:1>`, 부정 프롬프트에는 `(epiCNegative:1)` 추가)

### 로라 설치
  * 사진과 같은 결과물을 원할 경우 다음의 네거티브 프롬프트 전용 로라를 추천한다. [EpiCNegative](https://civitai.com/models/89484?modelVersionId=95263), [BadDream](https://civitai.com/models/72437/baddream-unrealisticdream-negative-embeddings), [UnrealisticDream](https://civitai.com/models/72437?modelVersionId=77173), [Bad-Hands-5](https://civitai.com/models/116230?modelVersionId=125849)이다.

### 파라메터 설명
  * `Sampling method`는 결과물을 생성할 방법을 의미하는 알고리즘이다. `DPM++ SDE Karras`를 추천한다.
  * `Sampling steps`는 결과물을 생성할 단계를 의미하는 숫자이다. `25`를 추천하는데 의미는 **25**번 반복하여 결과물을 생성하는 것이다. **1**에 가까울수록 완성도가 떨어지고 **25**를 넘어갈수록 원하지 않는 결과물이 나올 가능성이 높다.
  * `Hires. fix`는 체크시 지정된 해상도의 2배로 생성해준다. 체크를 추천한다.
  * `Denoising strength`는 선명함의 강도를 의미한다. `0.35`과 `0.7` 사이를 추천한다.
  * `Seed`는 결과물을 식별해주는 숫자이다. 동일한 프롬프트라도 기본값인 **-1**에 두고 생성하면 매번 전혀 다른 이미지가 생성된다. 결과물이 생성되면 해당하는 **Seed**도 같이 생성되는데 이 값으로 이미지를 재생성하면 동일한 결과물에 다양한 변경점을 줄 수 있어 매우 유용하다. 예를 들면 동일한 사람을 머리 스타일, 머리색, 인종, 피부색, 입고 있는 옷만 프롬프트로 바꾸어 결과물을 재생성할 수 있다.

### 프롬프트 설명
  * `Positive Prompt`는 결과물에 포함할 단어 목록을 입력한다. 사실적인 결과물을 원하면 아래 프롬프트를 추천한다. (사전에 [Add More Details](https://civitai.com/models/82098/add-more-details-detail-enhancer-tweaker-lora) 임베딩을 설치해야 한다.)

```
<lora:epiCRealismHelper:1>, <lora:more_details:1>
```

  * `Negative Prompt`는 결과물에서 제외할 단어 목록을 입력한다. 사실적인 결과물을 원하면 아래 프롬프트를 추천한다. (사전에 [epiCNegative](https://civitai.com/models/89484?modelVersionId=95263), [BadDream](https://civitai.com/models/72437/baddream-unrealisticdream-negative-embeddings), [UnrealisticDream](https://civitai.com/models/72437?modelVersionId=77173), [Fast Negative Embedding](https://civitai.com/models/71961/fast-negative-embedding-fastnegativev2) 임베딩을 설치해야 한다.)

```
(epiCNegative:1), (BadDream:1), (UnrealisticDream:1), (FastNegativeV2:1)
```

### 참고 글
  * [How to Run Stable Diffusion on Your PC to Generate AI Images](https://www.techspot.com/guides/2590-install-stable-diffusion/)
