# PostgreSQL On Kubernetes

## 개요

* Kubernetes 에서 PostgreSQL 을 사용하는 방법
* Kubernetes 에서 RDB 를 사용할때의 장점과 문제점

## k8s 에서 PostgreSQL 을 사용하는 방법

### 핵심 요소

* Kubernetes StatefulSet VS Deployment
* Persistent Volume (PV) & Persistent Volume Claim (PVC)
* YAML 예시
* Helm Chart

### Deployment VS StatefulSet

PostgreSQL 을 사용하기 위해서는 k8s 에서 제공하는 핵심 기능들을 사용할 수 없다.

이를 위해 Deployment 와 StatefulSet 에 대한 이해가 필요하다.

#### Deployment

**특징**

* Stateless (상태 없음) 애플리케이션 배포에 적합하다.
* 모든 Pod 가 동일한 역할을 수행하며, 서로 교체가 가능하다.
* Pod ㅏ 삭제, 생성될 때 이전 Pod 와 무관하다.

**사용 사례**

* 웹 서버 (Nginx, Apache 등)
* API 서버
* 단독 애플리케이션 (java 데몬 등)

#### StatefulSet

**특징**

* Stateful (상태 있음) 애플리케이션 배포에 적합하다.
* 각 Pod 에 고유한 이름, 고유한 영구 스토리지를 가진다. (PV)
* Pod 가 재시작, 이동 등 변경이 생겼을때 동일한 이름, 저장소를 유지한다. (PV)

**사용 사례**

* 데이터베이스 (PostgreSQL, MySQL, MongoDB 등)
* Kafka, Zookeeper 같은 분산 시스템

위와 같은 특징으로 k8s 에서 PostgreSQL 을 사용하려면 StatefulSet 을 사용해야 한다.

### Persistent Volume (PV) & Persistent Volume Claim (PVC)

StatefulSet 에 이어서 영구 스토리지를 가지려면 PV, PVC 개념에 대한 이해가 필요하다.  
트랜잭션 중 장애 발생시 롤백을 해야 하는데, 데이터 무결성을 보장하기 위해 데이터가 안정적으로 저장되는 스토리지가 필요하다.  
이를 위해서 필요한 개념이 PV, PVC 다.

**Persistent Volume**

* 클러스터 광리자가 생성하는 스토리지 리소스
* Pod 와는 별개의 라이프사이클을 가짐

**Persistent Volume Claim**

* 사용자가 PV 를 사용하기 위한 정의
* PVC 는 PV 를 요청하고, 적절한 PV 가 있다면 자동으로 바인딩한다.
* StatefulSet 에서는 각 Pod 가 개별적인 PVC 를 가진다.

### YAML 예시

PostgreSQL 설치를 위해 필요한 필수 구성요소들에 대한 yaml 예시

참고 링크 : https://www.digitalocean.com/community/tutorials/how-to-deploy-postgres-to-kubernetes-cluster

#### 데이터베이스 세부 정보를 위한 ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: postgres-secret
  labels:
    app: postgres
  data:
    POSTGRES_DB: ps_db
    POSTGRES_USER: ps_user
    POSTGRES_PASSWORD: SecurePassword
```

#### PV, PVC 생성

PV

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: postgres-volume
  labels:
    type: local
    app: postgres
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: /data/postgresql
```

PVC

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgres-volume-cliam
  labels:
    app: postgres
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 10Gi
```

#### PostgreSQL 배포 yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
spec:
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
        - name: postgres
          image: 'postgres:14'
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 5432
          envFrom:
            - configMapRef:
              name: postgres-secret
          volumeMounts:
            - mountPath: /var/lib/postgresql/data
              name: postgresdata
      volumes:
        - name: postgresdata
          persistentVolumeClaim:
            claimName: postgres-volume-claim
```

이밖에도 서비스 등록, 복제 수 조정 (replication), 백업 설정 등 운영에 필요한 기능들을 추가할 수 있다.

### Helm Chart

운영상의 이점을 극대화 하기 위해 쿠버네티스 패키지 관리를 위한 helm 이라는게 있다.  
그리고 여기서 패키지는 쿠버네티스 리소스를 하나로 묶은 helm chart 라 부른다.  
쉽게 설명하면 yaml 의 모임

PostgreSQL 또한 bitnami에서 제공하는 helm chart 를 사용해 설치 및 관리가 가능하다.

https://github.com/bitnami/charts/tree/main/bitnami/postgresql

**설치 방법**

1. helm 을 사용하여 설치
  - helm install postgresql-demo bitnami/postgresql
2. 설정
  - 정상적인 사용을 위해 pv, pvc, replication 관련된 설정을 진행한다.
  - 자세한 설정은 bitnami 공식 사이트를 참고한다.

**추가 내용**

기본적으로 helm chart 특성상 node 나 node pool 을 지정하지 않는다.  
특정 node 에 배포하려면 nodeSelector, affinity, tolerations 을 설정에 추가해야 한다.

## Kubernetes 에서 RDB 를 사용할때의 장점과 문제점

위에서 설명한 내용을 보면 결국 k8s 에서 PostgreSQL 을 사용하는게 무조건적으로 좋다 라고 할수 없음을 알 수 있다.  
장점과 단점을 정리해보면 아래와 같다.

**장점**

* 확장 및 롤링 업데이트 가능
* 플랫폼과 독립적인 배포 가능
* 클라우드 네이티브 환경에서의 관리 용이성

**단점**
* 성능 이슈 (로컬 디스크 대비 IOPS 저하)
* 네트워크 오버헤드 증가
* 데이터 일관성 및 영속성 관련된 문제

물론 위 단점들을 해결하는 방법들 또한 존재한다.  
로컬 디스크(Local Persistent Volume)를 사용해 IOPS 저하 문제를 해결할 수 있다.  
하지만 Pod 가 다른 노드로 이동하면 데이터가 유실될 수 있어 항상 같은 노드에서 실행되도록 고정해야 한다.  
추가로 local pv 를 사용하게 되면 해당 노드에 장애가 발생하는 경우 데이터가 손실된다.  

이밖에도 k8s 의 이점들을 버리면서 발생하는 이슈들을 해결하기 위해 별도의 설정들이 필요하다.

## 결론

k8s 에서 PostgreSQL 또는 RDB 를 사용하는것이 최선의 선택은 아니지만  
배포가 잦거나 주기적으로 PM 작업을 하고 멀티 클라우드 환경에서 설치를 하는 등  
특정 상황에서는 사용을 고려해볼 수 있다.
