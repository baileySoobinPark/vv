---
title: Overview
excerpt: >-
  Welcome to the VerifyVASP documentation. Access a comprehensive overview for
  quick entry to key reference materials supporting your TravelRule or
  VerifyName protocol implementation.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Getting started with the Travel Rule & Our solutions

**`Recommended to read first`**

If you’re new to the Travel Rule or VerifyVASP, explore these resources to understand its core concepts and integration framework. Grasping the background and big picture before starting integration will provide valuable clarity and guidance.

<HTMLBlock>{`
<style>
  
a.card-link {
  text-decoration: none;
  color: inherit;
}
.card-link,
.card-link:visited,
.card-link:hover,
.card-link:focus,
.card-link:active,
.card-link *,
.card-link *:visited,
.card-link *:hover,
.card-link *:focus,
.card-link *:active {
  text-decoration: none !important;
}
/* 방문, 호버, 포커스, 클릭 상태 모두 같은 색상으로 통일 */
a.card-link:visited,
a.card-link:hover,
a.card-link:focus,
a.card-link:active {
  text-decoration: none;
  color: inherit;
}
/* 내부 텍스트에 직접 스타일이 적용되어 있다면 아래처럼 오버라이딩 */
.card-link p {
  text-decoration: none;
  color: inherit;
}

/* 혹시 강제로 스타일이 덮어씌워질 경우 */
.card-link * {
  color: inherit !important;
  text-decoration: none !important;
}
  
.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 16px;
}

.card-link {
  width: calc((100% - 32px) / 3); /* 3개 정렬 */
  text-decoration: none;
  color: inherit;
  display: block;
}

.card {
  height: 100%;
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  background-color: #fff;
  transition: transform 0.2s ease;
}

.card:hover {
  transform: translateY(-4px);
}

.card-image {
  width: 100%;
  aspect-ratio: 3 / 2;
  object-fit: contain; /* 이미지 전체 표시 */
  display: block;
}

.contents-space {
  flex: 1;
  padding: 16px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.contents-space p {
  margin: 0 0 12px;
  flex-grow: 1;
}
</style>

<body>
  <div class="container">
    <div class="card-container">

<a href="ref:verifyname-request-verification" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/5a637d6da90c1ffec03c8b4179939b9bb61da78f94ae40e7d48238d6aeb04856-IMG_2158.png" alt="Card Image" />
      <div class="contents-space">
        <p>Before starting integration, understand travel rule and VASPs' duties.</p>
      </div>
    </div>
  </a>

      <a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/7dba9908acf3bf5359a3b54b73b5ad6c4c2445c37e242442af06f0168a4fc1ec-IMG_2157.png" alt="Card Image" />
      <div class="contents-space">
        <p>Let’s start with the big picture—architecture, main flows, and security features.</p>
      </div>
    </div>
  </a>

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/b6c2a8626aaeb494c534130f426310d5a015baf5ab33428b1060940fa09e23a4-IMG_2156.png" alt="Card Image" />
      <div class="contents-space">
        <p>Identify your VASP's implementation scope first</p>
      </div>
    </div>
  </a>

    </div>
  </div>
</body>
`}</HTMLBlock>

<br />

## For Travel Rule obliged VASP developers

Developers integrating TravelRule can refer to these resources for a step-by-step guide to its core functionalities, architecture, and integration requirements. The documentation includes API specifications, the integration guide, process flows, and architectural details to help you implement TravelRule efficiently and ensure compliance with the Travel Rule and regulatory standards.

