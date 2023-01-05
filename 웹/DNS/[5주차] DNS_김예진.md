# DNS

- IP 주소를 외울 수 없으므로 사용하는 것이 바로 DNS!  
  ex) http://142.250.207.36/ -> https://www.google.com/

# 도메인

- 웹사이트 주소의 구조
  ![image](https://user-images.githubusercontent.com/68051794/210775735-4b85300d-4b6d-4352-9f29-38d2a561462b.png)

- 도메인 관리
  ![image](https://user-images.githubusercontent.com/68051794/210775963-80d161da-0252-4798-bc72-f76492e047e5.png)

- ICANN
  국제 인터넷주소 관리 기구이다.
  현재 4억개에 달하는 도메인을 관리한다.

- REGISTRY
  TLD를 관리하는 기관이다.
  각 도메인 정보의 데이터베이스를 관리하고, registry에 따라 도메인 종류가 달라진다.
  한국인터넷진흥원, VeriSign 등이 여기에 속한다.

- REGISTRAR
  도메인 중개 등록업체이다
  REGISTRY의 데이터베이스에 직접 도메인 정보를 등록할 수 있다.
  가비아, 카페24 등이 여기에 속한다.

# DNS

도메인 이름을 사용하면 입력한 도메인을 실제 네트워크 상에서 사용하는 IP 주소로 바꾸고 해당 IP 주소로 접속하는 과정이 필요하다. 이러한 과정, 전체 시스템을 DNS(Domain Name System)라고 한다. DNS는 전세계적으로 약속된 규칙을 공유한다.
DNS(=Name) 서버는 도메인과 IP 주소를 저장하고 관리하는 컴퓨터나 애플리케이션을 말한다. 즉, 도메인 이름과 IP 주소가 매핑된 데이터베이스 시스템이다. 대표적인 DNS 서버는 KT, LG, SK와 같은 ISP(통신사)의 서버이다.

![image](https://user-images.githubusercontent.com/68051794/210776223-35ad34ad-60d2-4438-b240-12273fec72f0.png)

# 구조

![image](https://user-images.githubusercontent.com/68051794/210775388-7b51692c-5081-460e-9761-2618efeaabc1.png)

도메인은 체계적인 분류와 관리를 위해 .으로 연결한 계층(hierarchy) 구조를 갖고 있다. 최상위에 루트 DNS 서버가 존재하고, 그 하위로 노드가 연속해서 이어진 트리 구조로 되어있다.

1. Top Level Domain (TLD)
   `.com` 과 같은 일반 도메인(gTLD)과 `.kr`과 같은 국가 코드 도메인(ccTLD)

2. Second Level Domain (SLD)
   조직의 속성을 구분
   : `co` 영리기업, `go` 정부기관, `ac` 대학과 같은 도메인

조직이나 서비스의 이름
: `google`, `naver`와 같은 도메인으로, 도메인 사용자가 원하는 문자열을 사용

3. Sub Domain (=Host)
   `www`, `m`, `store`와 같이 도메인에 따라 사이트의 구성이 달라지는 것

- 작동 과정
  ![image](https://user-images.githubusercontent.com/68051794/210778773-45d5ad48-1a42-4c24-8045-96680d7878c5.png)

1. 사용자가 웹 브라우저를 열어 주소 표시줄에 www.example.com을 입력한다.

2. www.example.com에 대한 요청은 일반적으로 케이블 인터넷 공급업체, DSL 광대역 공급업체 또는 기업 네트워크 같은 인터넷 서비스 제공업체(ISP)가 관리하는 DNS 해석기로 전송된다.

3. ISP의 DNS 해석기는 www.example.com에 대한 요청을 DNS 루트 이름 서버에 전달한다.

**RootT DNS란?
Root DNS는 인터넷의 도메인 네임 시스템의 루트 존이다. 루트 존의 레코드의 요청에 직접 응답하고 적절한 최상위 도메인에 대해 권한이 있는 네임 서버 목록을 반환함으로써 다른 요청에 응답한다. 전세계에 961개의 루트 DNS가 운영되고 있다.**

4. ISP의 DNS 해석기는 www.example.com에 대한 요청을 이번에는 .com 도메인의 TLD 이름 서버 중 하나에 다시 전달한다.  
   .com 도메인의 이름 서버는 example.com 도메인과 연관된 4개의 Amazon Route 53 이름 서버의 이름을 사용하여 요청에 응답한다.

5. ISP의 DNS 해석기는 Amazon Route 53 이름 서버 하나를 선택해 www.example.com에 대한 요청을 해당 이름 서버에 전달한다.

6. Amazon Route 53 이름 서버는 example.com 호스팅 영역에서 www.example.com 레코드를 찾아 웹 서버의 IP 주소 192.0.2.44 등 연관된 값을 받고 이 IP 주소를 DNS 해석기로 반환한다.

7. ISP의 DNS 해석기가 마침내 사용자에게 필요한 IP 주소를 확보하게 됩니다. 해석기는 이 값을 웹 브라우저로 반환한다. 또한, DNS 해석기는 다음에 누군가가 example.com을 탐색할 때 좀 더 빠르게 응답할 수 있도록 사용자가 지정하는 일정 기간 example.com의 8. IP 주소를 캐싱(저장)한다.

8. 웹 브라우저는 DNS 해석기로부터 얻은 IP 주소로 www.example.com에 대한 요청을 전송한다. 여기가 콘텐츠가 있는 곳으로, 예를 들어 웹 사이트 엔드포인트로 구성된 Amazon S3 버킷 또는 Amazon EC2 인스턴스에서 실행되는 웹 서버이다.

9. 192.0.2.44에 있는 웹 서버 또는 그 밖의 리소스는 www.example.com의 웹 페이지를 웹 브라우저로 반환하고, 웹 브라우저는 이 페이지를 표시한다.

# Resolver

![image](https://user-images.githubusercontent.com/68051794/210777053-3a29f78d-df9b-4dcd-aa0a-b2d15544c605.png)

웹 브라우저와 같은 DNS 클라이언트의 요청을 네임 서버로 전달하고, 네임 서버로부터 정보(도메인 이름과 IP 주소)를 받아 클라이언트에게 제공하는 기능을 수행한다. 이 과정에서 리졸버는 하나의 네임 서버에게 DNS 요청을 전달하고, 해당 서버에 정보가 없으면 다른 네임 서버에게 요청을 보내서 정보를 받는다. 리졸버는 이렇게 재귀적인 질의(query)통해 정보를 알아낸다.
기록되지 않은 IP 주소는 캐싱하여 다음 쿼리 때 빠르게 접근한다.

# 재귀적 질의 vs 반복적 질의

- 재귀적 질의(Recursive Query)
  - 브라우저 - 로컬 DNS 서버 사이의 통신을 재귀적 질의라고 한다.
- 반복적 질의(Iterative Query)

  - 로컬 DNS 서버 - Root NS, TLD NS(com NS), Sub Domain NS 사이의 통신을 반복적 질의라고 한다.

# 정리

- 도메인 이름과 IP 주소에 대한 정보를 관리하는 시스템
- 인터넷 사용자는 IP 주소를 몰라도 된다.
- 도메인 이름을 IP로 바꿔준다.
- 계층 구조이다.
