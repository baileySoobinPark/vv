---
title: Onboarding
excerpt: >-
  Before integrating the Travel Rule or VerifyName protocols, you must complete
  VerifyVASP onboarding and obtain an API Key.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Sign Up for the VerifyVASP Console

Follow the steps below to complete your VerifyVASP console registration. Once completed, your VASP will be onboarded for Travel Rule or VerifyName integration.

<br />

### 1. Click the \[Get Started] button in the top-right corner of the [VerifyVASP website](https://www.verifyvasp.com) to open the console.

<Image align="center" src="https://files.readme.io/01daa9f786c6b15eb40a8ca98e865271467b0c1a4793dd7f731b8dfd21b2404c-VV_Onboarding.png" />

<br />

### 2. Enter your email and password to register.

<Image align="center" src="https://files.readme.io/174061e161e9453bade2be15d12868a6a0304332167f6dba72eda4e6bdf64d04-VV_onboarding_2.png" />

<br />

### 3. Enter Company Information

* Provide all required company information for onboarding.
* If additional documents are needed, the VerifyVASP operations team will contact the email address provided in the Business Contact tab.
* Make sure the email address is accurate and able to receive messages.

<Image align="center" src="https://files.readme.io/ce2309555ff44045258a74d903dccae2741ebf73af18779f937a84dbc744f7a9-VV_Onboarding_3.png" />

<br />

### 4. Await Approval

* Once registration is submitted, the VerifyVASP operations team will review and approve your application.
* Additional documents may be requested.
* After all documents are reviewed, your registration will be approved.
* For a detailed walkthrough, refer to the attached onboarding video.

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=FusIymjfGdU" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252FFusIymjfGdU%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253DFusIymjfGdU%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252FFusIymjfGdU%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=FusIymjfGdU" providerUrl="https://www.youtube.com/" providerName="YouTube" />

<br />

## Obtain Your VerifyVASP API Key

Once approved, your VASP can issue an API Key from the VerifyVASP console. This key is required when starting the Enclave and will be used to authenticate API calls between the Enclave server and the Central Server.\
For detailed instructions, see the attached API Key issuance video.

<Embed typeOfEmbed="youtube" url="https://www.youtube.com/watch?v=VK8DT12orSA" html="%3Ciframe%20class%3D%22embedly-embed%22%20src%3D%22%2F%2Fcdn.embedly.com%2Fwidgets%2Fmedia.html%3Fsrc%3Dhttps%253A%252F%252Fwww.youtube.com%252Fembed%252FVK8DT12orSA%253Ffeature%253Doembed%26display_name%3DYouTube%26url%3Dhttps%253A%252F%252Fwww.youtube.com%252Fwatch%253Fv%253DVK8DT12orSA%26image%3Dhttps%253A%252F%252Fi.ytimg.com%252Fvi%252FVK8DT12orSA%252Fhqdefault.jpg%26type%3Dtext%252Fhtml%26schema%3Dyoutube%22%20width%3D%22854%22%20height%3D%22480%22%20scrolling%3D%22no%22%20title%3D%22YouTube%20embed%22%20frameborder%3D%220%22%20allow%3D%22autoplay%3B%20fullscreen%3B%20encrypted-media%3B%20picture-in-picture%3B%22%20allowfullscreen%3D%22true%22%3E%3C%2Fiframe%3E" href="https://www.youtube.com/watch?v=VK8DT12orSA" providerUrl="https://www.youtube.com/" providerName="YouTube" />

> ❗️ API Key Usage Notes
>
> 1. **Secret Key visibility**\
>    The Secret Key is displayed only once at the time of issuance.
>    Store it securely; you will not be able to view it again after leaving the issuance screen.
> 2. **Lost Secret Key**\
>    If the Secret Key is lost, you must reissue the API Key.
>    When reissued, the previous key is automatically revoked and can no longer be used.
> 3. **Enclave Environment Variables**
>
>    To use the API Key, set the following environment variables in your Enclave server.\
>    The variable names **must not be changed**.
>
>    1. `VEGA_ALLIANCE_ACCESS_KEY` : Enter the issued Access Key.
>    2. `VEGA_ALLIANCE_SECRET_KEY` : Enter the issued Secret Key.