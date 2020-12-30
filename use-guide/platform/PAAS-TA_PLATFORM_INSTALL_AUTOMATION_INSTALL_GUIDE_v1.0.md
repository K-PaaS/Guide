## Table of Contents

1. [개요](#1)
  * [목적](#2)
  * [범위](#3)
  * [참고자료](#4)
2. [플랫폼 설치 자동화 실행 환경 구성](#5)
	* [실행 환경을 위한 패키지 설치](#6)
	* [Ruby 및 BOSH 의존패키지 통합 설치](#7)
3. [플랫폼 설치 자동화 메뉴얼](#8)
	* [플랫폼 설치 자동화 실행](#9)

# Deprecated

<b>※ 본 문서는 PaaS-TA 5.0.1 이하의 버전까지 지원한다.</b>

## Executive Summary


본 문서는 플랫폼 설치 자동화를 설치하는 가이드 문서로 플랫폼 설치 자동화를 실행할 수 있는 환경을 구성하여 실행하고 사용하는 방법에 대해서 설명하였다.

본 문서는 다음과 같은 내용들을 포함한다.

#### 플랫폼 설치 자동화 실행 환경 구성
-	ruby
-	bosh_cli
-	spiff
-	java8
-	maven
-	mysql
-	make


# <div id='1'/>1.  문서 개요

## <div id='2'/>1.1.  목적
본 문서는 플랫폼 설치 자동화 시스템의 설치를 위한 환경 구성에 대해
기술하였다.

## <div id='3'/>1.2.  범위
본 문서에서는 Linux 환경(Ubuntu 16.04)을 기준으로 인프라 환경에 플랫폼
설치 자동화의 설치하는 방법에 대해 작성되었다.

Openstack을 통해 PaaS-TA 5.0을 배포 할 경우 지원이 검증 된 Openstack 버전의 범위는 아래와 같다.
<table>
<tr>
<td>Openstack Version</td>
<td>Service Name</td>
<td>API Version</td>
</tr>
<td rowspan="5">newton</td>
<td>Identify</td>
<td>v2</td>
</tr>
<tr>
<td>Compute</td>
<td>v2.1</td>
</tr>
<tr>
<td>Glance</td>
<td>v1</td>
</tr>
<tr>
<td>Network</td>
<td>v1</td>
</tr>
<tr>
<td>Volume</td>
<td>v1,v2</td>
</tr>
<tr>
<td rowspan="5">ocata</td>
<td>Identify</td>
<td>v3</td>
</tr>
<tr>
<td>Compute</td>
<td>v2.1</td>
</tr>
<tr>
<td>Glance</td>
<td>v1</td>
</tr>
<tr>
<td>Network</td>
<td>v1</td>
</tr>
<tr>
<td>Volume</td>
<td>v2,v3</td>
</tr>


<td rowspan="5">pike</td>
<td>Identify</td>
<td>v3</td>
</tr>
<tr>
<td>Compute</td>
<td>v2.1</td>
</tr>
<tr>
<td>Glance</td>
<td>v1</td>
</tr>
<tr>
<td>Network</td>
<td>v1</td>
</tr>
<tr>
<td>Volume</td>
<td>v2,v3</td>
</tr>
</table>

## <div id='4'/>1.3.  참고자료

본 문서는 Cloud Foundry의 Document를 참고로 작성하였다.

BOSH Document: [http://bosh.io](http://bosh.io)

CF & Diego Document:
[http://docs.cloudfoundry.org/](http://docs.cloudfoundry.org/)


# <div id='5'/>2.  플랫폼 설치 자동화 실행환경 구성

## <div id='6'/>2.1. 실행 환경을 위한 패키지 설치

플랫폼 설치 자동화는 BOSH CLI(command line interface) 실행환경을 웹으로
구현한 것으로 BOSH CLI와 유사한 구동 환경을 구성할 필요가 있다.
인프라 환경에 설치한 가상머신에 실행환경을 구성한다. 환경 구성에 있어서 전제조건으로 가상머신은 외부와 통신이 가능해야 한다.

플랫폼 설치 자동화의 실행환경을 구성하기 위해 다음의 패키지를 플랫폼 설치 자동화 설치 스크립트를 통해 자동으로 설치한다.
-	Ruby (1.9.3 이상)
-	bosh_cli (cli v2 이상)
-   make
-   ruby (2.3.1 이상)
-	Java (1.8 이상)
-	maven
-	mysql


## <div id='7'/>2.2.  플랫폼 설치 자동화 설치

-   플랫폼 설치 자동화 설치는 ubuntu(16.04 이상)에서 실행 된다.
-   본 가이드는 Inception 스펙 2Core, Memory 16G, Disk 80G에서 플랫폼 설치 자동화를 설치 및 운영하였다.

### 1.  플랫폼 설치 자동화 설치 구성

| 파일 명  |설명|
|---------|---|
| deployer-install.sh        |설치 스크립트 파일   |
| pds        |플랫폼 설치 자동화 서비스 등록 파일   |
| deployer        |플랫폼 설치 자동화 서비스 실행 파일   |
| deployerlog        |Logrotate 설정 파일   |
| bosh        |BOSH CLI 실행 파일   |


### 2.  플랫폼 설치 자동화 설치(IEDA-WEB-INSTALLER) 모듈과 플랫폼 설치 자동화(OPENPAAS-IEDA-WEB) 모듈을 다운로드 받는다.(PaaSTA-Env)

[다운로드](https://paas-ta.kr/download/package)

	  IEDA-WEB-INSTALLER-v5.0.tar
	  OPENPAAS-IEDA-WEB-v5.0.tar


### 3.  다운로드 받은 IEDA-WEB-INSTALLER-v5.0.tar 파일을 Home 디렉토리에 압축을 푼다.

  	$ tar xvf IEDA-WEB-INSTALLER-v5.0.tar -C ~/


### 4.  플랫폼 설치 자동화 설치 및 서비스 등록

	$ cd IEDA-WEB-INSTALLER
	$ ./deployer-install.sh <OPENPAAS_IEDA_WEB-v5.0.tar 파일이 있는 경로>/OPENPAAS_IEDA_WEB-v5.0.tar <mysql 비밀번호>

	ex)
	$ ./deployer-install.sh ~/Downloads/OPENPAAS_IEDA_WEB-v5.0.tar 1q2w3e4r5t



# <div id='8'/>3.  플랫폼 설치 자동화 매뉴얼

본 장에서는 플랫폼 설치 자동화를 실행하는 방법과 메뉴 구성 및 화면
설명에 대해서 기술하였다.

## <div id='9'/>3.1.  플랫폼 설치 자동화 실행

### 1.  플랫폼 설치 자동화를 실행한다.

	# 플랫폼 설치 자동화 실행
	$ pds start[stop/start/restart]


### 2.  플랫폼 설치 자동화가 실행중인 계정에서 아래와 같이 설정 디렉토리가 생성되었는지 확인한다.

| 설정 디렉토리  |설명|
|---------|---|
|  {HOME}/.bosh_plugin       | 플랫폼 설치 자동화가 사용하는 기준 디렉토리  |

### 3.  웹 브라우저를 이용해서 플랫폼 설치 자동화(http://[IP]:8080) 화면이 출력되면 플랫폼 설치 자동화의 설치가 완료되며 로그인 화면으로 이동된다.

![PaaSTa_Platform_Image00]

[PaaSTa_Platform_Image00]:./images/use-guide/login.png

### 4. 플랫폼 설치 자동화 메뉴얼
- 플랫폼 설치 자동화의 메뉴얼
  - [플랫폼 설치 자동화 사용 메뉴얼 가이드](PAAS-TA_PLATFORM_INSTALL_AUTOMATION_USE_MANUAL_v1.0.md)
