# REST API
****
<br>
<br>
<br>

## REST란?
REpresentatinal State Transfer의 약자로 자원을 이름으로 구분하여 해당 자원의 상태를 주고 받는 모든 것을 의미한다.  
동작 특징으로 정의하자면,

1. HTTP URI를 통해 자원(Resource)을 명시한다.
2. HTTP Method(GET, POST, PUT, DELETE 등)를 통해 해당 자원에 대한 CRUD 작업을 적용하는 것을 의미한다.

## REST 구성 요소
REST는 다음과 같은 3가지로 구성이 되어있다.

1. 자원(Resource): HTTP URI
2. 자원에 대한 행위(Verb): HTTP Method
3. 자원에 대한 행위의 내용(Representations): HTTP Message Payload
```
페이로드(Payload)란?

페이로드는 전송되는 데이터를 의미한다.
데이터를 전송할 때에는 헤더, 메타 데이터, 에러 체크 비트 등과 같은 다양한 요소를 포함해서 전송한다.
이 때, 보내고자 하는 데이터 자체를 의미하는 것이 페이로드이다.
우리가 택배를 보낼 때를 예시로 들면, 택배 물건이 페이로드이고, 박스나 흡충제 같은 부가요소들은 페이로드가 아니다.
```

## REST의 원칙

### 1. Server-Client(서버-클라이언트 구조)
REST API는 클라이언트-서버 아키텍처 스타일을 구현한다. 클라이언트가 리소스에 대한 요청을 보내며, 클라이언트는 데이터 저장소(ex: DB)와 연결되어 있지 않다.  
데이터 저장소는 서버에서 연결한다. 서버는 사용자 인터페이스에 관여하지 않는다. 클라이언트와 서버는 상호 의존적이며, 이는 REST를 유연하고 확장 가능하게 만든다.

### 2. Stateless(무상태)
서버에 클라이언트에 대한 정보가 포함되어 있지 않음을 의미한다. 요청 처리에 필요한 모든 정보가 요청에 포함된다.

### 3. Cacheable(캐시 처리 가능)
각 응답에는 캐시 가능 여부와 응답을 캐싱할 수 있는 기간을 알려주는 정보가 있어야 한다. 캐싱 가능한 경우 유사한 요청에서 클라이언트는 서버에 반복적으로 요청을 보내지 않고도 동일한 데이터를 사용할 수 있다.  

### 4. Layered System(계층화)
서버는 계층화된 시스템 아키텍처를 사용한다. 클라이언트와 API 서버 사이에 중간 단계로 보안 계층, 캐싱 계층 등 여러 서버 계층이 있는 형태이다.  
이러한 구조는 Proxy, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 한다.
각 레이어는 바로 다음 레이어만 보고 상호 작용하도록 제한되어야 한다.
클라이언트는 오직 API 서버만 호출해야 하고, 서버의 동작은 일반적으로 알 수 없다.

