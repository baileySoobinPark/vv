---
name: Common_database
---
> 💡 TravelRule과 VerifyName 프로토콜을 모두 지원하는 경우
>
> VerifyVASP Enclave 모드 설정에 따라 TravelRule 프로토콜과 VerifyName 프로토콜을 동시에 지원할 수 있습니다. 이 경우 Enclave 데이터베이스의 공통 테이블에 두 프로토콜의 검증 데이터가 함께 저장됩니다. 따라서 두 프로토콜을 동시에 지원하는 VASP는 DBMS 선택 및 아래`TravelRule/VerifyName 공통 필수 테이블 생성 쿼리`를 최초 1번만 실행하여 해당 테이블을 공통으로 사용해야 합니다.