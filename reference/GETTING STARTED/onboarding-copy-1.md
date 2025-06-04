---
title: VerifyVASP Onboarding
excerpt: >-
  본격적인 TravelRule과 VerifyName 연동에 앞서 VerifyVASP 콘솔 가입을 통해 VASP를 등록하고 API Key
  발급받기 위한 절차를 먼저 수행해야 합니다.
deprecated: false
hidden: false
metadata:
  robots: index
---
## VerifyVASP 콘솔 회원가입

아래 절차에 따른 VerifyVASP 콘솔 회원가입을 통해, TravelRule 또는 VerifyName 프로토콜을 연동하기 위한 VASP 온보딩을 완료할 수 있습니다.

<br />

### 1. [VerifyVASP 홈페이지](\[https://www.verifyvasp.com]\(https://www.verifyvasp.com\))의 우측 상단 \[Get Started] 버튼을 클릭하여 콘솔로 이동합니다.

<Image align="center" src="https://files.readme.io/01daa9f786c6b15eb40a8ca98e865271467b0c1a4793dd7f731b8dfd21b2404c-VV_Onboarding.png" />

<br />

### 2. 이메일과 비밀번호를 입력하여 회원가입을 진행합니다.

<Image align="center" src="https://files.readme.io/174061e161e9453bade2be15d12868a6a0304332167f6dba72eda4e6bdf64d04-VV_onboarding_2.png" />

<br />

### 3. 기업 정보를 입력합니다.

VerifyVASP 온보딩을 위해 필수 기업 정보를 모두 입력해야 합니다. 추가 서류가 필요한 경우, VerifyVASP 운영팀 담당자가 ‘Business Contact’ 탭에 입력된 이메일 주소로 개별 연락을 드릴 수 있으므로 반드시 수신 가능한 이메일을 입력하세요.

<Image align="center" src="https://files.readme.io/ce2309555ff44045258a74d903dccae2741ebf73af18779f937a84dbc744f7a9-VV_Onboarding_3.png" />

<br />

### 4. 가입 승인 대기

회원가입 요청이 완료되면 VerifyVASP 운영팀에서 가입 승인 절차를 진행합니다. 승인 과정에서 추가 서류를 요청할 수 있으며, 모든 자료의 검토가 완료되면 가입이 승인됩니다.

자세한 가입 절차는 아래 첨부된 안내 영상을 참고하시기 바랍니다.

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=FusIymjfGdU" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252FFusIymjfGdU%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253DFusIymjfGdU%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252FFusIymjfGdU%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=FusIymjfGdU" providerUrl="https://www.youtube.com/" providerName="YouTube" />

<br />

## VerifyVASP API Key 발급

가입 승인된 VASP는 VerifyVASP 콘솔에서 API Key를 발급받아 Enclave API 호출 시 사용할 수 있습니다. 자세한 API Key 발급 절차는 아래 첨부된 안내 영상을 참고하세요.

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=VK8DT12orSA" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252FVK8DT12orSA%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253DVK8DT12orSA%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252FVK8DT12orSA%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=VK8DT12orSA" providerUrl="https://www.youtube.com/" providerName="YouTube" />

> ❗️ API Key 발급 및 사용시 주의사항
>
> 1. **Secret Key는 발급 시점에만 조회할 수 있습니다.**\
>    발급 화면을 벗어나면 다시 확인할 수 없으므로, 반드시 안전하게 보관하세요.
> 2. **Secret Key를 분실한 경우 API Key를 재발급해야 합니다.**\
>    이 경우 기존에 발급된 API Key는 자동으로 폐기되며, 더 이상 사용할 수 없습니다.
> 3. **발급 받은 API Key를 사용하기 위해서는 Access Key와 Secret Key를 Enclave 서버의 환경 변수로 설정해야 합니다.**\
>    해당 환경 변수 명은 아래와 같습니다. Enclave 서버의 **환경 변수 명은`절대` 변경할 수 없습니다.**
>    1. `VEGA_ALLIANCE_ACCESS_KEY` : 발급받은 Access Key를 입력하세요.
>    2. `VEGA_ALLIANCE_SECRET_KEY` : 발급받은 Secret Key를 입력하세요.