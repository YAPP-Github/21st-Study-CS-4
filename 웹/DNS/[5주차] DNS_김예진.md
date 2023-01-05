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

# Name Server (=DNS Server)

![image](https://user-images.githubusercontent.com/68051794/210776890-9f601234-28e4-4ecb-b39a-694de630d968.png)

도메인 네임 스페이스의 트리 구조에 대한 정보를 가지고 있는 서버를 네임 서버라고 한다. 데이터베이스의 역할을 하며 요청 처리에 대한 응답을 구현한다.
하위 조직의 네임 스페이스를 할당하고 관리하는 방식은 각 하위 기관의 관리 책임자에게 위임된다. 예를 들어 www.google.com 도메인은 google.com을 관리하는 네임 서버에 등록되어 있고, google.com 도메인은 .com 도메인을 관리하는 네임 서버에 등록되어 있다. 이러한 위임 구조는 호스트의 증가에 대한 관리를 효율적으로 이루어지게 한다.

1. 루트 네임 서버
   ICANN이 직접 관리하는 서버로, 전 세계에 13개의 루트 네임 서버가 구축되어 있다.
   TLD 네임 서버의 IP 주소가 등록되어 있다.

2. TLD 네임 서버
   도메인 등록 기관이 관리하는 서버이다.
   Authoritative 네임 서버의 IP 주소가 등록되어 있다.

3. Authoritative 네임 서버 (=SLD 네임 서버)
   실제 도메인과 IP 주소의 관계를 기록(저장, 변경)하고, 관리하는 권한을 가진 서버이다.
   일반적으로 도메인/호스팅 업체의 네임서버를 말한다.

4. Non-Authoritative 네임 서버 (=resolver 서버, recursive 서버, recursor)
   도메인 이름과 IP 주소가 매핑되어 있지 않고 질의를 통해 IP 주소를 알아내거나 캐시한다.
   ISP가 제공하는 DNS 서버를 말하며 일반 가정의 경우 이 서버를 사용한다.

# Resolver

![image](https://user-images.githubusercontent.com/68051794/210777053-3a29f78d-df9b-4dcd-aa0a-b2d15544c605.png)

웹 브라우저와 같은 DNS 클라이언트의 요청을 네임 서버로 전달하고, 네임 서버로부터 정보(도메인 이름과 IP 주소)를 받아 클라이언트에게 제공하는 기능을 수행한다. 이 과정에서 리졸버는 하나의 네임 서버에게 DNS 요청을 전달하고, 해당 서버에 정보가 없으면 다른 네임 서버에게 요청을 보내서 정보를 받는다. 리졸버는 이렇게 재귀적인 질의(query)통해 정보를 알아낸다.
기록되지 않은 IP 주소는 캐싱하여 다음 쿼리 때 빠르게 접근한다.

# 재귀적 질의 vs 반복적 질의

- 재귀적 질의(Recursive Query)
  - 브라우저 - 로컬 DNS 서버 사이의 통신을 재귀적 질의라고 한다.
- 반복적 질의(Iterative Query)

  - 로컬 DNS 서버 - Root NS, TLD NS(com NS), Sub Domain NS 사이의 통신을 반복적 질의라고 한다.

- 작동 과정
  ![image](https://user-images.githubusercontent.com/68051794/210777307-684ab4fe-772a-42de-9373-08276167d5c8.png)

1. 브라우저 캐시 확인
2. hosts 파일과 캐시 확인
3. 로컬 DNS 서버에 www.mo-rak.com IP 주소 요청
4. 로컬 DNS 서버는 캐시 확인
5. Root NS(Name Server)에 IP 주소 요청
6. Root NS 서버는 IP를 가지고 있지 않고 com NS 주소를 가지고 있어서 TLD NS(com NS) 주소에 IP를 요청
7. TLD NS(com NS) 서버에 IP 주소 요청
8. TLD NS(com NS) 서버에도 IP가 없고 Sub Domain NS 주소를 가지고 있어서 Sub Domain NS에 IP 요청
9. Sub Domain NS 서버에 IP 주소 요청
10. Sub Domain NS 서버가 IP 주소를 로컬 DNS 서버에 전달
11. 로컬 DNS 서버가 브라우저에 IP 전달

# 정리

- 도메인 이름과 IP 주소에 대한 정보를 관리하는 시스템
- 인터넷 사용자는 IP 주소를 몰라도 된다.
- 도메인 이름을 IP로 바꿔준다.
- 계층 구조이다.
