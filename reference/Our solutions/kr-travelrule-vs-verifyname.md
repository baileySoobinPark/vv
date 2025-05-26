---
title: (KR)TravelRule VS VerifyName
deprecated: false
hidden: true
metadata:
  robots: index
---
## 두 프로토콜의 차이점

**TravelRule** 은 Travel Rule 규제 의무가 있는 VASP간 송.수신인의 개인정보를 암호화하여 안전하게 주고 받을 수 있도록 설계되었습니다. 송신인과 수신인이 같거나 다른 경우 모두 지원이 가능합니다.

반면, **VerifyName**은 Travel Rule 규제 의무가 있는 VASP와 의무가 없는 VASP 간의 가상자산 송·수신을 지원하기 위해 설계된 솔루션입니다. 해당 솔루션은 송.수신인이 동일한 경우에만 자산 이동을 허용하며, 개인정보를 직접 주고받는 대신, 개인정보를 Hash 처리한 후 해당 Hash 값의 일치 여부를 통해 동일인 여부를 확인합니다.

<br />

## Travel Rule 의무 VASP vs. 비의무 VASP

VASP가 Travel Rule 의무 대상인지 여부는 관할 지역의 규제 요건에 따라 달라집니다. 관할 지역의 규제상 라이센스 또는 신고/등록이 요구되는지, AML/CFT 의무사항 여부 등 법무 또는 컴플라이언스 팀과 함께 정확한 요구사항을 파악하여 TravelRule 과 VerifyName 중 어떤 솔루션을 사용할지를 판단하십시오.

<Image align="center" src="https://files.readme.io/060b94af81c9aa3dcfa8c7b767af2789fe1fd743c59c79d2a58a21e3394925c8-travelrule_vs_verifyName.png" />