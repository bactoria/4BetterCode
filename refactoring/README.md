# 리팩토링

리팩토링 기법은 매우 많다. 
용어들 (eg. extract method, inline method, 전략패턴, 템플릿메서드 등등 )이 많아서 책을 한번 본다고 해서 기억하기는 불가능하다고 생각한다. 그래서 필요시 찾아보기 편하게 정리하고자 한다.  


**이 책을 통하여 바라는 점**  
리팩토링 기법들을 익혀 실제 리팩토링할 때 써먹으면 좋겠다.   
또한 같은 팀원의 PR에 좀 더 도움되는 코드리뷰를 하고 싶다.

---

**메서드 정리**
<details><summary>메서드 추출 (extraxt method)</summary>
<p>

#### hidden block test
```java
print("hello!")
```
</p>
</details>

- 메서드 내용 직접 삽입 (inline method)
- 메서드를 메서드 객체로 전환 (replace method with method object)
- 임시변수 내용 직접 삽입 (inline temp)
- 임시변수 -> 메서드 호출로 전환 (replace temp with query)
- 임시변수 직관적이게 사용 (introduce explaining variable)
- 임시변수 분리 (split temporary variable)
- 매개변수로의 값 대입 제거 (remove assignments to parameters)
- 알고리즘 전환 (substitute algorithm)    

&nbsp;

**객체 간의 기능 이동**
- 메서드 이동 (move method)
- 필드 이동 (move field)
- 클래스 추출 (extract class)
- 클래스 내용 직접 삽입 (inline class)
- 대리 객체 은폐 (hide delegate)
- 과잉 중개 메서드 제거 (remove middle man)
- 외래 클래스에서 메서드 추가 (introduce foreign method)
- 국소적 상속확장 클래스 사용 (introduce local extension)

&nbsp;

(TODO)

**데이터 체계화**

**조건문 간결화**

**메서드 호출 단순화**

**일반화 처리**
