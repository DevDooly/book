---
icon: monument
---

# Harbor

## Harbor

공식문서

* https://goharbor.io/docs/2.12.0/
* https://github.com/goharbor/harbor

데모 페이지

* https://demo.goharbor.io/

Harbor 는 '컨테이너 이미지' 의 레지스트리 오픈소스로 이미지를 효율적이고 안전하게 관리할 수 있는 프로젝트이다.

## 주요 기능

* 클라우드 네이티브 레지스트리 (Cloud Native Registry)
  * 컨테이너 이미지, Helm Chart 를 클라우드 네이티브 환경에서 지원한다.
* RBAC (Role-Based Access Control)
  * 프로젝트 단위로 사용자나 팀에 대한 권한 관리
* 정책 기반 복제 (Policy Based Replication)
  * 여러 레지스트리 인스턴스 간 이미지, 차트의 동기화 가능
  * 이중화 및 오류 복구 지원 (https://goharbor.io/docs/2.12.0/administration/configuring-replication/)
* 취약점 검사 (Vulnerability Scanning)
  * 이미지에 포함된 OS 패키지, 애플리케이션 라이브러리 등에서 이미 알려진 보안이슈가 있는 경우 CVE(Common Vunerabilities and Exposures) 를 참조하여 리스트 및 심각도를 알려줌.
  * 심각도 기준 Critical 등급 이상은 pull 차단 (Unknown -> Low -> Medium -> High -> Critical )
  * 폐쇄망인 경우 Trivy 오프라인 업데이트를 통해서 CVE DB 최신화 (외부 파일 반영)
* LDAP/AD Support
  * 기존 LDAP 그룹을 통해 특정 프로젝트에 대한 권한을 부여
* OIDC Support
  * OIDC : OpenID Connect
  * SSO 활성화 및 Harbor 포탈 로그인 가능 (UI)
* Image Deletion & Garbage Collection
  * 실제로 디스크에서 삭제하는것이 아닌 태그를 삭제하여 참조 불가 상태로 변경
  * GC 를 사용하여 주기적으로 이미지 삭제 및 공간 확보
  * ex. 이미지 push -> 이미지 삭제(태그 삭제) -> UI 목록에서 제거(blob 파일 존재, 용량 변경없음) -> GC 실행 및 실제 파일 삭제
* Notary 지원
  * Notary : Docker 공식 프로젝트로 컨테이너 이미지에 대한 디지털 서명 및 검증을 제공함
  * 이미지 탈취 및 변조에 대한 검증을 확인
* Graphical User Portal
  * Harbor 의 대시보드
* Auditing
  * 기본적인 로깅
* RESTFul API
  * API 제공과 Swagger Ui 제공
* Easy Deployment
  * Docker Compose, Helm Charts 로 설치 제공

## Architecture

<figure><img src="https://goharbor.io/docs/1.10/img/ha.png" alt=""><figcaption></figcaption></figure>

## Load Balancer

HA 구성을 위해 ingress controller 을 설치하고 load balance 를 수행한다.

## HA pgSQL

postgreSQL 는 메타데이터 저장소로 활용된다.

* 프로젝트 정보
* 사용자 정보 및 권한
* 이미지 태그, 아티팩트 정보 등
* 레지스트리 정책, Webhook 설정 등

자체적으로 pgSQL Pod 을 제공하지만 HA 구성을 위해서는 별도로 구성된 pgSQL 이 필요하다.

## HA Redis

redis 에 로그인 세션, 토큰 등 임시 정보를 저장한다.\
pgSQL 과 마찬가지로 HA 구성을 위해서는 별도 구성이 필요.

## Clair (or Trivy)

주요 특징인 Volnerability Scanning (취약점 검색) 을 수행하는 스캐너

## Notary

Docker 공식 프로젝트, 컨테이너 이미지에 대한 디지털 서명 및 검증을 제공한다.

## Registry

로드밸런싱, 장애대응을 위해 여러개의 Pod 으로 관리되지만\
MinIO, Longhorn 같은 외부 저장소를 활요하는 경우 harbor 내부 registry 를 사용하지 않는다.
