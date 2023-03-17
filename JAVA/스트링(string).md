# 스트링(String)
**가장 많이 사용하면서도 가장 헷갈리기 쉬운 자료형 String**
#

## String 객체의 값은 불변이다.
- String 타입의 변수를 선언하고, 내부의 값을 바꾸면 값이 잘 바뀌는 것처럼 보인다.  
하지만 이는 실제로 값이 변경되는 것이 아니라, 스트링 풀이라는 저장송 미리 사용될 문자들이 저장되어 있는데,  
스트링 풀이라는 저장소에 미리 사용될 문자들이 전부 저장되어 있는데, 문자열 변수의 내용 자체가 바뀌는게 아니라 문자열 변수가 가리키는 주소만 바뀌는 것이다.

- 실제로 string을 리터럴로 선언하고 System.identityHashCode() 메서드를 통해 고유 주소값을 반환하면 확인 가능하다.

## 스트링 풀이란?
- 내가 한번 썼던 문자열들을 저장해놓는 창고라고 생각하면 된다. 예를들어 "abc"라는 문자열을 한번 사용했다면,  
이제 JVM은 해당 문자열을 Heap 영역에 저장해놓고, 프로그램에서 사용하는 문자열을 보고 해당 문자열이 이미 스트링 풀에 저장되어 있는지 확인한다.
- 스트링 풀이라는 저장소를 사용함으로써, 매번 같은 문자를 쓸 때 새로운 객체를 만들지 않아서 객체 생성 비용의 낭비를 줄일 수 있다.

## String의 intern() 메서드
- String은 내부에 intern() 이란 메서드가 있는데, 이 메서드를 간단히 표현한 말이 다음과 같다.
```
returns a canonical representation
```
이 메서드는 문자열이 스트링 풀에 있는지 먼저 확인 후에 있다면, 스트링 풀 속 해당 리터럴을 가리키는 주소를 가져올 것이고,  
만약 없다면 스트링 풀 속에 해당 리터럴을 넣고 그 주소를 가져올 것이다.

## intern() 메서드 간단 테스트
```java
@Test
public void stringInternTest() {
    String a = "Hello";
    String b = new String("Hello");

    System.out.println("a == b ? : " + (a == b)); // false
    System.out.println("a == b.intern() ? : " + (a == b.intern())); // true
}
```
위의 예시를 통해 intern() 메서드의 동작을 알 수 있다.

## 그럼 왜 String만 Pool로 관리하나?
- String은 다른 Reference Type 들과 다르게 **immutable(불변성을 가진)한 타입**이기 때문이다.  
```
다른 Reference Type -> 값 변경 시, 같은 주소 값의 객체의 실제 값이 변경  
String -> 값 변경 시, 다른 주소 값을 참조 (원래 참조하던 주소의 값은 변하지 않음)  
```
이러한 특성으로 인해 String만 Pool을 이용해 관리가 가능하다.

## 그러면 다른 참조 타입들도 불변으로 선언하면 되지 않나?
- 우선 과연 immutable한 타입에 장점만 존재하나? 를 따져봐야 한다.  
immutable 한 타입이라는 것은 어찌보면 성능에 불리한 면이 있다.  
가령 List가 불변 객체라고 생각해보자.  
```
List를 생성할 때 'a', 'b', 'c', 'd', 'e' 총 5개의 요소를 넣어줬다.  
그리고 List에 'f'라는 1개의 요소를 추가한다.  
이 때 만약 List가 불변타입이라면 새로운 6 byte의 공간을 가지는 List에 대한 메모리 할당을 또 진행해줘야 한다.
```
위와 같이 immutable 한 타입이라는 것은 단점도 많이 내포하고 있다.  
<br />
하지만 함수형 프로그래밍에서는 적극적으로 불변 자료구조를 사용한다.  
이에 대해서는 다음 정리에서..

