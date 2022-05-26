# 컬렉션
* 자료를 어떻게 잘 저장하고 꺼낼것인가?

## 리스트(interface)
* 순서가 있다.(인덱스O) / 중복이 있다.
    - crud(create-추가, read-읽기, update-변경, delete-삭제)

* ArrayList(class) : add, get, set, remove
	- 장점 : 검색이 빠르다
	- 단점 : 데이터 입출력 속도가 느리다. (중간에 추가하면 뒤의 요소들의 인덱스를 바꿔줘야 하고, 공간이 부족하면 늘려야 하는 등의 작업이 필요하다.)
* LinkedLise(class) : add, get, set, remove
	- 장점 : 데이터 입출력 속도가 빠르다. (각 데이터가 순서대로 끈으로 연결되어있다고 생각)
	- 단점 : 검색이 느리다. (0번부터 찾고자 하는 번호까지 모두 순서대로 검색해야한다.)
	
## 셋(Set) 집합
* 인덱스가 없다. => 순서도 없다.
* 중복이 제거
* 종류
    - hashSet
    - TreeSet

## 맵(MAP)
* 종류
  - HashMap
  - TreeMap