# Getter와 Setter
**무분별하게 사용될 여지가 있는 Getter와 Setter의 올바른 사용법에 대해서 짚고 넘어가자**
<br/>
<br/>
<br/>
## Getter와 Setter란?
Getter와 Setter는 객체의 속성에 접근하기 위한 메서드이다.  
필드 값은 private으로 설정하고, 외부에서 접근할 수 없도록 하여 객체의 속성을 보호하기 위해 사용된다.  
ex)
```java
public class Person {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

  <br/>
  <br/>
  <br/>

## Getter의 무분별한 사용
Getter는 객체의 속성을 외부에서 접근할 수 있도록 해준다.  
그래서 객체의 속성을 꺼내와서 객체의 상태를 판단하는 코드를 작성할 수 있다.  
ex)
```java
Person person = Person.fromRequest(request);

if (person.getAge() > 20) {
    System.out.println("성인입니다.");
} else {
    System.out.println("미성년자입니다.");
}
```

이런 식으로 Getter를 이용해 값을 조회하고, 그에 따라 필요한 동작을 하는 코드를 쉽게 볼 수 있다.  
하지만 이런 방식이 좋은 방식일까?  
지금 예시로 든 코드는 20살 이상이면 성인이라는 사회적 통념을 이용하고 있다.  
그렇기 때문에 처음 코드를 보는 사람도 잘 작성된 코드라고 오해할 수 있다. 하지만 아래와 같은 코드라면 어떨까?
```java
SystemCode systemCode = SystemCode.fromRequest(request);

if (systemCode.getCode().equals("1")) {
    doSomething();
} 
if (systemCode.getCode().equals("2")) {
    doSomethingElse();
} 
if (systemCode.getCode().equals("3")) {
    doAnotherThing();
}
```
이 코드의 문제점은 두 가지라고 볼 수 있다.  
첫번째로 "1", "2", "3"이라는 코드 값이 무엇을 의미하는지 알 수 없다. 즉, 하드코딩(매직넘버)이다.  
두번째로 getter를 통해 값을 꺼내와서 상태판단을 하고 있다. 이렇게 되면 개발자가 코드를 이해하기 어려워지고 객체에 대한 상태 판단을 외부에서 하게 되어 관심사 분리가 이루어지지 않는다.  
<br/>
<br/>
<br/>
## Getter 사용을 지양하자
Getter를 사용하지 말라는 것은 아니다.  
하지만 더 가독성이 뛰어나고 지속 가능한 코드를 위해 아래와 같은 방식을 추천한다.  
```java
SystemCode systemCode = SystemCode.fromRequest(request);

if (systemCode.isNormal()) {
    doSomething();
}
if (systemCode.isError()) {
    doSomethingElse();
}
if (systemCode.isWarning()) {
    doAnotherThing();
}
```
Getter를 사용해 값을 꺼내서 판단 로직을 작성한 코드보다 훨씬 코드를 이해하기 쉬워졌다.  
결국 본질은 하드코딩을 하지말자는 취지와 같다. 이 코드가 무엇을 의미하는지 한번 감싸서 파악할 수 있게 만드는게 핵심이다. 물론 여기에 "1", "2", "3"과 같은 코드들은 enum으로 관리하는 것이 좋다.
<br/>
<br/>
<br/>
## Setter의 무분별한 사용
Setter는 객체의 값을 변경하는 역할인 만큼 Getter보다 더 조심해야 한다.  
```java
User user = User.fromRequest(request);

if (someCondition) {
    user.setRole("ADMIN");
}
```
이런 식으로 setter를 사용하게 되면 user를 다루는 정책에 대해 무방비하게 노출된다.  
만약 이렇게 user의 role을 변경하는 코드가 여러 곳에 존재한다면?  
나중에 user의 role을 변경하는 정책이 바뀌게 되면 모든 코드를 수정해야 한다.
<br/>
<br/>
<br/>
## Setter 사용을 지양하자
해당 객체의 속성을 변경하는 로직은 해당 객체 내부에서 처리하는 것이 좋다.  
```java
User user = User.fromRequest(request);

user.changeRoleBySomeCondition(someCondition);
```
이렇게만 적어두고 실제 어떤 role로 변경되는지는 User 객체 내부에서 처리하게 된다.  
이런 식으로 사용하게 되면 나중에 정책이 바뀔 때에도 쉽게 대응이 가능하고, 클래스 역할 분리 관점에서도 더 명확하게 코드를 관리할 수 있다.