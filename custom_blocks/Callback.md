---
name: Callback
---
> 💡 TravelRule과 VerifyName 프로토콜을 모두 지원하는 경우
>
> Callback API는 TravelRule 및 VerifyName 프로토콜의 비동기 흐름을 처리하기 위해 필수로 구현해야 하는 VASP API입니다. 단, 프로토콜 별로 처리해야 하는 콜백 유형은 서로 상이하므로, VASP는 단일 Endpoint로 각 프로토콜의 콜백 유형을 분기 처리할 수 있도록 구현해야 합니다.