<HTMLBlock>{`
<style>
  
a.card-link {
  text-decoration: none;
  color: inherit;
}
.card-link,
.card-link:visited,
.card-link:hover,
.card-link:focus,
.card-link:active,
.card-link *,
.card-link *:visited,
.card-link *:hover,
.card-link *:focus,
.card-link *:active {
  text-decoration: none !important;
}
/* 방문, 호버, 포커스, 클릭 상태 모두 같은 색상으로 통일 */
a.card-link:visited,
a.card-link:hover,
a.card-link:focus,
a.card-link:active {
  text-decoration: none;
  color: inherit;
}
/* 내부 텍스트에 직접 스타일이 적용되어 있다면 아래처럼 오버라이딩 */
.card-link p {
  text-decoration: none;
  color: inherit;
}

/* 혹시 강제로 스타일이 덮어씌워질 경우 */
.card-link * {
  color: inherit !important;
  text-decoration: none !important;
}
  
.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 16px;
}

.card-link {
  width: calc((100% - 32px) / 3); /* 3개 정렬 */
  text-decoration: none;
  color: inherit;
  display: block;
}

.card {
  height: 100%;
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  background-color: #fff;
  transition: transform 0.2s ease;
}

.card:hover {
  transform: translateY(-4px);
}

.card-image {
  width: 100%;
  aspect-ratio: 3 / 2;
  object-fit: contain; /* 이미지 전체 표시 */
  display: block;
}

.contents-space {
  flex: 1;
  padding: 16px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.contents-space p {
  margin: 0 0 12px;
  flex-grow: 1;
}
</style>

<body>
  <div class="container">
    <div class="card-container">

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/c1ccd437b1059444118790528113b4df84081773e3b711e226aaab0762ab3e51-VV_02_TRR_Flow_Diagram.png" alt="Card Image" />
      <div class="contents-space">
        <p>Learn the best practice of integration with sequence diagram.</p>
      </div>
    </div>
  </a>

      <a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/b05f25f9e709130aa2cc873f28a725d4794578705b947408a3cf572801460e7f-VV_03_TRR_To-Be_Architecture.png" alt="Card Image" />
      <div class="contents-space">
        <p>Review the TO-BE VASP architecture and define the implementation scope.</p>
      </div>
    </div>
  </a>

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/b85f6bb2f4fe3ffa7308791888e77c55de67922a3cd7e65b64a9e4d8cdffd7f1-VV_04_TRR_Implementation_Steps.png" alt="Card Image" />
      <div class="contents-space">
        <p>Let's start integration with onboarding. See all action items here.</p>
      </div>
    </div>
  </a>

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/ba01de84cd5b0a75ab66e98a47d0a9b3748466a78815db31174248d93eeb6a39-VV_05_TRR_API_Implementation.png" alt="Card Image" />
      <div class="contents-space">
        <p>API specifications that your VASP must provide.</p>
      </div>
    </div>
  </a>

      <a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/dd7e2187ac468ca9db2212d9d44ba6faba6e789d8d32b12d11ba7f6ddcc0d974-VV_06_TRR_Enclave_Installation.png" alt="Card Image" />
      <div class="contents-space">
        <p>Enclave API specification that helping your VASP connect with counterparty VASPs.</p>
      </div>
    </div>
  </a>

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/1d0875d9b01e6f0af39382f7d75dbb9b989c4536f5a4c45c9c509f4dde4441e1-VV_07_VN_for_Travel_Rule_Obliged_VASP.png" alt="Card Image" />
      <div class="contents-space">
        <p>Start your VerifyName integration here: for Travel Rule obliged VASP developers.</p>
      </div>
    </div>
  </a>
      
    </div>
  </div>
</body>
`}</HTMLBlock>

<br />

## For Travel Rule non-obliged VASP developers

If you’re a VASP developer integrating VerifyName, these resources will help you understand its key features, implementation framework, and best practices.

