
---

## **✅ 1. 전체 코드 분석**

java

복사편집

```java 
void createComment_ShouldThrowException_WhenUserDoesNotExist() {
	// Given: 모든 userId에 대해 false 반환
	when(userRepository.existsById(anyLong())).thenReturn(false); 
	
	// When & Then: 예외 발생 여부 확인     
	Exception exception = assertThrows(IllegalArgumentException.class,             () -> commentService.createComment(mockComment));      // 예외 메시지 검증     
	assertTrue(exception.getMessage().contains("존재하지 않는 사용자 ID입니다"));
	
	// Verify: userRepository.existsById()가 1번 호출되었는지 확인    
	verify(userRepository, times(1)).existsById(anyLong());
	// Verify: commentRepository.save()가 호출되지 않았는지 확인    
	verify(commentRepository, never()).save(any(CommentEntity.class)); 
	}
```
---

## **✅ 2. 개별 문법 설명**

### **🔹 1) `when().thenReturn()` – Mockito를 사용한 스텁 설정**

`when(userRepository.existsById(anyLong())).thenReturn(false);`

📌 **설명**
- `userRepository.existsById(anyLong())` → `existsById()`가 **어떤 `Long` 값**으로 호출되더라도 실행됨 (`anyLong()` 사용).
- `.thenReturn(false);` → **이 메서드가 호출되면 항상 `false`를 반환**하도록 설정.
- 즉, `userId`가 존재하지 않는 상태를 **가짜(Mock) 데이터**로 설정.

✅ **Mockito의 `when().thenReturn()` 구조**

`when(가짜 데이터가 필요한 메서드 호출).thenReturn(리턴 값);`

---

### **🔹 2) `assertThrows()` – 예외 발생 여부 확인 (JUnit 5)**

`Exception exception = assertThrows(IllegalArgumentException.class,         () -> commentService.createComment(mockComment));`

📌 **설명**
- `assertThrows(예상 예외 클래스, 실행할 코드)`  
    → **예외가 발생하는지 확인하는 JUnit 5의 테스트 메서드**
- `commentService.createComment(mockComment)` 실행 시 **예외(`IllegalArgumentException`)가 발생하는지 확인**

✅ **JUnit 5 `assertThrows()` 구조**

`assertThrows(예상 예외 클래스, () -> 실행할 코드);`

---

### **🔹 3) `assertTrue()` – 예외 메시지 검증**

`assertTrue(exception.getMessage().contains("존재하지 않는 사용자 ID입니다"));`

📌 **설명**
- `exception.getMessage()` → 발생한 예외의 메시지를 가져옴
- `.contains("존재하지 않는 사용자 ID입니다")` → 예외 메시지에 **특정 문자열이 포함되어 있는지 확인**
- **예외 메시지가 예상과 다르면 테스트 실패**

✅ **JUnit 5 `assertTrue()` 구조**

`assertTrue(조건식);`

---

### **🔹 4) `verify()` – 특정 메서드가 호출되었는지 검증**

`verify(userRepository, times(1)).existsById(anyLong());`

📌 **설명**
- `verify(가짜 객체, 호출 횟수).메서드(매개변수);`  
    → **해당 메서드가 몇 번 호출되었는지 검증**
- `times(1)` → **한 번만 호출되었는지 확인**
- `anyLong()` → **어떤 `Long` 값이 들어와도 호출된 것으로 간주**

✅ **Mockito `verify()` 구조**

`verify(가짜 객체, times(호출 횟수)).메서드(매개변수);`

---

### **🔹 5) `never()` – 특정 메서드가 호출되지 않았는지 검증**

`verify(commentRepository, never()).save(any(CommentEntity.class));`

📌 **설명**
- `verify(commentRepository, never()).save(any(CommentEntity.class));`  
    → `commentRepository.save()` 메서드가 **한 번도 호출되지 않았는지 확인**
- `never()` → **메서드가 전혀 실행되지 않았어야 함**
- `any(CommentEntity.class)` → **모든 `CommentEntity` 타입의 객체를 인자로 받는 호출을 검증**

✅ **Mockito `verify()` (호출되지 않았는지 검증)**

`verify(가짜 객체, never()).메서드(매개변수);`

---

## **✅ 3. 전체 문법 구조 정리**

|구문|역할|예제|
|---|---|---|
|**Mockito `when().thenReturn()`**|가짜 객체의 반환값 설정|`when(userRepository.existsById(anyLong())).thenReturn(false);`|
|**JUnit `assertThrows()`**|예외 발생 여부 검증|`assertThrows(IllegalArgumentException.class, () -> 실행할 코드);`|
|**JUnit `assertTrue()`**|예외 메시지 검증|`assertTrue(exception.getMessage().contains("존재하지 않는 사용자 ID입니다"));`|
|**Mockito `verify(times(n))`**|특정 메서드가 `n`번 호출되었는지 확인|`verify(userRepository, times(1)).existsById(anyLong());`|
|**Mockito `verify(never())`**|특정 메서드가 전혀 호출되지 않았는지 확인|`verify(commentRepository, never()).save(any(CommentEntity.class));`|
