# 설계

1. 요구사항 확인 분석 (사용자 입장)
2. 파트별 업무 분석
3. 백엔드 설계 - DB 설계
    - 개념 설계 - 엔티티, 속성 구분
        + 엔티티 정의서
        + 속성 정의서
        + 식별자
            + 주식별자 : 속성중에서 기본키에 해당할만한 키를 채택 : 일련번호
            + 외래식별자 : 두 엔티티를 부모자식으로 구분하는 속성
    - 논리 설계
        + 개념설계에서 구분한 용어들을 도식화  
        (E-R 다이어그램 : [추천사이트](https://app.diagrams.net))
        + 각 엔티티의 이상현상을 제거하기 위한 정규화(1,2,3,...)
    - 물리 설계
        + 테이블 정의서
            + 엔티티, 관계, 속성, 식별자들의 테이블, 칼럼, 키 등으로 변환하는 과정  
            테이블 설계도 : [추천사이트](https://www.erdcloud.com)
        + 실제 테이블 퀴리를 작성
        + 외래키 설정

4. 프론트 설계
    - 시나리오 구성
        + 유스케이스
    - 화면구성
        + 프로토타입
    - 기능 명세서
        + 사이트에서 동작되는 기능 모음

5. 본격적인 개발 착수
6. 테스트
    - 요구사항을 토대로 만든 기능명세서를 기준으로 테스트 케이스 제작
7. 완성

# 관계 모델
- 1:1 관계
  + 1 : 1 관계란 어느 쪽 당사자의 입장에서 상대를 보더라도 반드시 단 하나씩 관계를 가지는 것을 말합니다.
  + 일부일처제인 혼인 제도에서, 즉 한 남자는 한 여자와, 한 여자는 한 남자와 밖에 결혼을 할 수 없습니다. 부인을 또는 남편을 2명 이상 둘 수 없다.
  + 이런 관계를 1:1 관계라고 합니다. 개념 상 하나로 합쳐도 전혀 관계가 없습니다.

- 1:M 관계 => 가장 많이 사용하는 형태
  + 1 : M관계는 한쪽이 관계를 맺은 쪽의 여러 객체를 갖는 것을 의미하며, 가장 흔하게 나타나는 매우 일반적인 형태입니다.
  + 부모와 자식 관계가 대표적 예가 될 수 있죠. 컴퓨터 디렉터리 구조 역시 마찬가지 입니다.
  + 테이블은 서로 선천적으로 관계를 가지고 있습니다.

- N:M 관계 => 많이 사용하는 형태
  + N : M 관계는 관계를 가진 양쪽 당사자 모두에서 1 : M 관계가 존재할 때 나타나는 모습입니다.
    + 예를들면, 학생과 과목의 관계입니다. 학생 입장에서는 여러 개 과목을 수강할 수 있고, 과목 입장에서 보면 여러 학생이 이 과목을 선택할 수 있습니다. 어느 쪽에서 봐도 다:다 관계가 성립됩니다.
  + 이 관계는 선천적으로는 테이블과 테이블의 관계가 없습니다. 각 테이블은 스스로 존재하고 있습니다. 그런데 이들 사이에 어떤 관계를 맺어 줌으로써 관계가 형성됩니다.
    + 위의 예시를 연장해보면 두 테이블 사이에 사실상 관계가 없기 때문에 두 테이블 사이에 수강신청이라는 관계를 맺어 관계를 형성한다.
  + n:m의 관계를 1:n과 m:1 두 관계로 바꿔주는 역할의 테이블이 필요하다.

# 정규화
* 관계형 데이터베이스를 설계할 때

* 데이터의 중복을 최소화여 저장 효율을 높이고 관계에서 바람
직하지 않은 삽입 , 삭제 , 갱신 이상이 발생하지 않도록 한다.

* 새로운 형태의 데이터가 삽입될 때 관계를 재구성할 필요성을
줄일 수 있다.

* 보다 간단한 관계 연산에 기초하여 검색을 보다 효율적 으로
할 수 있다.

* 장점
  - 데이터베이스의 일관성을 향상시킬 수 있다.
  - 데이터베이스의 확장성을 보장할 수 있다.
  - 데이터베이스의 논리적 구조를 견고하게 만들 수 있다.  
<br>

* 어노말리 (이상현상) => 정규화를 통해 해결
  - 정규화를 진행하지 않은 경우 발생
  - 릴레이션을 조작할 때 이상 현상이 발생하게 된다.  
<br>
  1. 삽입이상 (insertion Anomaly)
        - 데이터를 삽입할 때 불필요한 데이터가 함께 삽입되는 현상으로, 제 1 정규형에 문제가 있는 경우 발생하는 현상
        - 다른 데이터가 존재하지 않아 원하는 데이터를 입력할 수 없는 것이 삽입 이상 현상이다
  2. 삭제이상 (Deletion Anomaly)
        - 릴레이션의 한 튜플을 삭제함으로써 연쇄 삭제로 인해 정보의 손실이 발생하는 현상
  3. 갱신이상 (Update Anomaly)
        - 튜플중에서 일부 튜플의 값만을 갱신함으로써 정보의 모순성이 발생하는 현상
        - 이유는 데이터의 중복  

<br>

* 제 1 정규형
  - 모든 속성은 반드시 하나의 값을 가져야 한다
  - 값이라는 것은 원자성을 가져야 한다 . 즉 더 이상 쪼갤 수 없는 하나의 값만을 가져야 한다
    + 다가 속성
    + 복합 속성

<br>

* 제 2 정규형 (부분적 함수 종속)
  - 두 개 이상으로 구성된 PK(기본키)에서 발생한다. - 슈퍼키
  - R의 모든 속성이 후보 식별자 전체에 종속한다.
  - 일반 속성이 후보 식별자 전체에 종속되지 않고 일부에 종속된다면 2 정규형이 아니다.
  - 따라서 2 정규형이기 위해서는 모든 비 식별자 속성은 후보 식별자 속성에 완전 함수 종속돼야 한다.
  - 방법
    + 일반 속성 중에서 후보 식별자 전체에 종속적이지 않은 속성을 찾아 기본 엔터티에서 제거하고, 그 속성의 결정자를 주 식별자로 하는 새로운 상위 엔터티를 생성한다.

<br>

* 제3 정규형 (이행적 함수 종속)
  - 식별자가 아닌 일반 속성 간에는 종속성이 존재하지 않는다.
  - 제 3 정규화 방법
    + 일반 속성간의 종속 관계를 분해하는 것