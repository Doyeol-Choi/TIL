## Lombok
* VO나 DTO등을 편하게 사용할 수 있게 해주는 라이브러리 이다.

* getter/setter 메서드와 toString, equals, hashCode 등의 메서드를 어노테이션으로 사용할 수 있도록 해준다.

* 디펜던시 추가 뿐만 아니라 따로 설치도 해주어야 한다.
  - https://projectlombok.org/download 에서 다운받거나 메이븐 업데이트 등을 통해 받아진 lombok.jar 파일의 위치를 찾아간다.

  - cmd에서 해당 lombok.jar 파일이 있는 경로에 있다면 java -jar lombok.jar 를 입력해서 설치할 수 있고, 아니라면 java -jar [경로]\lombok.jar 를 통해 설치할 수 있다.

  - 설치는 사용중인 프레임워크의 위치를 잡아주고 install 하면 된다.

* 자주 사용하는 어노테이션
  - @Getter : getter 메소드 생성
  - @Setter : setter 메소드 생성
  - @NoArgsConstructor : 기본 생성자를 만들어 준다.
  - @AllArgsConstucto : 클래스의 모든 멤버변수를 받는 생성자를 만들어 준다.
  - @RequiredArgsConstructor : 클래스의 멤버변수중 final 키워드, 혹은 lombok 어노테이션인 @NonNull 이 붙은 멤버 변수만을 받는 생성자를 만들어 준다.
  - @Builder : 빌더패턴을 적용 시킨 방법으로 객체를 생성 할 수 있다.
    ```java
    post = Post.builder()
        .title(title)
        .content(content)
        .author(author)
        .build();
    ```
  - @ToString : 클래스의 멤버 변수들에 toString을 적용시켜 변수들을 출력 할 수 있게 해 준다.
  - <span style="color:yellowgreen">@Data : 위에 언급 했던 모든 어노테이션을 포함하는 어노테이션이다. 추가로 @EqualsAndHashCode 라는 어노테이션도 지원 한다.</span>