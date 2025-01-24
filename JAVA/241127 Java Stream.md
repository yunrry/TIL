
# 스트림(Stream)

- [https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)
- 람다 표현식에 대한 자바 튜토리얼: [Lambda Expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
- 메서드 참조에 대한 자바 튜토리얼: [Method References](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)
    
- java 8부터
- 람다를 활용해 배열과 컬렉션을 함수형으로 간단하게 처리할 수 있는 기술
- 기존 for문 iterator를 사용하면 코드가 길어져서 -> 코드 간결하게 하기 위해 사용
    
- 특징
	- 원본 데이터 소스를 변경하지 않고, 읽기만 한다
	- 일회용: 한 번 사용하면 닫혀서 재사용이 불가능
	- 최종 연산 전까지 중간 연산을 수행하지 않음(lazy binding)
	- 작업을 내부 반복으로 처리 : forEach() 매개변수에 대입된 람다식을 데이터 소스의 모든 요소에 적용
	- 병렬 처리 쉬움: 멀티스레드 사용
	- 기본형 스트림을 제공: IntStream .sum(), .average() 등

- 중간 연산(가공) : 데이터 변환환
	- Filtering
	- Mapping
	- Sorting
	- 기타연산: 중복제거, 크기제한. 중간 작업결과 확인
    
- 최종 연산(결과를 만드는) 
	- Calculating : 기본형 타입 
		- .count() / .sum() / min()
	- Reduction
	- Collecting
	- Matching
	- Iterating (forEach)
	- Finding
