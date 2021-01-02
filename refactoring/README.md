# 리팩토링

리팩토링을 공부하면 접하는 용어가 매우 많다. (eg. extract method, inline method, 전략패턴, 템플릿메서드 등등 )  
책을 한번 본다고 해서 용어들을 기억하기는 불가능하다고 생각한다. 그래서 필요시 찾아보기 편하게 정리하고자 한다.  


**이 책을 통하여 바라는 점**  
리팩토링 기법들을 익혀 실제 리팩토링할 때 써먹으면 좋겠다.   
또한 같은 팀원의 PR에 좀 더 도움되는 코드리뷰를 하고 싶다.

---

### 1. 메서드 정리
<details><summary>메서드 추출 (extraxt method)</summary>
<p>
플
> 어떤 코드를 그룹으로 묶어도 되겠다고 판단될 때 해당 코드를 메서드로 빼내자.

메서드 목적에 부합하는 네이밍을 하자.   
임시변수가 너무 많아 메서드 추출이 힘들다면, 이 때는 임시변수를 메서드 호출로 전환 / 메서드를 메서드 객체로 전환을 고려해보자.

변수를 두 개 이상 반환해야하는 메서드는 하나를 반환하는 두개의 메서드로 만들 수는 없는지 고려하자.
</p>
</details>

<details><summary>메서드 내용 직접 삽입 (inline method)</summary>
<p>

> 메서드 기능이 너무 단순하여 뻔할 경우 메서드를 호출하는 곳에 넣어버리자.

```java
boolean moreThanFiveLateDeliveries() {
    return _numberOfLateDeliveries > 5;
}
```

#### why?
메서드명(moreThanFiveLateDeliveries) 을 읽는 것 보다 코드가 더 직관적이고 이해하기 쉽다.

</p>
</details>

<details><summary>메서드를 메서드 객체로 전환 (replace method with method object)</summary>
<p>

> 지역변수 때문에 Extraxt Method 를 적용할 수 없는 긴 메서드의 경우   
> 1. 긴 메서드 자체를 객체로 전환해서 모든 지역변수를 객체의 필드로 만들자.    
> 2. 긴 메서드를 객체 안의 여러 메서드로 쪼개자. (extract method)

근데 이거... 쓰는게 맞는건지는 잘 모르겠다. 공감이 안됨.

</p>
</details>

<details><summary>임시변수 내용 직접 삽입 (inline temp)</summary>
<p>

> 간단한 수식을 대입받는 임시변수로 인해 다른 리팩토링 기법 적용이 힘들 경우  
> 그 임시변수를 참조하는 부분을 전부 수식으로 바꾸자.

```java
// bad
double basePrice = anOrder.basePrice();
return basePrice > 1000;

// good
return anOrder.basePrice() > 1000
```

흠.. 공감이 잘 안되네.  
basePrice 임시변수를 네이밍을 잘 지어서 사용하면 읽기 쉬운 경우도 있지 않을까 싶은데..

이 기법은 바로 아래기법(replace temp with query)의 동기라고 한다.  
순수히 inline temp만 하려면 하지 않아도 되지만,  
extract method 등의 다른 리팩토링에 방해가 된다면 inline temp 를 적용해야 한다고 한다.
</p>
</details>

<details><summary>임시변수 -> 메서드 호출로 전환 (replace temp with query)</summary>
<p>

> 수식의 결과를 저장하는 임시변수가 있을 경우  
> 1. 그 수식을 메서드로 추출하고  
> 2. 임시변수 사용처를 추출한 메서드로 전부 교체

#### why?
임시변수는 일시적이고 국소적이다.  
메서드 호출로 전환하면 클래스 안 모든 메서드가 이용할 수 있다. -> 코드가 깔끔해진다.

대부분의 경우 메서드 추출을 하기 전에 반드시 적용해야 한다. -> 지역변수가 많을수록 메서드 추출이 힘들어지는데 이를 줄이기 위함.

```java
// bad
double getPrice() {
    int basePrice = _quantity * _itemPrice;
    double discountFactor;
    if (basePrice > 1000) discountFactor = 0.95;
    else discountFactor = 0.98;
    return basePrice * discountFactor;
}

// good
double getPrice() {
    return basePrice() * discountFactor();
}

private int basePrice() {
    return _quantity * _itemPrice;
}

private double discountFactor() {
    if (basePrice() > 1000) return 0.95;
    else return 0.98;
}
```

