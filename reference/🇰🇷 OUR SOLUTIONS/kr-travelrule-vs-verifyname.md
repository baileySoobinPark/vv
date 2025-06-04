---
title: TravelRule VS VerifyName
excerpt: >-
  VerifyVASP의 두 대표 솔루션인 TravelRule과 VerifyName의 기능적 차이 및 VASP 요구사항에 따른 구현 권장사항을
  확인할 수 있습니다. 
deprecated: false
hidden: false
metadata:
  robots: index
---
## 두 프로토콜의 차이점

**TravelRule** 은 Travel Rule **규제 의무가 있는 VASP간** 가상자산 전송 과정에서 송.수신인의 개인정보의 안전한 교환 및 상호 검증을 지원합니다. TravelRule을 지원하는 VASP간 송금 시 송신인과 수신인이 같거나 다른 경우 모두 송금이 가능합니다.

반면 **VerifyName**은, Travel Rule 규제 의무가 있는 VASP와 의무가 없는 VASP 간 가상자산 송·수신시 규제 의무 VASP측의 단독 정보 검증을 통한 최소 컴플라이언스 요건 충족을 지원합니다. VerifyName 프로토콜은 송.수신인이 동일한 경우에만 자산 이동을 허용하며, 개인정보를 직접 주고받는 대신 송.수신인의 이름과 생년월일의 Hash값 을 교환하여 일치 여부를 비교함으로서 동일인 여부를 검증합니다.

<Image align="center" src="https://files.readme.io/060b94af81c9aa3dcfa8c7b767af2789fe1fd743c59c79d2a58a21e3394925c8-travelrule_vs_verifyName.png" />

## VASP 의무에 따른 구현 필수 사항과 권장사항

Travel Rule 준수 의무가 있는 VASP는 TravelRule 프로토콜을 필수로 구현하고, VerifyName 프로토콜을 선택적으로 구현할 수 있습니다. VerifyVASP는 규제 준수 의무가 있는 VASP의 경우 TravelRule과 VerifyName 모두를 지원하는 것을 권장하고 있습니다. VerifyName 프로토콜을 구현하는 경우 Travel Rule 준수 의무가 없는 VASP와의 자산 전송을 추가로 지원하여 높은 상호 운영성을 제공할 수 있기 때문입니다.

Travel Rule 준수 의무가 없는 VASP 또한 VerifyName 프로토콜을 구현하여 Travel Rule 준수 의무가 있는 주요 VASP들과의 자산 전송을 지원할 수 있습니다.

<br />

## Travel Rule 의무 VASP vs. 비의무 VASP

VASP가 Travel Rule 의무 대상인지 여부는 관할 지역의 규제 요건에 따라 달라집니다. 관할 지역의 규제상 라이센스 또는 신고/등록이 요구되는지, AML/CFT 의무사항 여부 등 법무 또는 컴플라이언스 팀과 함께 정확한 요구사항을 파악하여 TravelRule 과 VerifyName 중 어떤 솔루션을 사용할지를 판단하십시오.