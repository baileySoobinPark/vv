---
name: Callback
---
> 💡 TravelRule과 VerifyName 프로토콜을 모두 지원하는 경우
>
> Callback API는 TravelRule, VerifyName 프로토콜에서 비동기 Flow 대응을 위해 필수로 구현해야 하는 VASP API입니다. 단, 프로토콜별로 처리해야 하는 콜백 유형은 서로 상이하므로, VASP는 단일 Endpoint에서 두 프로토콜의 필수 구현 요구사항을 모두 구현하여 제공할 수 있습니다.