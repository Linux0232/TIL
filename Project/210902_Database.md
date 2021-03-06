# S/W Development Process를 알아보려고 한다.
    - 가장 먼저 어떤 Service를 할껀지 가장 먼저 정한다(예를들어 부동산 관련 사이트를 만들겠다.)
        1. Planning - 그 다음 실무자들끼리 필요한기능은 무엇인지 회의를 거치거나 interview를 한다.
        2. Analysis - 업무 분석을 한다. 계획한거를 바탕으로 어떻게 하면 더 효율적이고 양질의 Service제공할 수 있는지 고민하고 분석한다. (USECASE Diagram, 기획서 등)
        3. Design - UI / UX를 설계한다.
        4. DB 설계 - 작성된 기획서나 USECASE Diagram를 통해서 DB 모델 설계(ERD)를 한다.
        5. Implementation - Class Diagram, 코딩규약(변수 등), 언어등을 선택하고 그것을 바탕으로 S/W 프로그래밍을 한다.
        6. Testing & Integration - 프로젝트는 여러사람이서 개발하기에 통합하는 과정이 필요하고, 통합한 후 테스팅한다.
        7. Release - 테스팅 후 문제가 없다면 배포를 한다. 
        8. Maintenance - 배포를 한 후 유지관리를 한다.

    - Database Modeling은 3단계가 있다.
        1. 개념적(Conceptual) 모델링
            1-1. Entity 도출 (임대인,임차인,중개인 등 개념적인 것들 도출)
        2. 논리적(Logical) 모델링
            2-1. Data 구조 및 속성 정의
            2-2. 무결성 정의 및 정규화
        3. 물리적(Physical) 모델링
            3-1. 스키마, 테이블 , 인덱스 생성
     
[UseCase]
![Usecase](https://user-images.githubusercontent.com/81960642/131710033-0fbb9822-de3f-4cef-83fa-a8ae03ef9314.JPG)
[ERD]
![PROPERTY2](https://user-images.githubusercontent.com/81960642/131707988-f8dc5756-28f1-4cc3-b992-5644f392a7f8.png)
