## 프로세스
* 실행중인 하나의 프로그램을 프로세스라고 부른다.

* 프로그램을 실행하면 운영체제로부터 필요한 자원을 받아서 필요한 코드를 실행하는 과정을 프로세스라고한다.

## 멀티프로세스
* 멀티프로세스는 동시에 여러가지 프로그램을 동작 시키는 것을 의미한다.

* 예를 들면 게임을 하면서 동영상도 켜고 문서 작업을 하면서 음악을 듣고 인터넷검색도 하는 등 여러프로그램들이 동시에 진행되는 것을 의미한다.

## 스레드개념
* 스레드란 한가닥의 실을 의미하는데 하나의 작업을 실행하기 위한 코드를 실처럼 엮어 놓았다고해서 유래한 이름이다.

* 모든 프로그램은 기본적으로 하나의 프로세스에 하나의 스레드를 가진다.

* 자바도 마찬가지로 main 메소드의 시작으로 프로그램이 시작되고 main 메소드의 끝으로 프로그램이 끝난다.

* 이런 시작과 끝의 흐름이 하나의 스레드이고 이런 하나의 스레드로 프로그램이 완결 되는 것을 싱글 스레드라고 부른다.

* 모든 자바프로그램은 main 메소드의 실행과 끝으로 이어지는 main 스레드를 가진다.

* 멀티스레드는 main 메소드의 실행이 진행중에 별도의 작업 메소드가 병렬로 처리가 되는 기술을 의미한다.
  - 자바는 멀티 스레드를 기본적으로 지원한다.  
  <br>
* 메인 스레드만 사용하는 경우 아래와 같은 메인 메서드를 실행하시면 for문 실행이 끝나고 이후에 비로소 아래 for문이 실행된다.
    ```java
    public class Main01 {

        public static void main(String[] args) {

            for(int i=0; i<5; i++) {
                System.out.println("딩");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            
            for(int i=0; i<5; i++) {
                System.out.println("동");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    ```

* 멀티 스레드를 사용하는 경우

* Runnable 인터페이스를 구현한 클래스
    ```java
    public class Dong01 implements Runnable {

        @Override
        public void run() {	// 새로운 스레드의 시작 => 메인과는 별도로 동작하는 스레드
            
            for(int i=0; i<5; i++) {
                System.out.println("동");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }            
        }
    }
    ```
    ```java
    public static void main(String[] args) {
		
		Dong01 d = new Dong01();
		
		Thread thread = new Thread(d);
		thread.start();	// 새로운 스레드가 생성된 후 스레드 동작이 시작됨
		
		for(int i=0; i<5; i++) {
			System.out.println("딩");
			try {
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
    ```