</p>
</details>


<details><summary>직관적인 임시변수 사용 (introduce explaining variable)</summary>
<p>

> 사용된 수식이 복잡한 경우  
> 수식의 결과/일부분 을 용도에 부합하는 직관적 이름의 임시변수 만들자.

#### why?
수식이 너무 복잡해서 이해하기 힘들다.

```java
// bad
double price() {
    // 결제액 = 총 구매액 - 대량 구매 할인 + 배송비
    return _quantity * _itemPrice - Math.max(0, _quantity - 500) * _itemPrice * 0.05 + Math.min(_quantity * _itemPrice * 0.1, 100.0);
}

// good
double price() {
    final double basePrice = _quantity * _itemPrice;
    final double quantityDiscount = Math.max(0, _quantity - 500) * _itemPrice * 0.05;
    final double shipping = Math.min(basePrice * 0.1, 100.0);
    return basePrice - quantityDiscount + shipping;
}

// good2 (replace temp with query)
double price() {
    return basePrice() - quantityDiscount() + shipping();
}

private double quantityDiscount() {
    return Math.max(0, _quantity - 500) * _itemPrice * 0.05;
}

private double shipping() {
    return Math.min(basePrice() * 0.1, 100.0);
}

private double basePrice() {
    return _quantity * _itemPrice;
}
```

</p>
</details>
  
<details><summary>임시변수 분리 (split temporary variable)</summary>
<p>

> 임시변수에 여러 번 값이 대입되는 경우 (루프 변수나 값 누적용 임시변수는 예외)  
> 각 대입마다 다른 임시변수를 사용하자.

#### why?
값이 두번 이상 대입되면 여러 용도로 사용된다는 의미이다. 이는 혼동을 줄 수 있는 코드이므로 분리해야 한다.

```java
// bad
double getDistanceTravelled(int time) {
    double result;
    double acc = _primaryForce / _mass;
    int primaryTime = Math.min(time, _delay);
    result = 0.5 * acc * primaryTime * primaryTime;
    int secondaryTime = time - _delay;

    if (secondaryTime > 0) {
        double primaryVel = acc * _delay;
        acc = (_primaryForce + _secondaryForce) / _mass;
        result += primaryVel * secondaryTime + 0.5 * acc * secondaryTime * secondaryTime;
    }
    return result;
}

// good
double getDistanceTravelled(int time) {
    double result;
    final double **primaryAcc** = _primaryForce / _mass;
    int primaryTime = Math.min(time, _delay);
    result = 0.5 * **primaryAcc** * primaryTime * primaryTime;
    int secondaryTime = time - _delay;

    if (secondaryTime > 0) {
        double primaryVel = **primaryAcc** * _delay;
        **final double secondaryAcc** = (_primaryForce + _secondaryForce) / _mass;
        result += primaryVel * secondaryTime + 0.5 * **secondaryAcc** * secondaryTime * secondaryTime;
    }
    return result;
}

주의: 아직도 리팩토링할 거리는 남아있음.
```

</p>
</details>

<details><summary>매개변수로의 값 대입 제거 (remove assignments to parameters)</summary>
<p>

> 매개변수로 값을 대입하는 코드가 있는 경우  
> 매개변수 대신 임시변수를 사용하자.

#### why?
매개변수를 변경하는 경우 혼동이 더 발생할 수 있다.

```java
int discount (int inputVal, int quantity, int yearToDate) {
    if (inputVal > 50) inputVal -= 2;
    if (quantity > 100) inputVal -= 1;
    if (yearToDate > 10000) inputVal -= 4;
    return inputVal;
}

int discount (final int inputVal, final int quantity, final int yearToDate) {
    int result = inputVal;
    if (inputVal > 50) result -= 2;
    if (quantity > 100) result -= 1;
    if (yearToDate > 10000) result -= 4;
    return result;   
}
```

</p>
</details>

<details><summary>알고리즘 전환 (substitute algorithm)</summary>
<p>
    
> 알고리즘을 더 분명한 것으로 교체해야 하는 경우  
> 해당 메서드의 내용을 새 알고리즘으로 바꾸자.

</p>
</details>

&nbsp;

### 2. 객체 간의 기능 이동
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

### 3. 데이터 체계화

### 4. 조건문 간결화

### 5. 메서드 호출 단순화

### 6. 일반화 처리
