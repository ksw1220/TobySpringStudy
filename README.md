# HTTP1.0 HTTP1.1 HTTP2

HTTP 프로토콜은 웹 상에서 클라이언트와 서버가 통신하기 위한 대표적인 프로토콜이다.
Connectionless와 Stateless의 특징을 갖고 HTTP 프로토콜을 개선하기 위해 다양한 노력이 이루어져 왔다.
HTTP1.0 HTTP1.1 HTTP2의 차이을 비교해보며 개선된 사항을 알아보자.

## HTTP1.0과 HTTP 1.1
두 프로토콜의 차이는 다음과 같다.
1. 커넥션 유지
  
  기존 HTTP1.0 에서는 각 요청마다 새로운 TCP 커넥션을 생성하였고, 요청에 대한 응답이 완료되면 커넥션을 제거하였다.
  HTTP1.1 에서는 생성된 커넥션을 응답이 완료된 뒤 바로 종료하지 않고, 다른 요청과 응답에도 이용할 수 있다.
2. 호스트 헤더
  HTTP1.1에서는 하나의 IP가 여러개의 도메인을 운영하는 것이 가능해졌고 이를 버츄얼 호스팅이라고 한다.
3. 강력한 인증 절차
  - proxy-authentication
  - proxy-authorization
  
  위 두 개의 헤더가 추가되었고 클라이언트와 서버 사이에 프록시에 대해서도 인증을 요구할 수 있게 되었다.

https://withbundo.blogspot.com/2021/02/http-http-10-http-11.html

이렇게 HTTP1.1을 도입하며 위의 사항들이 개선되었지만 여전히 문제는 존재하였다.

## HTTP 1.1의 한계
1. HOL(Head of Line) Blocking(특정 응답 지연)
  - HTTP 1.1에서 클라이언트의 요청 순서와 서버의 응답 순서는 같아아 한다. 즉 먼저 전송된 요청이 처리되기 전에는 다음 요청에 대한 응답이 이루어 질 수 없다.
2. RTT(Round Trip Time) 증가
  - TCP 커넥션을 유지하여 커넥션 생성에 필요한 비용을 줄였지만, 여전히 하나의 커넥션을 통해 하나의 요청만을 처리할 수 있다. 즉 동시에 다수의 리소스를 처리하는 것이 불가능하다.
3. 무거운 Header
  - HTTP 1.1의 헤더에는 많은 메타정보가 존재하여 매 요청 마다 중복된 헤더 값이 전송되게 된다.
  - 또한 쿠키를 사용하여 헤더의 크기가 더욱 커지게 된다.

## HTTP/1.1을 개선하기 위한 방법
1. Image Spriting
  - 웹 페이지를 구성하는 다양한 아이콘 이미지 파일의 요청 횟수를 줄이기 위해 아이콘을 하나의 큰 이미지로 만든 다음 CSS에서 해당 이미지의 좌표 값을 지정하여 표시한다.

2. Domain Sharding
  - 샤딩(sharding)이란 하나의 거대한 데이터베이스나 네트워크 시스템을 여러 개의 작은 조각으로 나누어 분산 저장하여 관리하는 것을 말한다.
  - HTTP/1.1의 단점을 극복하기 위해 여러 개의 Connection을 생성해서 병렬로 요청을 보내기도 한다. 하지만 브라우저 별로 도메인당 Connection의 개수 제한이 존재하기 때문에 근본적인 해결책은 아니다.

3. Minified CSS/Javascript
  - HTTP를 통해 전송되는 데이터의 용량을 줄이기 위해서 CSS, Javascript를 축소하여 적용한다. name.min.js, name.min.css 등이 그 예이다.

## HTTP/2
HTTP/2는 HTTP/1.1을 프로토콜의 성능에 맞춰 수정하였다.

1. Multiplexed Streams
  - HTTP/2 에서는 Multiplexed Streams을 이용하여 하나의 커넥션으로 여러개의 메시지를 주고받는다.
2. Stream Priorization
  -  이미지 파일과 CSS파일을 클라이언트가 요청해 본다고 가정해보자. 이 때 CSS 파일이 이미지 파일보다 늦게 응답된다면 브라우저 렌더링에 문제가 발생하므로 리소스 간의 우선순위를 설정하여 리소스 로드 문제를 해결한다.
3. Server Push
  - 서버는 클라이언트가 요청하지 않은 리소스를 사전에 푸쉬를 통해 전송할 수 있다. 
  - 클라이언트가 추후에 HTML 문서를 요청할 때 해당 문서 내의 리소스를 사전에 클라이언트에서 다운로드할 수 있도록 하여 클라이언트의 요청을 최소화할 수 있다.
5. Header Compression
  - Header Table과 Huffman Encoding으로 헤더 정보를 압축한다.
  - HTTP/2에서는 헤더에 중복이 있는 경우 Static/Dynamic Header Table 개념을 이용하여 중복을 검출해내고 해당 테이블에서의 index값 + 중복되지 않은 Header 정보를 Huffman Encoding 방식으로 인코딩한 데이터를 전송한다.
https://seokbeomkim.github.io/posts/http1-http2/

# MSA

MSA란 Micro Service Architecture의 약자로, 기존 Monolithic Architecture와 상반된 개념이다. MSA를 알아보기에 앞서 Monolithic Architecture를 알아보자.

