---
title: Overview
excerpt: >-
  Welcome to the VerifyVASP documentation. Access a comprehensive overview for
  quick entry to key reference materials supporting your TravelRule or
  VerifyName protocol implementation.
deprecated: false
hidden: true
metadata:
  robots: index
---
## Getting started with the Travel Rule & our solutions

**`Recommended to read first`**

If you’re new to the Travel Rule or VerifyVASP, explore these resources to understand its core concepts and integration framework. Grasping the background and big picture before starting integration will provide valuable clarity and guidance.

<HTMLBlock>{`
<style>
* {
 		text-decoration: none;   
  }  
  
.container {
    display: inline-flex;
    gap: 20px;
    widht:100%;
    
}
  
.supported-chain-container {
  width: 100%;
  display: flex;
  justify-content: center;
}

.supported-chain-component-container {
  margin-top: 20px;
  width: 100%;
  display: flex;
  flex-wrap: wrap; 
  justify-content: flex-start; 
  gap: 14px; /* 요소들 간 간격 */
}

.tutorial-container, .new-popular-container{
	width: 100%;
  display: flex;
  justify-content: center;
  }

  .tutorial-component-container, .new-popular-component-container{
    margin-top: 20px;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 30px;
    max-width: 100%;
  }  

  
a:link{
 		text-decoration: none; 
}
a:visited{
 		text-decoration: none; 
}
a:hover{
 		text-decoration: none; 
}
a:active{
 		text-decoration: none; 
}
.component-column-container {
	margin-top: 20px;
  width: 48%;
  display: flex;
  flex-direction: column;
  }
  
.component-box {
  width: 100%; 
  color: black;
  transition: height 0.3s ease;
  overflow: hidden; 
  display: flex;
  flex-direction: column; 
}

.component-box h2 {
  margin: 0;
  padding: 10px;
  cursor: pointer;
  text-align: center;
  border: 1px solid #ccc; /* 회색 얇은 1px 테두리 */
  border-radius: 10px 10px 10px 10px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  color: black;
  font-size: 16px;
  transition: border-radius 0.1s ease;
  background-color: #ffffff;
}

.component-box h2:hover {
  	border-radius: 10px 10px 0 0;
  }
.component-box p {
  margin: 0;
  padding: 10px;
	background-color: white;
  max-height: 0; 
  opacity: 0; 
  transition: max-height 0.3s ease, opacity 0.3s ease;
  overflow: hidden;
}

.component-box h2:hover + p {
  max-height: 200px;
  border: 1px solid #ccc;
  border-top: none; /* 상단 테두리 제거 */
  opacity: 1;
  border-radius: 0 0 10px 10px;
  margin-bottom: 20px;
}

  
.box-chain {
    background-color: #ffffff;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    padding: 20px;
    text-align: center;
    width: 120px;
    height: 120px;
    transition: transform 0.2s, box-shadow 0.2s;
    color: #333;
    border: 1px solid #ccc; /* 회색 얇은 1px 테두리 */
    white-space: normal; /* 텍스트 줄바꿈을 허용 */
    margin-top: 5px;
    display: flex; /* 플렉스박스 사용 */
    flex-direction: column; /* 수직 정렬 */
    justify-content: center; /* 수직 방향 중앙 정렬 */
    align-items: center; /* 수평 방향 중앙 정렬 */
    text-align: center; /* 텍스트 중앙 정렬 */
}

  .box-chain a p {
    margin: 0 auto;
    font-weight: bold;
    text-decoration: none; 
    font-size: 16px;
		transform: translateY(6px);
  }
  
  
.box-image.eth{
    background: url("https://files.readme.io/39a8dfd7af92797f4a4992dc3eef822a35039ae6472b244cd12b52c9b2a947cc-ethereumTestnet.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}

.box-image.poly{
    background: url("https://files.readme.io/52aed35f7705f94eae8d65bbd1614d1f378f2c0698b1778f63edc8f444599750-polygonTestnet.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
.box-image.arb{
    background: url("https://files.readme.io/5e8d7fd7304dabeccd04b1625c7381670dfb83d1e0f898469c24f2a32512f3a3-arbitrumTestnet.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
.box-image.opt{
    background: url("https://files.readme.io/1c90c3745c5b2fc3bdf2786992e8c2ab182b45e55d49f8b5f562e0c43ec73c68-optimismTestnet.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
.box-image.apt{
    background: url("https://files.readme.io/247f10947400849c248f83dca589b41371eca44b4d06f34ba22d30853bde3ea0-aptosTestnet.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
.box-image.kaia{
    background: url("https://files.readme.io/51aa0c7d8d4ddceb5bcc6487916098c550c4850a10e1f287443edd69c12d1369-kaiaTestnet.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
.box-image.base{
    background: url("https://files.readme.io/7bccf3afa181fec11bd0f798c9030037703dadc422565dcd0fe29b59903559a5-baseMainnet.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
.box-image.btc{
    background: url("https://files.readme.io/ac9bf38fe09b79e70e2764116691a330cc557d0b81225e5e65f28d97c9c758dd-bitcoinBtcl_S.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}

.box-image.doge{
    background: url("https://files.readme.io/e68f451a969f4be381ed71f4069d905b3a259313ede6f1085d60565facbaa666-dogel_S.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}

.box-image.avalanche{
    background: url("https://files.readme.io/6df2f5645c6004adb11f55610ce1dc39a754e9681ef2a276a114ec6ca63c82de-avalanche.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
 
.box-image.tron{
    background: url("https://files.readme.io/46ff54c4ef0d30f7ca2fcd64ee55b93e693c17afabd5539d701b0261d7c099c7-tron.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
}
  
.box-image.xrpl{
    background: url("https://files.readme.io/9d658d19f31f2c2aa72e3ab771ffaf785b58d996736fb04554c63ed8c080406a-ripple.png");
    width: 84px;
    height: 84px;
    background-size: 100%;
    background-repeat: no-repeat;
    background-position:center;
} 
 
.box-chain-test:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
}

  .test-box-chain:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
}
  
  
.box-tutorial {
    background-color: #ffffff;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    padding: 20px;
    width: 48%;
    height: 300px;
    transition: transform 0.2s, box-shadow 0.2s;
    color: #666;
    border: 1px solid #ccc; /* 회색 얇은 1px 테두리 */
    flex-shrink: 0;
    white-space: normal; /* 텍스트 줄바꿈을 허용 */
    margin-top: 5px;
   	display: block;
    position: relative;
    overflow: hidden;
    font-size: 13px;
}

  
.box-tutorial .title{
  text-align: center;
  font-weight: bold;
  font-size: 16px;
  color: #333;
}

.tutorial-image {
    position: absolute;
    bottom: 0; 
    left: 0;
    width: 100%; 
    height: 50%; 
    object-fit: cover; 
    border-top:2px solid #ccc;
}

.box-tutorial:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 12px rgba(0, 0, 0, 0.2);
}

.box h3 {
    margin: 0;
    font-size: 14px;
}
  
  </style>

<div class="container">
  		<div class="new-popular-container">
        <div class="new-popular-component-container">
          <a href="https://developer.nodit.io/docs/nodit-mcp" class="box-tutorial"> 
            <div class="title">Nodit MCP - Connect AI to Blockchain </div>
            <br>Nodit이 AI를 만나 더 쉽고, 더 빠른 Web3 개발을 지원합니다. 새롭게 공개된 Nodit MCP 활용하여 자연어 프롬프트를 통한 블록체인 데이터 조회와 Nodit 연동을 바로 시작해보세요.
            <img class="tutorial-image" src="https://files.readme.io/09b0837d53c29102f7a55a65ebbb26670d269d8362bb68812262431d58bf7503-Blockchain_MCP.png"/>
          </a>
          <a href="https://developer.nodit.io/reference/xrpl-quickstart" class="box-tutorial"> 
          </a>
      </div>
    </div>
  </div>
`}</HTMLBlock>