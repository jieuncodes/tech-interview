# 브라우저에 URL을 입력하면 일어나는 일

## Flow

1. 웹사이트, 서버, IP주소의 상관관계
2. 웹사이트를 호스팅하고있는 서버의 위치 찾기
3. 서버에 연결하기
4. 특정 페이지를 불러오기 위해 요청보내기
5. 서버가 보내는 응답 처리하기
6. 유저가 상호작용 할 수 있도록 페이지를 렌더하는 방법  

<br>

---
<br>

## 웹사이트, 서버, IP주소의 상관관계
웹사이트 : 파일, HTML, CSS, Javascript, 이미지의 콜렉션으로, 브라우저에게 이미지와 데이터들을 어떻게 보여줄지 알려준다.

사용자가 URL을 주소창에 입력하면 브라우저는 인터넷의 어떤 서버가 이 사이트를 호스팅하고 있는지 찾는다. 
인터넷에 접속할 수 있는 모든 장치들은 'IP 주소'라는 고유한 주소를 갖는다.

IP 주소: 네 부분의 숫자로 표현 (ex.203.0.113.0) 

그런데 이런 숫자들은 기억하기 어려우므로 도메인을 사용하여 기억하기 쉽도록 표현한다. 
**DNS를 사용하여** 도메인으로 IP주소를 찾을 수 있다.

유추:
- 도메인 -> 전화번호명
- IP -> 전화번호
- DNS -> 전화번호부

<br>

## The process
<br>

### Scheme
**https:// 는 scheme이다** (Hypertext Transfer Protocol Secure)
이 Scheme으로 브라우저는 서버에게 TLS(Transport Layer Security)를 사용하여 서버에 연결하라고 지시한다. 
HTTPS를 사용하면 비밀번호, 신용카드 정보 등이 암호화 되어 전달된다.

TLS: 인터넷의 통신을 보호하기 위한 암호화 프로토콜
ftp://, mailto:// file:// 과 같은 Scheme?은 TLS가 아닌 다른 프로토콜을 쓴다.



### Domain
도메인은 서버의 IP주소를 가리킨다.

### Path
resource를 조직화하는 방법. 서로 다른 resource들을 그룹화하기 위해 주로 사용된다.

### Resource
확장자를 붙이기도 한다. 주로 서버가 만든 컨텐츠를 가리킨다.

<br>

--- 
## 웹사이트를 호스팅하고있는 서버의 위치 찾기
DNS를 사용해서 브라우저는 도메인이 어떤 IP주소에 연결되었는지 알아낸다.
DNS는 매우 복잡하고 빠른 시스템으로, 브라우저와 여러 다른 인터넷의 주소를 각기 다른 레이어에서 데이터를 캐싱한다. 
브라우저는 
- 자신의 캐시 
- OS의 캐시  
- 라우터에서 로컬 네트워크의 캐시
- 회사 네트워크나 ISP의 DNS 서버의 캐시를 확인한다.

ISP: Internet Service Provider

만약 이 모든 레이어에서 브라우저가 IP주소를 찾지 못한다면, 회사 네트워크 또는 ISP가 재귀 DNS 검색을 시작한다

재귀 DNS 검색 : IP주소를 찾을 때까지 인터넷의 여러 DNS 서버들에서 연쇄적으로 검색이 일어나는것을 말한다.

---
<br>

## 서버에 연결하기(TCP Connection)
퍼블릭 인터넷 라우팅 인프라를 사용하면, 클라이언트 브라우저의 요청 패킷들이 
라우터, ISP 또는 다른 ISP나 네트워크로 전환하기 위한 'internet exchange'를 통해 전달된다. 이 세가지 과정은 모두 TCP를 따르는 방식으로 패킷을 전달하여 IP주소를 가진 서버를 찾은 뒤 연결한다. 그런데 이 방식은 매우 우회적인 방식이며 효율적이지 않다.

TCP: Transmission Control Protocol
패킷(packet) : 네트워킹에서 데이터가 전송되는 기본 단위. 목적IP + 출발IP + 전송계층데이터

대신 많은 사이트들은 CDN을 활용하여 컨텐츠를 브라우저에 물리적으로 가까운 위치로 캐싱시킨다. 

CDN: Content Delivery Network.
CDN은 전세계적으로 분산되어있는 캐시 서버이며 사용자가 물리적으로 웹 서버에 가까운 위치에서 데이터를 빠르게 받을 수 있도록 해주는 시스템이다.

이런 방식으로 브라우저가 인터넷에서 서버를 찾으면, TCP 연결을 만든다. 만약 HTTPS가 사용되는 상황이라면 'TLS handshake'가 일어나 연결을 보호한다. 

---
<br>

## 특정 페이지를 불러오기 위해 요청보내기
이제 브라우저는 서버에 연결되었다. 이제 HTTP(s) 프로토콜에 따라서 통신이 일어난다. 먼저 
페이지의 컨텐츠를 얻기 위해 브라우저가 HTTP 요청을 서버에게 보내는것으로 시작한다. 

### HTTP request 구성요소
- reqest line
- headers (or metadata about the request)
- body

### request의 구성요소
- request method (one of GET, POST, PUT, PATCH, DELETE or other HTTP verbs)
- path (요청된 resourse를 가리킨다)
- HTTP의 버전

 ```  ex )GET /hello-world HTTP/1.1 ```


### request headers
request를 라우트 하는데 도움을 주는 부가 정보들을 넣는다. 
예를들어 클라이언트가 어떤 종류의 request를 보내는지, A/B testing에 사용될 수 있는지, blue/green deployment나 canary deployment에 사용될 수 있는지 등등

```
Host: jennapederson.dev
User-Agent: curl/7.64.1
Accept: */* 
```


### body
포맷의 종류가 다양하며 서버는 request header ```Content-Type```에 따라서 body를 해석한다. 

---
<br>

## 서버가 보내는 응답 처리하기
서버가 request를 request line, headers, body를 보고 적절히 process한 뒤 응답을 보낸다. 

### response의 구성요소
- status line
- response headers
- request resource at that path (HTML, CSS, image file, data)

## status line
HTTP 버전과 해당 request의 상태가 적혀있다

```HTTP/1.1 200 OK
Date: Tue, 25 May 2021 19:40:59 GMT
Server: Apache
X-Frame-Options: SAMEORIGIN
X-Powered-By: Express
Cache-Control: max-age=0, no-cache
Content-Type: text/html; charset=utf-8
Vary: Accept-Encoding
X-Mod-Pagespeed: 1.13.35.2-0
Content-Encoding: br
Keep-Alive: timeout=1, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked

<!DOCTYPE html>
<html lang="en">
<head>
    THE REST OF THE HTML
```


---

<br>

## 유저가 상호작용 할 수 있도록 페이지를 렌더하는 방법
브라우저는 response header를 확인하고 어떻게 resourse를 render할 지 해석한다. 예를들면 ```Content-Type``` 헤더를 보고 HTML resource가 body에 들어있다는것을 알 수 있다. 브라우저가 HTML을 파싱하고 렌더링하는동안 Javascript, CSS, image, data에도 request를 보낸다. 


[reference]
* https://aws.amazon.com/ko/blogs/mobile/what-happens-when-you-type-a-url-into-your-browser/