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

