# ⭐AD Repelication 개념⭐

## AD 복제란?

- 네트워크를 통해 디렉터리 데이터를 배포하는 복제 서비스이다.
- 디렉터리에 변경사항(새로운 서버의 추가, 삭)이 생기면 바로 다른 DC에게 이 데이터가 복제된다.
- 이를 통해 장애로 인해 디렉터리 데이터에 손상이 일어나도 다른 서버를 통해 읽을 수 있고 복제된 디렉터리가 네트워크에 걸쳐있기 때문에 사용자의 액세스가 분산되어  로드를 줄일 수 있다.
- 또한 복제가 일어나면 해당 변경사항의 데이터가  `GCS(Global Catalog Server)` 에 복제가 된다.

---

## GCS(Global Catalog Server)란❓

- GCS는 DC가 생성될 때 자동으로 생성된다.
- 즉, 하나의 모든 포리스의 모든 AD 개체의 복사 데이터 저장하는 DC이다.
- GCS는 Domain Forest의 첫번째 도메인 컨트롤러 자동으로 생성된다. 하지만 GC의 역할을 다른 DC로 이동 시킬 수 있다.

## GCS의 역할은❓

1. 개체 검색

```markdown
- GC는 데이터의 위치와 상관없이 하나의 Forest의 모든 도메인에서 Directory 정보를
  사용자가 검색할 수 있게 도와준다.
- 다른 도메인의 있는 Directory 정보를 GC에 Access를 하여 해결할 수 있다는 것이다.
```

1. 사용자 인증

```markdown
- GC는 인증 주체인 DC가 계정 정보를 가지고 있지 않은 경우에 사용자 계정 이름을 해결한다.
- For Example, 사용자의 계정이 abc.com에 속해있을때 dfe.com에 속해있는 컴퓨터에서 
  user01@abc.com을 LogOn 하려고 하면 dfe.com의 DC는 계정 정보를 찾을 수 없어 인증 X.
  But, dfe.com의 DC가 GC에 접근하여 user01@abc.com으로 액세스를 하면 프로세스 O.
```

---

## 연결 개체란❓

- 연결 개체란, 원본 DC에서 대상 도메인 컨트롤러의 복제 연결을 나타내는 AD 개체이다.
- DC는 단일 사이트의 구성원이며,  ADDS의 서버 개체로 사이트에 포함되거나 나타난다.
    - 각 서버 개체(DC)에는 사이트의 복제 DC를 나타내는 NTDS 설정 개체가 존재한다.
- 즉, 연결 개체는 대상 서버에서 NTDS 설정 개체의 자식 개체이다.
- 두 DC 간에 복제를 수행하려면 하나의 서버 개체(DC)에 다른 DC의 Inbound 복제를 나타내는 연결개체가 있어야한다.
- 또한, DC에 대한 모든 복제 연결은 NTDS 설정 개체 아래의 연결 개체로 저장된다.

---

## KCC란❓

### Knowledge Consistency Checker

- KCC란, 모든 DC에서 실행되는 AD Forest에 대한 복제 토폴로지 생성을 위한 기본 제공 프로세스이다.
- KCC는  사이트 내 - 사이트 내 (사이트 내부의 개체) / 사이트 - 사이트 에 복제가 발생하는지에 따라 동적으로 알맞게 복제 토폴로지를 생성한다.

```markdown
복제 토폴로지란?

* AD내의 DC들 간에 어떤 형태로 데이터를 복제할지 결정하는 논리적인 네트워크 구조이다.
* 즉, DC간의 복제를 할 때의 경로, 형태를 정의한다. 
```

- 또한 KCC는 새로운 DC추가, 기존 DC제거, Site 및 DC의 이동, 일시적으로 사용할 수 없는 DC거나 오류가 난 DC들에 맞게 토폴로지를 동적으로 조정한다.
- KCC는 특정 시간을 통해 동작한다.
    - 일반적으로 15분이며, 변경 사항이 있을 시 즉시 실행 등 상태를 변경할 수 있다.
    - 하지만 너무 많이 하면 부하가 심하고 너무 늦으면 복제가 잘 안되니까 15분이 적당한 것임!

---

## 장애 조치(Failover)기능 🤭

- Site는 복제가 네트워크 오류 및 오프라인 DC를 중심으로 라우팅 되도록 한다.
- KCC는 일반적으로 15분을 간격으로 실행되어 ADDS에서 발생하는 변경사항에 대해 복제 토폴로지를 조정한다.

