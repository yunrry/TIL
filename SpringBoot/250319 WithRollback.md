
> 인스타그램 클론코딩 댓글 서비스 구현 중 CommentService 객체의 매서드를 이해해보자. 


### **📌 `{createComment}WithRollback()`의 역할과 동작 원리**


```java
@Transactional public CommentEntity createCommentWithRollback(CommentEntity entity) {     return commentRepository.save(entity); }
```

---

## **✅ 1. `createCommentWithRollback()`의 역할**

📌 **이 메서드는 댓글(`CommentEntity`)을 데이터베이스에 저장하는 역할이다.**  
즉, `commentRepository.save(entity)`를 호출하여 **댓글을 저장하는 기능을 수행**.

📌 **이 메서드는 `@Transactional`이 적용되어 있기 때문에, 트랜잭션이 롤백될 가능성이 있습니다.**  
➡ **이름(`createCommentWithRollback`)에서 알 수 있듯이, 트랜잭션 롤백을 고려한 메서드일 가능성이 큽니다.**

---

## **✅ 2. `@Transactional`의 역할과 롤백 동작**

📌 **Spring의 `@Transactional`이 적용된 메서드는 예외가 발생하면 자동으로 롤백됨.**  
즉, `createCommentWithRollback()`이 실행되는 도중 예외가 발생하면, **저장 작업이 취소되고 롤백.**

### **예제: 정상적으로 저장되는 경우 (`rollback` 없음)**

`CommentEntity comment = new CommentEntity(); comment.setContent("테스트 댓글"); commentService.createCommentWithRollback(comment);`

📌 **이 경우에는 예외가 발생하지 않기 때문에, 댓글이 정상적으로 저장.**

---

### **예제: 예외가 발생하여 `rollback` 되는 경우**

```java
@Transactional public CommentEntity createCommentWithRollback(CommentEntity entity) {     
	CommentEntity savedComment = commentRepository.save(entity);// 예외 발생 (트랜잭션 롤백)     
	if (entity.getContent().contains("에러")) {         
		throw new RuntimeException("강제 예외 발생: 롤백 테스트");     
	}      
	return savedComment;
}`
```

`CommentEntity comment = new CommentEntity(); 
`comment.setContent("에러가 포함된 댓글");` `commentService.createCommentWithRollback(comment); // 예외 발생으로 롤백됨`

📌 **이 경우, 예외(`RuntimeException`)가 발생하여 트랜잭션이 롤백되므로 댓글이 저장되지 않음.**

---

## **✅ 3. `createCommentWithRollback()`와 `createComment()`의 차이점**

📌 **두 메서드의 주요 차이점은 다음과 같다.**

|메서드|기능|예외 발생 시 롤백 여부|
|---|---|---|
|**`createComment()`**|`userId`, `postId` 유효성 검사를 수행한 후 댓글 저장|**예외 발생 시 롤백됨**|
|**`createCommentWithRollback()`**|단순히 `commentRepository.save()`를 호출하여 댓글 저장|**예외 발생 시 롤백됨**|

📌 `createCommentWithRollback()`은 **별도의 검증 없이 댓글을 저장하는 단순한 메서드**.  
→ **일반적인 댓글 생성 로직 (`createComment()`)과는 다르게 유효성 검사를 하지 않기 때문에, 테스트에서 직접 `save()`를 호출할 때 사용된다.**

---

## **✅ 4. `createCommentWithRollback()`이 사용될 가능성이 있는 상황**

### **1️⃣ 테스트 코드에서 직접 `commentRepository.save()`를 호출할 때**

`CommentServiceTest`에서 **댓글을 저장하는 단순한 테스트를 수행할 때** 사용할 수 있다.

java

복사편집


`@Test
`void createComment_ShouldSaveComment() { 
`// Given: commentRepository.save()가 실행될 때 mockComment를 반환하도록 설정`
	`when(commentRepository.save(any(CommentEntity.class)))
	`.thenReturn(mockComment);`
 
	  `// When: 새로운 댓글을 생성 `
	`CommentEntity savedComment = commentService
	.createCommentWithRollback(mockComment);      `
	
	// Then: 저장된 댓글이 예상대로 반환되는지 확인     
	assertNotNull(savedComment);     
	assertEquals(mockComment.getId(), savedComment.getId());
	assertEquals(mockComment.getContent(), savedComment.getContent());      
	
	// Verify: commentRepository.save()가 1번 호출되었는지 확인
	verify(commentRepository, times(1)).save(any(CommentEntity.class)); 
`}`

📌 `createCommentWithRollback()`은 **유효성 검사를 하지 않고, 단순히 댓글을 저장하는 기능만 수행**하기 때문에 **테스트에서 직접 `save()`를 확인할 때 적합하다.**

---

### **2️⃣ 트랜잭션 롤백이 필요한 테스트 시나리오**

만약 **특정 예외 발생 시 트랜잭션이 롤백되는지 확인하는 테스트**를 작성해야 한다면, `createCommentWithRollback()`을 활용할 수 있다.
```java
@Test
void createCommentWithRollback_ShouldRollback_WhenExceptionOccurs() {
	// Given: 댓글 내용에 "rollback"이 포함되면 예외 발생     
	CommentEntity comment = new CommentEntity();
	comment.setContent("rollback test");

	when(commentRepository.save(any(CommentEntity.class)))
	.thenThrow(new RuntimeException("강제 예외"));
	
	// When & Then: 예외 발생 여부 확인 (트랜잭션 롤백 테스트)
	assertThrows(RuntimeException.class, 
	() -> commentService.createCommentWithRollback(comment));
	
	// Verify: 댓글이 실제로 저장되지 않았는지 확인
	verify(commentRepository, times(1)).save(any(CommentEntity.class)); 
}
```


📌 **예외 발생 시 트랜잭션이 롤백되는지 확인하는 테스트에서 사용할 수 있습니다.**

---

## **✅ 5. `createCommentWithRollback()`을 유지해야 하는 이유**

이 메서드는 **테스트 코드에서 직접 `save()` 동작을 검증할 때 매우 유용**하기 때문에, 유지하는 것이 좋다.

📌 **`createCommentWithRollback()`이 유용한 경우** 1️⃣ **테스트 코드에서 `commentRepository.save()`를 직접 호출하여 댓글 저장 여부를 확인할 때**  
2️⃣ **트랜잭션 롤백이 제대로 수행되는지 확인하는 테스트를 작성할 때**  
3️⃣ **유효성 검사를 하지 않고 단순한 댓글 저장이 필요할 때**

---

## **🚀 최종 정리**

| 메서드                               | 기능                                         | 예외 발생 시 롤백 여부 | 주요 용도         |
| --------------------------------- | ------------------------------------------ | ------------- | ------------- |
| **`createComment()`**             | `userId`, `postId` 유효성 검사를 수행한 후 댓글 저장     | ✅ 롤백됨         | 실제 애플리케이션 로직  |
| **`createCommentWithRollback()`** | 단순히 `commentRepository.save()`를 호출하여 댓글 저장 | ✅ 롤백됨         | 테스트 또는 단순한 저장 |

📌 `createCommentWithRollback()`은 주로 **테스트 환경에서 댓글을 저장할 때 사용**되며, **유효성 검사를 하지 않고 트랜잭션 롤백을 확인하는 경우에도 활용**할 수 있다.


