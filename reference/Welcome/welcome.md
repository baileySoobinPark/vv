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
<body>
  <a href="https://developer.nodit.io/docs/referral-program" target="_blank" style="display: block; width: 100%;">
  <picture>
    <!-- 모바일용 배너 (최대 768px까지) -->
    <source media="(max-width: 768px)" srcset="https://files.readme.io/29cbd3afcceb25fbf6c5fda3ea5ccdf856b0fba5ae84bae5333552f209b71c8c-banner_new_mobile.png" />
    
    <!-- 기본 PC용 배너 -->
    <img src="https://files.readme.io/d6d9634d3048e07fd20baa7c4d74ae5bd37dc82577d9e4f0348842a91b911d40-banner_new.png"
         alt="Nodit Referral Program Banner"
         style="width: 100%; height: auto; display: block;" />
  </picture>
</a>
  <h3>Nodit Features</h3>
  Nodit이 처음이신가요? 아래 페이지들을 통해 Nodit의 주요 Feature들을 살펴보세요. Nodit Node, Web3 Data API, Webhook, Stream, Datasquare 등 Nodit이 제공하는 다양한 기능과 함께 활용 사례를 확인할 수 있습니다.
    <div class="container">
     <div class="component-column-container"> 
        <div class="component-box">
          <a href="https://developer.nodit.io/docs/elastic-node">
            <h2 class="node_h2">🌐 Elastic Node</h2>
            <p class="node_p1">SLA 99.9%의 안정성과 Auto-scaling을 통한 확장성을 제공하는 Nodit의 공유 노드 인프라 서비스입니다. 지원하는 네트워크를 살펴보고, 쉽고 빠르게 연동을 시작해보세요.</p>
          </a>
       </div>
        <div class="component-box">
         <a href="https://developer.nodit.io/docs/web3-data-api">
          <h2>👨🏼‍💻 Web3 Data API</h2>
          <p>정확하고 신뢰할 수 있는 블록체인 데이터를 조회할 수 있는 RSETful 쿼리 API들을 제공합니다. 트랜잭션, 계정, NFT, 토큰, 마켓 트렌드 등 Web3의 주요 데이터들을 지금 바로 조회해보세요.</p>
         </a>
        </div>
       <div class="component-box">
         <a href="https://developer.nodit.io/docs/stream">
          <h2>🎥 Stream</h2>
          <p>동기화된 채널을 생성하여 온체인 데이터를 실시간으로 구독할 수 있습니다. 다양한 필터를 활용하여 커스텀된 데이터 수집 도구를 생성해보세요.</p>
         </a>
        </div>
        
   	 </div>
    <div class="component-column-container"> 
    <div class="component-box">
          <a href="https://developer.nodit.io/docs/dedicated-node">
            <h2 class="node_h2">⚡ Dedicated Node</h2>
            <p class="node_p1">대규모 프로젝트 또는 기업을 위한 고성능 전용 노드 인프라 서비스입니다. 격리 운영되는 안정적인 인프라와 다양한 모니터링 도구, 별도의 Support Channel을 통해 더욱 안정적인 서비스를 제공할 수 있습니다. </p>
          </a>
        </div>
    <div class="component-box">
     <a href="https://developer.nodit.io/docs/webhook">
      <h2>⚓ Webhook</h2>
      <p>실시간 온체인 이벤트 추적을 위한 트리거를 생성하고 어플리케이션에 연동하여 반응형 서비스를 구축해보세요. 자산 전송, 잔고 추적 등 실시간 이벤트를 구독하여 높은 사용자 경험을 제공할 수 있습니다.</p>
     </a>
    </div>
    <div class="component-box">
         <a href="https://developer.nodit.io/docs/data-square">
          <h2>🗃️ Datasquare</h2>
          <p>Nodit이 구축해온 방대한 규모의 블록체인 데이터 웨어하우스를 이제 누구나, 무료로 사용할 수 있습니다. 데이터가 필요한 곳이라면 모든 가능성이 열려있습니다. 자유롭게 탐색하고, 분석하고, 더 많은 인사이트를 얻어보세요.</p>
         </a> 
       </div>
   </div>
  </div>
  <br>
  <h3>Supported Chains</h3>
  체인별로 제공되는 기능과 API, 그리고 관련 자료를 한눈에 볼 수 있습니다. 구현할 DApp에 최적화된 블록체인 네트워크를 선택하고, 체인별로 제공되는 도구들을 활용해보세요.  
   <div class="container">   
  		<div class="supported-chain-container">
        <div class="supported-chain-component-container">
          
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/aptos-quickstart">
            <p>Aptos</p>
            <div class="box-image apt">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/arbitrum-quickstart">
            <p>Arbitrum</p>
            <div class="box-image arb">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/avalanche-quickstart">
            <p>Avalanche</p>
            <div class="box-image avalanche">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/base-quickstart">
            <p>Base</p>
            <div class="box-image base">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/bitcoin-quickstart">
            <p>Bitcoin</p>
            <div class="box-image btc">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/dogecoin-quickstart">
            <p>Doge</p>
            <div class="box-image doge">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/ethereum-quickstart">
            <p>Ethereum</p>
            <div class="box-image eth">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/kaia-quickstart">
            <p>Kaia</p>
            <div class="box-image kaia">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/optimism-quickstart">
            <p>Optimism</p>
            <div class="box-image opt">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/polygon-quickstart">
            <p>Polygon</p>
            <div class="box-image poly">
            </div>
          </a>
          </div>
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/tron-quickstart">
            <p>Tron</p>
            <div class="box-image tron">
            </div>
          </a>
          </div>  
          <div class="box-chain">
          <a href="https://developer.nodit.io/reference/xrpl-quickstart">
            <p>XRPL</p>
            <div class="box-image xrpl">
            </div>
          </a>
          </div>
        </div>
     </div>
  </div>
	<br>
  <br>
 <h3>New & Popular</h3>
    최신 트렌드와 인기 있는 콘텐츠를 한곳에서 확인해 보세요. Web3 개발 커뮤니티에서 주목받고 있는 자료와 Nodit의 최신 업데이트를 제공합니다. 새로운 기술과 인사이트를 빠르게 습득하여, 경쟁력 있는 DApp을 개발하는 데 도움이 되는 정보를 제공받으세요.  
     <div class="container">
  		<div class="new-popular-container">
        <div class="new-popular-component-container">
          <a href="https://developer.nodit.io/docs/nodit-mcp" class="box-tutorial"> 
            <div class="title">Nodit MCP - Connect AI to Blockchain </div>
            <br>Nodit이 AI를 만나 더 쉽고, 더 빠른 Web3 개발을 지원합니다. 새롭게 공개된 Nodit MCP 활용하여 자연어 프롬프트를 통한 블록체인 데이터 조회와 Nodit 연동을 바로 시작해보세요.
            <img class="tutorial-image" src="https://files.readme.io/09b0837d53c29102f7a55a65ebbb26670d269d8362bb68812262431d58bf7503-Blockchain_MCP.png"/>
          </a>
          <a href="https://developer.nodit.io/reference/xrpl-quickstart" class="box-tutorial"> 
            <div class="title">XRP Ledger Web3 Data API</div>
            <br>정교하게 인덱싱된 Nodit의 XRPL Web3 Data API를 통해 Transaction, Token Transfer, Balance Change와 같은 XRP Ledger의 핵심 데이터들을 조회할 수 있습니다.
            <img class="tutorial-image" src="https://files.readme.io/a4b707169e304ddbf3d0b005c41f1d4067831266382655029f224c3eab3ff954-XRPL_x_Nodit.png"/>
          </a>
          <a href="https://developer.nodit.io/reference/tron-quickstart" class="box-tutorial"> 
            <div class="title">Tron Web3 Data API</div>
            <br>Tron 생태계를 위한 Nodit Web3 Data API를 만나보세요! TRC10, TRC20을 완벽 지원하며 Native Token 잔고와 전송 이력을 쉽게 추적할 수 있습니다.
            <img class="tutorial-image" src="https://files.readme.io/d7be3684d386607fde460395d70d90df9c65a9aa674f1cbbca554379e7c43cc9-tron_thumbnail.png"/>
          </a>
          <a href="https://developer.nodit.io/reference/avalanche-quickstart" class="box-tutorial">       
            <div class="title">Avalanche Node is Now On!</div>
            <br>Nodit에서 아발란체 C-체인을 위한 Elastic Node, Dedicated Node를 새롭게 지원합니다. 가용성 99.9%의 아발란체 노드를 통해 견고한 Dapp 서비스를 구축해보세요.
            <img class="tutorial-image" src="https://files.readme.io/a83f245c2f26395f46f794714721eae0fd9b18bae011801f137406719534a344-Avalanche_supported_1.png"/>
          </a>
          <a href="https://developer.nodit.io/reference/aptos-quickstart" class="box-tutorial">
            <div class="title">Aptos Webhook Quickstart</div>
            <br>Aptos Webhook을 어떻게 생성하고, 관리할 수 있는지 빠르게 배울 수 있는 Quickstart 페이지입니다. Webhook에 사용할 수 있는 다양한 옵션을 예제와 함께 살펴보고 바로 사용해보세요.
            <img class="tutorial-image" src="https://files.readme.io/d911175b65505b76a40ab0ca0e9ffd2c5e76264c985c8ea13b0247b4e9d39376-01.png"/>
          </a>
          <a href="https://developer.nodit.io/reference/aptos-quickstart" class="box-tutorial">
            <div class="title">Aptos Webhook</div>
            <br>Webhook을 기다려온 Aptos 생태계를 위해, Nodit Webhook이 Aptos를 새롭게 지원합니다. 이제 Event와 Transaction을 효과적으로 모니터링하세요!
            <img class="tutorial-image" src="https://files.readme.io/a6c17a04dec47f992b53db255892fa3b407881ad624b5274b076378beb4cfd4f-1730242870-4cde165341f5-13.avif"/>
          </a>
          <a href="https://developer.nodit.io/reference/bitcoin-quickstart" class="box-tutorial">
            <div class="title">Bitcoin Web3 Data API</div>
            <br>비트코인을 위한 Account, Transaction 데이터 인덱싱과 Data API를 새롭게 지원합니다. 세계 최대 암호화폐의 거래 이력과 계정의 데이터를 분석하고 활용해보세요.
            <img class="tutorial-image" src="https://files.readme.io/655bf0b888fbe163fd249aacdc0821b6ed9d12c11f792d4620b2daadf85df0ea-Now-supported_-10-mainnets.png"/>
          </a>
          <a href="https://developer.nodit.io/reference/dogecoin-quickstart" class="box-tutorial">
            <div class="title">Doge Web3 Data API</div>
            <br>도지코인을 위한 Account, Transaction 데이터 인덱싱과 Data API를 새롭게 지원합니다. 가장 유명한 밈코인의 거래 이력과 계정의 데이터를 분석하고 활용해보세요. 
            <img class="tutorial-image" src="https://files.readme.io/9d67f2f9e795e1cd20cccaae19fadc10676d313bc272686b9764a01563d26e84-modified_doge.jpeg"/>
          </a>
          
          <a href="https://developer.nodit.io/docs/ethereum-basics-block" class="box-tutorial">
            <div class="title">Ethereum Basics & Tutorials</div>
            <br>이더리움의 기본 개념을 이해하기 위한 튜토리얼을 제공합니다. Nodit Nodes를 활용한 간단한 이더리움 테스트를 진행해보세요.
            <img class="tutorial-image" src="https://files.readme.io/3c221face2517770043dad24f05514ee434a71e64d39c7c5d926ed174a29adec-_2024-09-05__11.49.49.png"/>
          </a>
           <a href="https://developer.nodit.io/docs/kaia-testnet-faucet" class="box-tutorial">
            <div class="title">Kaia Testnet Faucet</div>
            <br>Nodit이 제공하는 Kaia Kairos 테스트넷의 공식 Faucet 페이지를 확인해보세요.
            <img class="tutorial-image" src="https://files.readme.io/c32f194-_2024-07-19__10.46.25.png"/>
          </a>
      </div>
    </div>
  </div>
  <br>
  <br>
  <h3>Tutorials</h3>
  Nodit의 기능을 활용하여 Web3 개발 역량을 강화할 수 있는 실습형 튜토리얼을 제공합니다. 초보자부터 전문가까지 모두를 위한 단계별 가이드를 통해, DApp 개발에 필요한 기술을 체계적으로 익히실 수 있습니다. 
  <div class="container">    
    <div class="tutorial-container">
       <div class="tutorial-component-container"> 
        <a href="https://developer.nodit.io/docs/set-up-for-tutorials" class="box-tutorial">
          <div class="title">Aptos Tutorials</div>
	     		<br>Aptos노드를 연결하고 계정생성과 APT 전송, 간단한 모듈 배포를 경험해볼까요? Aptos Dapp 개발에 필요한 기본을 빠르게 익힐 수 있습니다.
          <img class="tutorial-image" src="https://files.readme.io/9aae7e7-Aptos_Node.png"/>
        </a>
        <a href="https://developer.nodit.io/docs/building-evm-wallet" class="box-tutorial">
          <div class="title">Building an EVM Wallet</div>
          <br>Nodit의 Web3 Data API를 이용하여 EVM 계열의 체인에서 사용 가능한 wallet을 구현해보세요!
          <img class="tutorial-image" src="https://files.readme.io/02b87202a3e2ea76ad42448751faf80d5b119b889564f05783a1a89ca2fe58a0-wallet3.png"/>
        </a>
        <a href="https://developer.nodit.io/docs/web3-data-api-tutorials" class="box-tutorial">
          <div class="title">Building a Simple NFT Explorer</div>
          <br>Nodit의 Web3 Data API중 NFT API를 사용하여 간단한 NFT 탐색기를 구현해보세요!
          <img class="tutorial-image" src="https://files.readme.io/f53a2194c41fb23730517cc2c5a6da00de15b26ac3ed0786e785dc2216bec418-_2024-08-27__5.39.04.png"/>
        </a>
        <a href="https://developer.nodit.io/docs/building-a-simple-token-explorer-using-web3-data-apis" class="box-tutorial">
          <div class="title">Building a Token Tracker</div>
          <br>Token API를 활용하여 자산을 추적하는 방법을 알아봅니다.
          <img class="tutorial-image" src="https://files.readme.io/13012aedd7e005091f6a74d7afebda9f7228a1f7a762137c2c009d52d86b98f6-_2024-09-02__2.30.49.png"/>
        </a>
      </div>
    </div>
  </div>
  <br>
  <br>
</body>
</html>
`}</HTMLBlock>