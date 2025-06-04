---
title: Onboarding
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

### 2. **Enter your Email and Password to sign up**

<Image align="center" src="https://files.readme.io/174061e161e9453bade2be15d12868a6a0304332167f6dba72eda4e6bdf64d04-VV_onboarding_2.png" />

<br />

### 3. **Enter the corporate information.**

Your VASP must enter the corporate information to participate in the VerifyVASP Alliance. If the VerifyVASP Team requires additional documents, an operator from the VerifyVASP Team can send an email to the address provided in the Business Contact tab. Therefore, you must enter an email address that can accurately receive replies.

<Image align="center" src="https://files.readme.io/ce2309555ff44045258a74d903dccae2741ebf73af18779f937a84dbc744f7a9-VV_Onboarding_3.png" />

<br />

### 4. **Successful request participating in the VerifyVASP Alliance**

The VerifyVASP Operations Team conducts the approval process for VASPs that have completed the registration. During the approval process, the Operations Team may request additional documents. They will grant approval after completing the review of all submitted documents.

You can refer to the attached video to learn how to sign up.

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=FusIymjfGdU" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252FFusIymjfGdU%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253DFusIymjfGdU%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252FFusIymjfGdU%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=FusIymjfGdU" providerUrl="https://www.youtube.com/" providerName="YouTube" />

<br />

### 5. VerifyVASP API Key Issuance

The VASP approved as a participant of the VerifyVASP Alliance can issue an API Key to use the VerifyVASP API on the VerifyVASP Console. Watch the attached video for guidance on issuing the API Key.

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=VK8DT12orSA" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252FVK8DT12orSA%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253DVK8DT12orSA%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252FVK8DT12orSA%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=VK8DT12orSA" providerUrl="https://www.youtube.com/" providerName="YouTube" />

> ❗️ Please Note:
>
> 1. The Secret Key can only be viewed on the creation screen. Once you leave the screen, it cannot be checked again, so please ensure it is securely stored.
> 2. If you lose the Secret Key, you must re-issue the API Key.
> 3. If you re-issue the API Key, the former API Key will be removed automatically and cannot be used again.
> 4. The Access Key and Secret Key must input on the Environment variable of Enclave Server. Its name is below.
> 5. The Access Key and Secret Key must be set in the environment variables of the Enclave Server. The names are listed below.
>    1. `VEGA_ALLIANCE_ACCESS_KEY` : Enter the Access Key.
>    2. `VEGA_ALLIANCE_SECRET_KEY` : Enter the Secret Key.
>    3. **The name of the environment variable for the Enclave can`never` be changed.**