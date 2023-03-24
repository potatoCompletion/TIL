# 예외처리(Exception)
**쉽게 생각할 수 있지만 실수하기 쉽고 서비스의 운영, 개선에 있어 필수적인 예외처리**  
<br/>
<br/>
<br/>

## 예외란?
- 사용자의 잘못된 조작이나 개발자의 코딩 실수로 인해 발생하는 프로그램 오류를 말한다.  
예외가 발생하면 프로그램은 곧바로 종료된다는 점에서 에러와 동일하나, 예외 처리를 통해 프로그램을 종료하지 않고 정상 실행 상태가 유지되도록 할 수도 있고, 예외 상황을 명확하게 파악하기 위해 부가 정보를 남길 수도 있다.
  <br/>
  <br/>
  <br/>
## 예외의 종류
- 큰 틀에서 분류하자면 두 가지로 나눌 수 있다.

1. Checked Exception: 개발자가 반드시 예외처리를 명시하도록 강제된다는 특징 (미처리 시 컴파일 불가)
2. Unchecked Exception: 예외처리를 하도록 강제받지 않음 (미처리 시 컴파일 가능)
   <br/>
   <br/>
   <br/>
## 인터넷에 있는 자료의 문제점
인터넷에서 Exception에 대해서 찾아보면 이러한 표를 쉽게 볼 수 있다.  
![흔히 볼 수 있는 익셉션 정리 표](../resource/checkedanduncheked.png)

이에 대해 현재 MS 개발자 이자 여러 자바, 스프링 관련 강의를 만드신 '백기선' 님이 남긴 영상이 있다.  

(백기선님 유튜브 영상 링크) https://www.youtube.com/watch?v=_WkMhytqoCc  

영상 제목이 다소 자극적이긴 하지만 영상의 요지는 '스프링에서의 기본설정을 왜 Checked Exception과 Unchecked Exception의 개념으로 정의하나?' 라고 해석했다.  
사실 저 표에 나와있는 '트랜잭션 처리' 부분은 정확한 표현이 아니다.  
우선 '트랜잭션' 이 'DB 트랜잭션'을 줄여쓴거라 가정하겠다.(어떠한 트랜잭션인지에 대한 설명이 없다)  
정확히 말하자면, 저 설명은 '스프링의 기본(default) 동작' 이라고 표현하는게 맞다고 본다.  
```
자바는 자체적으로 Checked Exception과 Unchecked Exception의 트랜잭션 처리를 지원하지 않는다!  
```
단지 **스프링 프레임워크** 가 해당 트랜잭션 처리를 도와주는 것 뿐이다.  
조금 더 정확히 표현하면 **스프링 프레임워크의 @Transactional 어노테이션의 기본동작**이다.  
하지만 그마저도 기본설정일 뿐이지, **Checked Exception도 롤백을 수행하게 할 수 있고, Unchecked Exception도 롤백을 수행하지 않게 설정할 수 있다**.  
  
물론 경험이 많거나, 해당 지식을 보유하신 분들은 내용을 잘 이해할 수 있겠지만, 처음 Exception의 개념에 대해서 공부하시는 분들의 이해에 혼선이 있을 것 같아 남긴다.
<br>
<br>
<br>
## 에러(Error)와 익셉션(Exception)은 다르다
팀에서 얘기를 하다보면, 나를 포함해 팀원들이 에러와 익셉션을 혼동해서 말하는 경우가 있다.  
개발자들 간에 커뮤니케이션 시에는 상황에 맞게끔 해석이 가능하지만 개념 자체를 혼동해선 안된다!

- 에러(Error, 오류)
    - 에러는 시스템이 종료되어야 할 수준과 같이 수습할 수 없는 심각한 문제를 의미한다. 이는 개발자가 미리 예측하여 방지할 수 없다.
- 익셉션(Exception, 예외)
    - 익셉션은 개발자가 구현한 로직에서 발생한 실수나 사용자의 영향에 의해 발생한다. 이는 개발자가 예측하여 방지할 수 있기 때문에 상황에 맞는 예외 처리(Exception Handle)를 해야한다.