## 그럼 왜 String은 immutable 하게 설계됐나?
- 자바를 만든 제임스 고슬링은 이 이유에 대해 이렇게 말하고 있다.
```
1. 캐싱: String이 불변객체이므로 이를 키값으로 이용하는 해싱 컬렉션의 퍼포먼스가 향상된다. (HashMap, HashTable, HashSet 등의 컬렉션들은 hashCode() 메서드를 통해 정수값을 받아 키값으로 이용한다).  
이 방식은 스트링 풀에서도 사용되기 때문에 기존에 저장된 String 값이 있는지에 대한 조회도 빠르게 일어난다
2. 보안: 문자열은 Java 어플리케이션에서 사용자 이름, 암호 등과 같은 중요한 정보를 저장하는데 널리 이용된다. 이러한 용도로 사용할 때 immutable 하다는 특징은 보안 측면에서 큰 강점을 가진다.
3. 복사가 필요없는 빠른 재사용성(성능): 문자열은 가장 많이 사용되는 타입이라고 봐도 무방하다. 이런 문자열이 생성될 때마다 메모리 할당을 받는다면 자원 소모가 심해진다.
4. 동기화 이슈: 불변객체라는 뜻은 곧 여러 스레드에서 접근해도 thread-safe 하다는 장점을 가진다.  
```

## 문자열 효율적으로 다루기
문자열 연산 중 많이 사용되는 연산 중 하나가 '합치기'이다. 이에 많이 사용되는 4가지 방법이 있다. 
```
1. concat
2. '+' 연산
3. StringBuilder
4. StringBuffer  
```
### concat
concat 메서드는 아래와 같이 구현되어 있다.
```java
public String concat(String str) {
    int otherLen = str.length();
    if (otherLen == 0) {
        return this;
    }
    int len = value.length;
    char buf[] = Arrays.copyOf(value, len + otherLen);
    str.getChars(buf, len);
    
    return new String(buf, true);
}
```
위 코드를 보게 되면 크게 세 가지의 로직이 일어나게 된다.
1. 두 문자열의 길이만큼 배열 할당
2. 배열에 두 값을 복사
3. 배열을 String으로 변환 후 반환

따라서 **새로운 메모리를 할당해서 저장하게 되고, 이는 String Pool 외부의 Heap 영역에 생성**된다.

### '+' 연산
ex)
```java
String c = "hello " + "world";
```
- '+' 연산자는 java 1.5 이전과 이후로 나눌 수 있다.  
1.5 이전 버전에서는 내부적으로 concat 메서드와 동일하게 수행되었고,  
이후 버전부터는 StringBuilder로 변환하여 처리한다.  
반복문이 아닌 일반적인 경우 많이 사용한다.
<br>
**주의할 점**  
'+' 연산을 StringBuilder로 변환해서 처리해주는 주체는 컴파일러이다.  
만약 반복문 안에서 문자열에 대한 '+' 연산을 하게 되면, 컴파일러가 StringBuilder로 변환해주지 못한다.

### StringBuilder
ex)
```java
StringBuilder sb = new StringBuilder();

sb.append("Hello ");
sb.append("world");
```
StringBuilder의 경우 흔히 **문자열을 합칠 때 가장 추천되는 방식**이다.  
그 이유는 바로 객체가 immutable 하지 않다는 점이다.  
다른 객체들과 같이 하나의 메모리를 차지하고 그 메모리 내부의 값을 계속 추가 및 삭제 해가며 진행된다.  
최종적으로 toString() 메서드를 호출할 때에만 string pool에 문자열이 생성되게 된다.
```
StringBuilder 생성 시 capacity를 주지 않을 경우 default는 16이다. 이 후 공간이 부족할 때 내부적으로 알아서 capacity를 늘림
```

### StringBuffer
사용법은 StringBuilder와 동일하다. StringBuilder와의 차이점은 **StringBuffer는 thread-safe** 하다는 것이다.  
StringBuffer 클래스는 동시에 이 객체에 접근했을 때, 동시성을 제어해주는 기능이 존재하고,  
StringBuilder 클래스는 동시성 제어 기능을 제외하여 상대적으로 동작속도가 빠르다.  
멀티스레드를 이용하여 하나의 문자열을 수정할 필요가 있다면 StringBuffer를, 그렇지 않다면 StringBuilder를 사용하는 것이 이상적이다.

## 추가해서 정리할 주제
1. 함수형 프로그래밍의 불변객체 사용 이유