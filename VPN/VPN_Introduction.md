# VPN 개요

## VPN이란❓

- VPN(Virtual Private Network)는 Internet과 같이 여러 사람이 사용하는 공인망을 Private Network처럼 동작시키는 것을 뜻한다.
- VPN을 이용하면 본사-지사 간의 네트워크를 전용선(물리적인 회선)으로 구축하는 것에 비하여 훨씬 적은 비용으로 네트워크를 구축,관리 할 수 있다.
- 또한, 전용선을 이용한 Private Network 환경에서도 데이터의 보안을 위해 VPN은 꼭 필수되는 기술이다.

---

## VPN이 동작하기 위해서는❓❓

- VPN을 사용하여 공중말을 사설망처럼 안전하게 사용하기 위해서는 다음과 같은 기능이 지원되어야 한다.

### 1. 통신 상대방 확인

- 처음 통신을 할 때 나와 통신할 사람이 엉뚱한 사람이 아니라는것을 확인해야한다.
- 이렇게, 통신 상대방을 확인하는것을 인증(Authentication)이라고 한다.
    - 인증 방식 : `Pre-Shared Key` / `Digital Certificate` ..

### 2. 데이터의 기밀성 유지

- 송수신 하는 데이터의 내용을 수신자를 제외한 모든것이 알지 못하게 암호화시키는 것을

     `기밀성 유지 기능`이라고 한다.

- 암호화 방식 : DES,3DES,AES,RC4 …

### 3. 데이터의 무결성 확인

- 송수신하는 데이터를 도중의 공격자가 변조하는것을 방지하는 기능을 `무결성 확인 기능` 이라고 한다.
- 무결성 확인 방식 : MD5,SHA-1 …
- 위와 같은 알고리즘을 사용하여 만든 코드를 `Hash Code`라고 한다.
- 이 Hash Code를 패킷에 첨부하여 수신자에게 보내면 수신자는 이 Hash 값을 송신자가 보낼 때 사용한 Password와 같은 Password를 사용하여 Hash Code를 검사한다.
- 해시코드가 동일 = 변조 X , 해시코드 동일 X = 변조 O

### 4. 재생 방지

- 공격자가 자신의 패킷을 도중에 끼워넣는 것을 방지하는 기능을 마ㄹ한다.
- 패킷의 순서번호, 도착시간을 확인하여 사전에 지정된 범위의 것들 받아들여 재생 방지의 목적을 달성할 수 있게 된다.

---

## VPN의 종류

- 현재 사용되는 주요 VPN

| VPN 종류 | 암호화 부분 | 주요 용도 | 비고 |
| --- | --- | --- | --- |
| 순수 IPSEC VPN | Layer 3 이상 | 본지사간의 통신 | 기본적인 IPSEC |
| GRE IPSEC VPN | Layer 3 이상 | 본지사간의 통신 | GRE를 이용한 Routing 해결 |
| DMVPN | Layer 3 이상 | 본지사간의 통신 | 대규모 VPN |
| IPSEC VTI | Layer 3 이상 | 본지사간의 통신 | VPN 설정 간편 |
| Easy VPN | Layer 3 이상 | PC와 본사간의 통신 | 외부근무 사원의 내부망 접속 허용 |
| Flex VPN | Layer 3 이상 | 본지사간의 통신, PC와 본사간의 통신 | IKEv2 기반의 통합 VPN 서비스 |
| GET VPN | Layer 4 이상 | 본지사간의 통신 |  대규모 VPN, 멀티캐스트,QOS 지원 |
| SSL VPN | Layer 5 이상 | PC와 서버간의 통신 | 전자상거래 용도 |
| PPTP VPN | Layer 2 이상 | PC와 본사간의 통신 | 외부근무 사원의 내부망접속 허용 |
| L2TP VPN | Layer 2 이상 | PC와 본사간의 통신 | 외부근무 사원의 내부망접속 허용 |
| MPLS VPN | 암호화 기능 X | 본지사간의 통신 | IPsec VPN과 동시 사용 |

---

## 암호의 구분 및 용도

- VPN에서는 인증, 데이터 암호화 및 변조 방지를 위하여 암호(Key)가 필요하다.
- Key는 양측에서 사용된 Key의 동일성의 따라 두가지로 나뉜다.

### 대칭키 암호화 방식

- 암호화, 복호화 둘 다 같은 Key를 사용하여 암호화 하는 방식이다.
- 대표적으로는 양칙에 미리 설정된 PSK - pre shared Key알고리즘에 의해서 생성된 키를 들 수 있다.

### 비 대칭키 암호화 방식

- 암호화, 복호화가 서로 다른 Key를 사용하여 암호화 하는 방식이다.
- 대표적으로 개발자의 이름을 딴 RSA(Rivest,Shamer,Adelman)키가 있다.
- 비 대칭키는 공개키(Public Key)와 개인키(Private Key)의 쌍으로 이루어져 있다.
- 송신자가 수신자의 공개키로 암호화를 하면 수신자는 자신의 개인키로만 복호화를 할 수 있다.

---

## IPSEC VPN 개요

- IPSEC VPN은 본지사간의 보안 통신에 가장 많이 사용되는 것으로 주요한 구성요소는 다음과 같다.

### 1. IKE와 ISAKMP

- IKE(Internet Key Exchange)와 ISAKMP(internet security association and key management protocol)은 처음 통신 당사자 (통신할 상대 즉, Peer)를 인증하고, 보안 통신에 필요한 보안정책 집합인 SA를 결정하며, 보안통신에 필요한 여러가지 Key를 결정하기 위한 프로토콜이다.

### 2. SA

- SA(Security Association)은 보안 통신을 위해 필요한 여러가지 알고리즘 및 정책의 집합이다.
- 예를 들어…

```markdown
ISAKMP 프로토콜을 암호화하려면...

암호화 방식 : AES
무결성 확인 : MD5

이와 같은 여러가지의 보안정책의 집합을 ISAKMP SA라고 불린다.

데이터를 IPSEC으로 송수신하려면...

보안정책 : 3DES, SHA-1

이 보안정책의 집합을 IPSEC SA라고 한다.
```

### 3. ESP 또는 AH

- ESP(Encapsulating Security Payload)와 AH(Authentication Header)
- 실제 데이터를 인캡슐레이션하고 보호하기 위한 프로토콜이다.
- ESP는 데이터를 암호화시키고 무결성 확인 기능이 있다.
- 하지만 AH는 암호화 기능이 없기 때문에 거의 사용하지 않는다.