<HTMLBlock>{`
<style>
  
a.card-link {
  text-decoration: none;
  color: inherit;
}
.card-link,
.card-link:visited,
.card-link:hover,
.card-link:focus,
.card-link:active,
.card-link *,
.card-link *:visited,
.card-link *:hover,
.card-link *:focus,
.card-link *:active {
  text-decoration: none !important;
}
/* 방문, 호버, 포커스, 클릭 상태 모두 같은 색상으로 통일 */
a.card-link:visited,
a.card-link:hover,
a.card-link:focus,
a.card-link:active {
  text-decoration: none;
  color: inherit;
}
/* 내부 텍스트에 직접 스타일이 적용되어 있다면 아래처럼 오버라이딩 */
.card-link p {
  text-decoration: none;
  color: inherit;
}

/* 혹시 강제로 스타일이 덮어씌워질 경우 */
.card-link * {
  color: inherit !important;
  text-decoration: none !important;
}
  
.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 16px;
}

.card-link {
  width: calc((100% - 32px) / 3); /* 3개 정렬 */
  text-decoration: none;
  color: inherit;
  display: block;
}

.card {
  height: 100%;
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  background-color: #fff;
  transition: transform 0.2s ease;
}

.card:hover {
  transform: translateY(-4px);
}

.card-image {
  width: 100%;
  aspect-ratio: 3 / 2;
  object-fit: contain; /* 이미지 전체 표시 */
  display: block;
}

.contents-space {
  flex: 1;
  padding: 16px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.contents-space p {
  margin: 0 0 12px;
  flex-grow: 1;
}
</style>

<body>
  <div class="container">
    <div class="card-container">

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/91bf5f41a43bdc3ff59d2f441c8d0bb820f816e6e8a6923de9e7b1c2ee23ba56-VV_08_VN_Flow_Diagram.png" alt="Card Image" />
      <div class="contents-space">
        <p>Differences between Post-verification and Pre-verification</p>
      </div>
    </div>
  </a>

      <a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/64bba7ef7310e87fd4066aa7bd430081d8a936bdc3790813663c86e1d2697a5c-VV_09_VN_To-Be_Architecture.png" alt="Card Image" />
      <div class="contents-space">
        <p>Review the TO-BE VASP architecture and define the implementation scope.</p>
      </div>
    </div>
  </a>

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/53a8d659ba5ce227485902614759b67a86710fb5b8402ec616d6a50335450879-VV_10_VN_for_Non-Obliged_VASP.png" alt="Card Image" />
      <div class="contents-space">
        <p>Start your VerifyName integration here: for Travel Rule non-obliged VASP developers.</p>
      </div>
    </div>
  </a>

    </div>
  </div>
</body>
`}</HTMLBlock>

<br />

## FAQ

<HTMLBlock>{`
<style>
  
a.card-link {
  text-decoration: none;
  color: inherit;
}
.card-link,
.card-link:visited,
.card-link:hover,
.card-link:focus,
.card-link:active,
.card-link *,
.card-link *:visited,
.card-link *:hover,
.card-link *:focus,
.card-link *:active {
  text-decoration: none !important;
}
/* 방문, 호버, 포커스, 클릭 상태 모두 같은 색상으로 통일 */
a.card-link:visited,
a.card-link:hover,
a.card-link:focus,
a.card-link:active {
  text-decoration: none;
  color: inherit;
}
/* 내부 텍스트에 직접 스타일이 적용되어 있다면 아래처럼 오버라이딩 */
.card-link p {
  text-decoration: none;
  color: inherit;
}

/* 혹시 강제로 스타일이 덮어씌워질 경우 */
.card-link * {
  color: inherit !important;
  text-decoration: none !important;
}
  
.card-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 16px;
}

.card-link {
  width: calc((100% - 32px) / 3); /* 3개 정렬 */
  text-decoration: none;
  color: inherit;
  display: block;
}

.card {
  height: 100%;
  display: flex;
  flex-direction: column;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  background-color: #fff;
  transition: transform 0.2s ease;
}

.card:hover {
  transform: translateY(-4px);
}

.card-image {
  width: 100%;
  aspect-ratio: 3 / 2;
  object-fit: contain; /* 이미지 전체 표시 */
  display: block;
}

.contents-space {
  flex: 1;
  padding: 16px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.contents-space p {
  margin: 0 0 12px;
  flex-grow: 1;
}
</style>

<body>
  <div class="container">
    <div class="card-container">

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/d59ed5b28060acc0e1025631e6ee099d7056093907a481534a48c644fd6011f5-VV_11_Onboarding.png" alt="Card Image" />
      <div class="contents-space">
        <p>Explore the Onboarding FAQ for unresolved answers.</p>
      </div>
    </div>
  </a>

      <a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/ae12e724d92006de4675ef906ea2871996a4949310a002f2b2387734bb95d566-VV_12_Implementation.png" alt="Card Image" />
      <div class="contents-space">
        <p>Explore the Implementation FAQ for unresolved answers.</p>
      </div>
    </div>
  </a>

<a href="#" class="card-link">
    <div class="card">
      <img class="card-image" src="https://files.readme.io/0dadfbeae51bee4b4a0ad95f7fab052489b977c7e85978f38b3ad37f3961f233-VV_13_Maintenance.png" alt="Card Image" />
      <div class="contents-space">
        <p>Explore the Maintenance FAQ for unresolved answers.</p>
      </div>
    </div>
  </a>

    </div>
  </div>
</body>
`}</HTMLBlock>