```markdown
!! 

dssite.msc 에서 [지금 복제]를 하여 즉시 복제를 할 수 있고 

repadmin /syncall /Aed 명령어를 통해 복제를 한번에 즉시 할 수 있다.
```

- 또한 KCC는 기존 연결의 복제 상태를 검토하여  연결이 정상적으로 작동하는지도 확인한다.
- 실패한 DC때문에 연결이 작동하지 않는 경우에 KCC는 복제가 정상적으로 수행되도록 다른 복제 파트너 (다른 DC, 하지만 다른 DC가 있는 경우나 그 DC가 정상적으로 동작할 떄)에 대한 임시 연결을 빌드한다.
- 하지만 파트너 DC또한 없고 사용할 수 있는 DC가 없을 때 KCC는 다른 사이트의 DC를 빌려서 복제 연결을 한다.

---

## 복제 Subnet❓

- 서브넷은  논리 IP 주소 집합이 할당되는 TCP/IP 네트워크의 세그먼트이다.
- 서브넷은 네트워크 상에서 물리적 근접성을 식별하는 방식으로 컴퓨터를 그룹화한다.
- ADDS의 서브넷 개체는 Computer를 Site에 매핑하는데 사용된다.

```markdown
For Example,

Site - HJ / Subnet1 = 192.168.0.0/24
SIte - MC / Subnet2 = 192.168.1.0/24

PC1 = 192.168.0.1
PC2 = 192.168.1.1

-> PC1 = Site HJ
-> PC2 = Site MC
```

---

## Site❓

- Site는 신뢰할 수 있고 빠른 네트워크 연결이 있는 하나 이상의 TCP/IP 서브넷을 나타내는 AD개체이다.
- Site 정보를 사용하면 Administrator가 AD Access 및 Replication을 구성하여 실제 네트워크의 사용을 최적화 할 수 있다.
- Site 개체는 Subnet 개체와 집합되며, Forest의 각 DC는 IP주소에 따라 AD 사이트와 연결된다.
- Site는 둘 이상의 DC를 호스팅 할 수 있으며, 둘 이상 DC를 Site에 표시할 수 있다.

```markdown
For Example,

Example Company Name = 'ABC Company'

ABC Company = { abc.com, xyz.com}

abc.com에 대한 DC
xyz.com에 대한 DC

이렇게 둘 이상의 DC가 하나의 Site에 Join 될 수 있으며 Site는 복수개의 DC를 Hosting 가능.
```

---

## Site Link❓

- SIte Link는 KCC가 AD 복제에 대한 연결을 설정하는 데 사용하는 논리적 경로를 나타내는 AD 개체이다.
- Site Link 개체는 지정된 Site들 간 전송이 균일하게 통신할 수 있는 사이트 들의 집합을 말한다.
- Site Link 내의 포함된 모든 Site는 동일한 네트워크 유형을 통해 연결된 것으로 간주된다.
- 한 Site의 DC가 다른 Site의 DC의 변경된 Directory 정보를 복제 해오려면 Site Link를 수동으로 연결해야 한다.
- 두 Site가 Site Link로 연결이 되면 복제 시스템은 `Bridge Head Server` 라고 하는 각 Site의 특정 DC간에 연결을 자동으로 생성한다.

```markdown

Bridge Head Server란 ?

- AD에서 데이터 복제를 관리하는 DC중에서 특정 Site간의 복제 트래픽을 집중시켜 역할을 수행
  하는 DC를 뜻한다.
- Bridge Head Server는 주로 느린 WAN 연결의 Site에서 사용된다. 
- 왜냐하면 여러 DC들이 계속 복제를 하면 부하가 심하고 속도가 느리기 때문에 특정 DC에 
  복제 트래픽을 모아서 하나로 보내는것이다. 
- Bridge Head Server는 특정 서버로 선택할 수 있다.

[수동으로 Bridge Head Server를 선택하는 방]
1. Active Directory Sites and Services 관리 도구를 연다
2. 적절한 Site의 하위 "NTDS Settings" 컨테이너로 이동한다.
3. Bridgehead 서버로 사용할 도메인 컨트롤러의 속성을 연다.
4. "Bridge all site links" 옵션을 체크해제하고, "Bridge all site links except the following" 옵션을 선택한다.
```