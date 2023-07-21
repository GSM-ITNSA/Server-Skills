# CredSSP란?
* Credential Security Support Protocol의 약자이다.
* CredSSP를 사용하면 애플리케이션이 원격 인증을 위해 Client에서 대상 Server로 사용자의 자격증명을 위임할 수 있다.

## 자격증명 위임?

    자격증명 위임이란, 사용자의 로그인 정보를 원격으로 암호화해서 대상 서버로 보낸 후 그 서버에서 인증을 거칠 수 있게 해주는 것.

## CredSSP가 왜 개발되었을까 ?
* 원격 연결을 할 때, 연결하는 Client의 정보를 안전하게 보호하기 위해 개발되었다.
* 자격증명을 위임할 때 전달 되는 User의 로그인 정보는 TLS/SSL에서 2번 더 암호화가 되므로 안전하다고 볼 수 있다.
* 하지만 CredSSP도 신뢰할 수 있는 Server와 Client 사이에서만 구성하는게 좋다.

## WSMAN이란?
* Windows Management Instrumentation을 기반으로 하는 원격 관리 프로토콜이다.
* WSMAN은 원결 클라이언트와 연결하기 위해 Web service 기술을 사용한다.
* 이 프로토콜은 사용하면 원격 시스템에 명령을 보내거나, 상태를 모니터링 할 수 있다. 
--- 

## 어떻게 동작하나요 ?

### 예시

1. Username : Hyeonjun123 / PW: Skill39!@# 인 컴퓨터가 있다. 이 컴퓨터는 PC1이라고 하겠다 !
2. PC1은 PC2로부터 자격 증명 위임을 하였다.
3. 그럼 앞으로 Username이 Hyeonjun123인 계정이 PC2로 원격 접속을 할 떄, PC1이 Hyeonjun123의 로그인 정보를 암호화를 해서 PC2로 전송한다.

## 자격증명 명령어 (PC1)
    Enable-WSManCredSSP -Role Client - DelegateComputer "PC2"
### * WSMANCredSSP를 사용할 수 있게 활성화해준다. 
### * 위와 같이 설정하게 되면 PC1은 CredSSP 설정을 할 떄 Client로 동작함.
### * 또한 PC2한테 자격증명을 위임하는것이다.

## 자격증명 명령어 (PC2)
    Enable-WSMANCredSSP -Role Server
### * 이렇게 설정해주면 CredSSP 설정 상에서 Server로 동작한다.

---

## CredSSP의 대한 여러가지 명령어 !!

    Get-WSMANCredSSP 
###  - 이 명령어는 Server, Client 모두의 CredSSP 구성을 출력한다.
    Get-WSMANInstance
### - 이 명령어는 Resource URI로 지정된 Resource Instance에 대한 관리 정보를 출력한다.
    New-WSMANInstance
### - 이 명령어는 관리 Resource의 새로운 Instance를 생성한다.
    Remove-WSMANInstance
### - 이 명령어는 관리 Resource Instance를 삭제한다.

---

## 유의할 점 !
* 또한, 원격 접속을 받는 즉, Server 역할을 하는 디바이스는 아래의 명령어를 입력해야한다.

### Command
    Enable-PSRemoting
### 이 명령어는 원격 명령을 받도록 컴퓨터를 구성한다.
* 이 명령어를 입력해야지 WSMAN을 통해 전송되는 Powershell 원격 명령을 받아들인다.