#
## 예외처리 시 주의점
정확한 예외처리 방법에 대해서는 다루지 않고(레퍼런스나 기술 자료를 보는 것이 더 효율적이기 때문) 몇 가지 주의점에 대해서만 정리한다.

1. 자식 클래스에 대한 예외 처리는 부모 클래스보다 위에 선언해야 한다.
```java
try {
        ///예외가 발생할 가능성이 있는 코드
        .... ......... ;
        .............. .. ; << 1. 여기서 예외가 발생했을 때
        .............. .... ; << 2. 예외 발생한 곳 아래는 실행하지 않고,
        }
        catch(FileNotFoundException e){
        // FileNotFoundException은 Exception의 자식 클래스이기 때문에
        // 부모 클래스보다 위에 catch문을 선언해야 한다.
        }
        catch(Exception e){
        // 그 외 모든 익셉션
        }
        finally {
        ///무슨 일이 있든 항상 실행
        }
}
```
**자식 클래스가 부모 클래스보다 밑에 선언되면 안된다.**  
위 코드에서 만약 FileNotFoundException에 대한 catch문이 더 밑에 선언되어 있다면, FileNotFoundException은 Exception의 자식 클래스이기 때문에 Exception에 대한 catch문에 걸리게 된다.  

물론 요즘 IDE에서 해당 이슈를 표시해 실수를 방지해 주기는 하지만 왜 그런지는 알아야한다.

2. 예외 떠넘기기  
```java
public void method1(){
 try {
   method2(); // throws가 붙은 method2는 반드시 이렇게 try문 안에서 호출되어야 함.
   // method2가 떠넘긴 예외를 아래 catch문을 통해 처리해 주고 있다.
}
 catch (ClassNotFoundException e1) {
  System.out.println("클래스가 존재하지 않음.");
}

public void method2() throws ClassNotFoundException {
 Class clazz = Class.forName("java.lang.String22");
}
```
메서드 선언부 맨 끝에 throws를 붙임으로써 예외 처리를 익셉션이 발생한 메서드를 호출한 **호출 메서드**에게 떠넘길 수 있다.  
이러한 원리로 call stack 의 가장 마지막 entry point(main 메서드) 까지도 떠넘겨 질 수 있는데, 이 메서드마저 떠넘길 경우 JVM이 최종적으로 처리를 하게된다.  
하지만 이는 권장되는 사항은 아니기에 지양하는 것이 좋다.
#
## 자원 반납
try-catch-finally 구문을 굉장히 자주 사용하는 용도 중 하나가 자원 사용이다.  
ex)
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;
 
public class ResourceClose {
    public static void main(String[] args) {
        Scanner scanner = null;
        try {
            // scanner 생성
            scanner = new Scanner(new File("input.txt"));
            System.out.println(scanner.nextLine());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } finally {
            // scanner 리소스 반납
            if (scanner != null) {
                scanner.close();
            }
        }
    }
}
```
Java 7 이전 버전 까지는 위 코드와 같이 finally 부분에 자원을 반납하는 코드를 넣어 안전하게 자원을 사용하고 반납하는게 일반적인 사용법이었다. (물론 지금도 많이 사용하긴 한다)  
안전하게 처리는 가능하지만 finally 구문을 반드시 넣어줘야 해서 코드가 길어지고, 개발자의 실수로 자원 반납을 안하는 경우도 생길 수 있다.  
<br />
이러한 이유로 Java 7 이후 버전부터는 try with resource 라고 불리는 리소스 생성/반납에 대한 기능이 추가되었다.  
```java
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;
 
public class ResourceClose {
    public static void main(String[] args) {
        try (Scanner scanner = new Scanner(new File("input.txt"))) {
            System.out.println(scanner.nextLine());
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

try 옆 () 괄호 안에 자원을 사용할 클래스를 선언해 개발자의 실수를 미연에 방지하고, 간결한 코드를 작성할 수 있다.  

```
혹시 C#을 경험해 본 사람이라면 C#의 Using과 같다고 볼 수 있다.
```
<br/>
<br/>
<br/>

## 추가해서 정리할 사항
- 스프링의 예외처리 (ExceptionHandler, ControllerAdvice)