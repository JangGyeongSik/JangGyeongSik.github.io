---
title: "Google Cloud_1" 
date: 2019-09-02 17:30 
categories: GCloud
---

#Google Cloud_1 

```class
cloud 종류
1) 클라우드 서비스 모델
 - IaaS 
 - PaaS
- SaaS
                                        애플리케이션
                                        -----------------------
                                        미들웨어
                                        -----------------------
                                        OS
                                        -----------------------
                                        하드웨어/네트워크

2) 클라우드 이용 모델
 -퍼블릭 클라우드
-프라이빗 클라우드
  + 온프라미스 프라이빗 클라우드
        클라우드 기반 소프트웨어 이용, 자사 전용 클라우드 환경을 구축해서 운영하는 형태. 자체적인 보안 정책에 따른 강력한 보안 환경을 구축해서 운용.
  +호스티드 프라이빗 클라우드
        클라우드 사업자가 기업 사용자별로 클라우드 환경을 제공함, 단기간에 전용 클라우드 환경을 구축하고 월비용을 지불하여 사용.
-커뮤니티 클라우드
-하이브리드 클라우드

Google Cloud Platform  Service
-Compute Engine : 확장가능한 고성능 VM 인스턴스
-Cloud Storage :글로벌 에지 캐싱을 제공하는 객체 Repository
-Cloud SQL : MySQL, PostrgreSQL
-Stackdriver : 통합모니터링 서비스
-Cloud IAM : ID 및 액세스 관리 서비스
-App Engine : 웹 애플리케이션 배포 및 웹 호스팅
-BigQuery :머신러닝이 내장되어 있으며 확장성이 우수한 관리형 데이터 웨어 하우스
-Cloud Dataproc :관리형 하둡(hadoop)과 스파트(Spark) 서비스
-Cloud PubSub : 메시지 큐잉 서비스
-Kubernetes  Engine : 쿠버네티스를 사용한 컨테이너 기반 서비스 구축
-Cloud Dataflow : 실시간 배치 처리 및 스트림 데이터 처리

-Cloud Dataprep : 분석을 위해 데이터를 탐색, 정리, 준비해주는 클라우드 데이터 서비스
-Cloud Composer : 아파치 에어플로(Apache Airflow)를 기반으로 하는 관리형 워크플로 서비스
-Cloud AutoML : 고품질 커스텀 머신러닝 모델을 간편하게 학습시켜주는 서비스
-Cloud ML Engine :머신러닝 서비스
-Persistent Disk : VM인스턴스용 블록 Repository
-Knative : 서버리스로 빌드, 배포, 관리할 수 있는 kubernetes서비스


Google Cloud Platform : 구글이 제공하는 퍼블릭 클라우드 서비스
Public Cloud Service : amazon web services, 마이크로소프트가 제공하는 Microsoft azure
 퍼블릭 클라우드가 가지는 특징을 충분히 활용한 시스템 아키텍처를 도입하는 것이 중요함.
GCP : 소프트웨어 개발과 실행 기반을 클라우드에서 제공하는 IaaS와 PaaS를 중심으로 한 서비스
```

```class
콘솔 https://console.cloud.google.com

프로젝트 만들고 프로젝트 내에서 리소스를 사용.
 프로젝트 이름 : 임의
 프로젝트 ID : GCP 전체에서 고유
 프로젝트 번호 : GCP가 직접 할당. GCP 전체에서 고유

프로젝트 이름 : 4~30자
프로젝트 ID :6자~30자  (UUID Universaly Unique Identifier)
        영 소문자/숫자/대시/프로젝트 번호
        cloud 명령이나 API 요청 시 옵션에 프로젝트ID를 식별자로 사용함.
        회사에서는...회사의 식별자/시스템이름의 식별자/개발용인지 실제운용이지 플래그 등 포함



생성한 프로젝트 이름/ID를 기록해놓기.
생성한 프로젝트 이름 : syl-class01
생성한 프로젝트 ID : syl-class01

VM인스턴스
- Preemptible VM Instance 선점형
  GCP 내에서 아무도 사용하고 있지 않은 리소스를 사용-저렴. 언제든지 종료될수 있음. 24H.
- 일반 VM  인스턴스

리소스 공유 영역
- Global 전역  Resource :프로젝트 내의 모든 영역에 있는 모든 리소스가 접근 가능.
        Address, Images, Snapshots, instance tmeplate, VPC network, firewall, Route경로  등
- Region 영역 resource : 동일한 Region 내에서 접근
         Address(지역고정외부IP), subnets 등...
- Zone 지역 Resource : 
 Instance, Persistent disk, machine type 등

생성한 VM인스턴스 :  webserver  35.184.80.27
```

```shell
 $ sudo apt-get -y update
 $ sudo apt-get -y install \
git python-pip python-dev python-flask \
python-wtforms python-arrow \
python-flask-sqlalchemy python-pymysql \
python-flaskext.wtf
                
                git 분산형 버전 관리 시스템 (github.com 연동)
                python-pip : 패키지 관리 시스템
                python-dev : 헤더 파일 과 정적 라이브러리
                python-flask : 웹 애플리케이션 프레임워크
                python-wtforms : 폼 요소의 랜더링 유효성 검증
                python-arrow : 날짜 처리 라이브러리
                python-flask-sqlalchemy : ORM (Object Relational Mapper)
                python-pymysql : Mysql 클라이언트
                python-flaskext.wtf : 폼 요소의 렌더링

                
 $ sudo pip install --upgrade setuptools
 $ sudo pip install --upgrade gcloud

 $ git clone https://github.com/freelec/gcp-compute-engine

 $ cd gcp-compute-engine
 $ cd app-v1
 $ ls
                install.sh   dongonban.sevice
 $ sudo install.sh  (설치하기  /opt/dengonban/v1/ 에 설치됨. message.db 파일에 메시지가 저장됨)

 $ sudo systemctl enable dengonban.service
 $ sudo systemctl start dengonban.service
 $ sudo systemctl status dongonban.service

에러 날 경우 $ tail -n 50 /var/log/daemon.log 로 에러 확인
```








