# 엔티티(Entity)와 Wrapper 클래스
**무의식적으로 사용하던 Wrapper 클래스의 사용 이유에 대해 알아보자**  
<br/>
<br/>
<br/>

## Wrapper 클래스란?
Wrapper 클래스는 Java의 기본형(primitive type) 데이터를 객체로 감싸는 클래스를 말한다.  
> ex) int -> Integer, double -> Double 등

<br/>
<br/>
<br/>

## Wrapper 클래스의 사용 이유
1. **컬렉션 프레임워크 사용**: Java의 컬렉션 프레임워크는 객체만을 저장할 수 있기 때문에, 기본형 데이터를 저장하려면 Wrapper 클래스를 사용해야 한다.  
   ex) List<Integer>, Map<String, Double> 등
2. **null 값 처리**: 기본형 데이터는 null 값을 가질 수 없지만, Wrapper 클래스는 null 값을 가질 수 있다. 이를 통해 데이터가 없는 상태를 표현할 수 있다.
3. **유틸리티 메서드 제공**: Wrapper 클래스는 기본형 데이터에 대한 다양한 유틸리티 메서드를 제공한다.  
   ex) Integer.parseInt(), Double.valueOf() 등
4. **자동 박싱과 언박싱**: Java는 자동으로 기본형 데이터를 Wrapper 클래스로 변환(박싱)하거나, Wrapper 클래스를 기본형 데이터로 변환(언박싱)할 수 있다. 이를 통해 코드의 가독성을 높일 수 있다.

<br/>
<br/>
<br/>

## 엔티티(Entity)는 왜 Wrapper 클래스를 사용하나?
엔티티(Entity) 클래스는 데이터베이스의 테이블과 매핑되는 객체이다. 그러므로 DB의 데이터를 정확하게 받을 수 있어야 한다.  
예를 들어 어떤 서비스에서 유저마다 새로운 번호를 신청해서 받는다고 가정해보자.  
신청받은 번호를 DB에 저장할 때, 아직 번호를 신청하지 않은 유저는 해당 컬럼 값이 NULL이 될 수 있다.  
이 때, Entity에서 만약 primitive 타입(int, long 등)을 사용한다면, 해당 컬럼 값이 NULL일 때 기본값인 0이 저장될 것이다. (기본 자료형은 null을 표현할 수 없으므로)  
0과 NULL은 완전히 다른 의미를 가지므로, 이는 잘못된 데이터가 저장되는 결과를 초래할 수 있다.

```java
// primitive 타입 사용
@Column(name = "applied_number")
private int appliedNumber; // DB 값이 NULL이어도 0이 들어감

// Wrapper 타입 사용
@Column(name = "applied_number")
private Integer appliedNumber; // DB 값이 NULL이면 필드도 null로 유지

```
따라서 엔티티(Entity) 클래스에서는 데이터베이스의 NULL 값을 정확하게 표현하기 위해 Wrapper 클래스를 사용하는 것이 일반적이다.