### 5. Uniform Interface(인터페이스 일관성)
자원에 대한 요청을 통일하고 한정적인 인터페이스로 수행하는 아키텍처 스타일이다.  
인터페이스 일관성을 지키기 위한 4가지 원칙이 존재한다.
1. 자원식별  
각 리소스에는 리소스 상태와 독립적인 ID가 있어야 한다. URL은 식별자 역할을 한다.
2. 표현을 통한 자원 조작  
클라이언트가 가지고 있는 리소스 표현(GET, POST 등)에는 리소스를 삭제하거나 수정하는 데 필요한 데이터가 포함된다.  
3. 자기 설명 메시지.  
이러한 메시지에는 수신자가 이해할 수 있도록 모든 정보가 포함되어 있다. 별도의 문서나 메시지에는 추가 정보가 필요하지 않다.
4. HATEOAS(Hypermedia As The Engine Of Application State).
Hypermidea(링크)를 통해서 애플리케이션의 상태 전이가 가능해야 한다. 또한 Hypermedia(링크)에 자기 자신에 대한 정보가 담겨야 한다.  
상태 전이란 말이 어색할 수 있는데, 클라이언트가 특정 요청을 한 후, 그 요청에 대한 후속작업으로 이루어질 수 있는 작업들을 말한다.  
이를테면, 사용자가 게시글을 조회한다고 가정하고 그 이후 다음 게시물 조회, 댓글 달기 등 여러 작업이 이루어질 수 있다. 이러한 작업을 상태 전이라고 한다.  
자기 자신에 대한 메시지란, API 문서를 의미한다. 응답하는 API가 자신에 대한 문서를 응답에 포함해야 한다는 뜻이다.
```json
{
  "data": { // HAL JSON의 리소스 필드
    "id": 1000,
    "name": "게시글 1",
    "content": "HAL JSON을 이용한 예시 JSON"
  },
  "_links": { // HAL JSON의 링크 필드
    "self": {
      "href": "http://localhost:8080/api/article/1000" // 현재 api 주소
    },
    "profile": {
      "href": "http://localhost:8080/docs#query-article" // 해당 api의 문서
    },
    "next": {
      "href": "http://localhost:8080/api/article/1001" // article 의 다음 api 주소
    },
    "prev": {
      "href": "http://localhost:8080/api/article/999" // article의 이전 api 주소
    }
  }
}
```
HAL 이란 Hypertext Application Language 의 약자로 JSON, XML 코드 내에 외부 리소스에 대한 링크를 추가하기 위한 특별한 데이터 타입이다.  
HAL은 두 개의 타입을 갖는다. 1. application/hal+json   2. application/hal+xml  
HAL 타입을 이용하면 쉽게 HATEOAS를 달성할 수 있다.  
더 간단히 설명하면 리소스, 링크 두 영역으로 이루어진 데이터 타입이라고 이해하면 쉽다.


```
여기에 추가로 Code-On-Demand(주문형 코드)라는 원칙이 있다.
주문형 코드는 REST 원칙 중에서 유일한 선택사항이다.
```

## REST의 장점

1. HTTP 프로토콜의 인프라를 그대로 사용하므로 REST API 사용을 위한 별도의 인프라를 구출할 필요가 없다.
2. HTTP 프로토콜의 표준을 최대한 활용하여 여러 추가적인 장점을 함께 가져갈 수 있게 해준다.
3. HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.
4. Hypermedia API의 기본을 충실히 지키면서 범용성을 보장한다.(HATEOAS)
5. REST API 메시지가 의도하는 바를 명확하게 나타내므로 의도하는 바를 쉽게 파악할 수 있다.
6. 여러 가지 서비스 디자인에서 생길 수 있는 문제를 최소화한다.
7. 서버와 클라이언트의 역할을 명확하게 분리한다.

## REST의 단점

1. 표준이 자체가 존재하지 않아 정의가 필요하다.
2. HTTP Method 형태가 제한적이다.
3. 브라우저를 통해 테스트 할 일이 많은 서비스라면 쉽게 고칠 수 있는 URL보다 Header 정보의 값을 처리해야 하므로 전문성이 요구된다.
4. 구형 브라우저에서 호환이 되지 않아 지원해주지 못하는 동작이 많다.(특히 익스플로어.. 익스플로어.. 익스플로어..)

## RESTful 이란?
RESTful 이란 'REST한~' 라고 생각하면 될 것 같다. REST 아키텍처의 원칙을 잘 지켜 구현한 API는 RESTful API라고 할 수 있다.  


## 웹 API 디자인 할 때 좋은 참고사이트
마이크로소프트 웹 API 디자인 모범 사례 문서  
https://learn.microsoft.com/ko-kr/azure/architecture/best-practices/api-design#organize-the-api-design-around-resources  
<br>
Query Parameter와 Path Variable의 사용처  
https://ryan-han.com/post/translated/pathvariable_queryparam/