## Monolithic Architecture
하나의 서비스 또는 어플리케이션이 하나의 거대한 아키텍처를 가질 때, Monolithic 하다고 한다. 
이 정의를 고려해보자면 애플리케이션의 FE, BE의 서비스가 하나의 서버에서 동작하는 방식도 Monolithic 이지만, 각 서비스의 규모가 거대해진 경우도 Monolithic에 해당한다.

https://www.mantech.co.kr/micro-service/

많은 서비스를 하나의 서버에서 실행하는 것은 구조가 심플하고, 애플리케이션의 설계 및 테스트가 용이하다. 무엇보다 트랜잭션을 관리하는 것이 매우 쉽다는 장점이 있다.

하지만 다음과 같은 문제점이 존재한다.
1. 하나의 서비스 장애가 전체적인 장애를 야기한다.
2. 배포 및 재배포에 많은 비용이 발생한다.
3. 의존관계의 모든 서비스를 개발자가 모두 이해해야 한다.
4. 기술, 언어, 프레임워크의 선택이 제한적이다.

이러한 MA의 단점을 보완하기 위해 MSA가 도입된다.

## MSA
MSA는 각 서비스가 작고 독립적으로 배포 가능한 단위로 구성된 구조이다.
하나의 애플리케이션의 서비스가 각각 독립적으로 개발 및 배포되며 각 서비스들은 API를 통해 통신한다.

https://kr.tmaxsoft.com/info/storyTView.do?seq=345

MSA는 다음과 같은 장점이 있다.
1. 각 서비스가 모듈화되어 API 서버를 통해 통신하기 때문에 개별 서비스의 개발과 유지보수가 용이하다.
2. 각 서비스에 적합한 기술과 프레임워크를 적용할 수 있다.
3. 각 서비스는 독립적으로 배포 가능하다.
4. 각 서비스의 부하에 따라 스케일링이 가능하다.

MSA에는 다음과 같은 단점이 있다.

1. 독립된 서비스의 통신과 
2. 트랜잭션 관리가 어렵다.(DB가 분산되어 있기 때문)
2. 통합 테스트가 어렵다.

## Spring Cloud 
MSA 환경에서는 각 서비스가 독립적으로 관리되며 배포 및 재생성이 빈번하게 발생한다. 만약 컨테이너 환경이 적욛된다면 이러한 경향을 더 클 것이다.
또한 스케일링이 이루어지고 로드밸런싱 또한 적용할 수 있다.

이렇게 독립적으로 변경되는 서비스의 정보와 위치를 관리하고 MSA 환경에서 개발을 쉽게 할 수 있도록 Spring Cloud 프레임워크를 사용한다.

Spring Cloud는 위에서 언급했듯이 MSA 환경에서 애플리케이션을 쉽게 개발하고 각 서비스를 배포, 운영하기 위해 사용한다.

Spring Cloud에는 다음과 같은 핵심 컴포넌트가 존재한다.

### Config Server
spring을 통해 개발한 애플리케이션에 다른 설정 정보를 런타임 시에 제공할 수 있다. 이 때 사용되는 설정 정보를 config server에게 요청한다.

config-server는 설정 정보들이 저장된 저장소에 접근하고 보통 Git Repository가 이용된다. 얻어낸 설정 정보를 애플리케이션에게 전달한다.

따라서 애플리케이션에 적용될 설정 정보가 변경될 경우 개발자는 git Repository만을 변경하여 변경 사항을 적용할 수 있다.

레포지토리의 위치를 등록한다.
config server를 통해 설정정보를 가져오기 위한 모든 서비스들은 config-server의 위치를 가지고 있어야 한다.

### Discovery Server
위에서 언급했듯이 MSA 상의 서비스들은 빈번하게 변경된다. 이 때 서비스의 IP가 변경된다고 가정해보자. 
MSA의 각 서비스들은 API server를 통해 서로 통신하는데 서비스의 IP가 동적으로 변경된다면 변경된 IP를 알아내서 그 IP로 요청을 전달해야 할 것이다.

Discovery Server는 MSA의 서비스들의 목록와 위치(IP, Port)를 관리하는 서버이다.

MSA의 각 서비스들은 생성시에 자신의 정보(IP,FQDN,PORT)를 Discovery-server에 등록한다.
Discovery-server는 주기적으로 서비스들의 상태를 체크하고, 정지된 경우 registry에서 삭제한다.

### Gateway-Server
MSA의 각 서비스들의 IP와 Port 번호에 대한 단일화된 엔드포인트를 제공하는 API-Server이다.

또한 인증, 모니터링, 오케스트레이션, 사용량  제어, 요청/응답 등의 기능을 포함한 향상된 Reverse Proxy 의 역할을 한다.

MSA 내부에서의 각 서비스 간의 요청은 API-Server에게 전달된다. 즉 모든 서비스에 대한 요청을 대신 받아 라우팅한다.

API-Server는 Discovery Server의 Client로서 요청의 대상이 될 서비스의 위치를 Discovery Server에게 요청한다. 응답받은 서비스에게 요청을 전달하고 응답을 반환한다.


### Circuit Breaker
MSA의 특정 서비스가 다른 서비스에게도 장애를 발생시키는 가능성을 방지한다.

### Ribbon Client
클라이언트 측의 로드밸런서로 라운드 로빈 방식을 이용한다.

https://honeywater97.tistory.com/205
