# Module 1 Kubernetes 기본 개념

### Kubernetes(k8s) Background

* Cloud Native 기술의 예시
  * Container
  * Service Mesh&#x20;
  * 마이크로서비스
  * 불변(Immutable) 인프라
  * 선언형(Declarative) API
* [CNCF](https://www.cncf.io/) : Cloud Native 프로젝트에 대한 지원, 감독, 방향성을 지원하는 단체
  * Kubernetes는 CNCF에 의해서 Incubating 되고 Graduated

### Kubernetes의 특징

* 컨테이너의 오케스트레이션 도구
  * 스케줄링 및 배치
* 애플리케이션 기능에 집중하도록 인프라를 추상화
* 수많은 서비스를 효과적으로 관리 가능
* 인프라를 효과적으로 관리할 수 있도록 효과적이고 손쉬운 도구 제공
* 상태 확인, 자가 치유, 오토스케일링, 애플리케이션 개발 단순화 등

<div align="left"><figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FFXjum78dFsOTqFySzjow%2Fimage.png?alt=media&#x26;token=96018709-4a5f-43a0-98e3-93487e33fc98" alt="" width="563"><figcaption></figcaption></figure></div>

### Cluster

* Control Plane : 클러스터를 전체 관리하고, 이벤트를 감지하고 대응
* Data Plane : 실제 컨테이너 애플리케이션이 배포되는 컴퓨팅 리소스
* Node가 최소 3개가 있고, 구성 요소가 3중화 구성 되어 있는 이유
  * 롤링 업데이트시 이중화 구성이 되므로, 하나가 장애 발생을 하더라도 서비스가 유지
  * 쿼럼-최소정족수 모델을 사용
    * 첫번째로 만들어지는 Node가 Leader Node로 선출
    * Leader Node가 장애 발생 되면 나머지 노드 중 하나가 Leader Node로 선출

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2Fwhm2uLUObjpdEEGLOfFA%2Fimage.png?alt=media&#x26;token=3953f30e-cad2-4a5c-910b-13f86e3b5123" alt=""><figcaption></figcaption></figure>

### Control Plane

* API 서버 :  Kubernetes API 노출, 컨트롤 플레인의 Frontend
* etcd : 클러스터에서 일어나는 모든 상태, 메타 데이터가 저장되는 3중화 key-value 저장소
* Contoller Manager : 클러스터의 상태를 감시하고 동작을 제어하는 컨트롤러들의 집합
  * Node Contoller : 노드가 다운될 때 이를 감지하고 대응하는 역할
  * Job Contoller : 특정 작업을 완료하고 종료하는 Pod을 실행하는 Job 객체 관리
  * EndpointSlice Controller
    * 클러스터 내부의 Network endpoint를 추적하는 EndpointSlice 객체 관리
    * k8s에서 서비스와 파드 간의 연결을 관리
  * ServiceAccount Contoller : Pods, 시스템 구성 요소 및 엔티티가 API 서버에 작업을 호출하는데 사용할 수 있는 ServiceAccount를 관리
* Scheduler : API 서버에 Pod 생성 요청을 보내면, 여러가지를 고려한 노드에 해당 pod을 배포
* Cloud Controller Manager : CSP별 제어 로직을 포함, 클러스터와 CSP의 API 서버를 연결
  * CSP 종속성을 가지는 컨트롤러
    * Node Contoller : 응답이 중지된 후 클라우드에서 노드가 삭제되었는지 클라우드 공급자를 확인
    * Route Contoller : 기반 클라우드 인프라에서 경로를 설정하는 데 사용
    * Service Contoller : CSP 로드 밸런서를 생성, 업데이트, 삭제

<div align="left"><figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FCLMDT4Mr45pu09MQP7Xc%2Fimage.png?alt=media&#x26;token=f28cfefa-b2fb-4484-816d-a5f2f18d7100" alt="" width="474"><figcaption></figcaption></figure></div>

### Data Plane

* kubelet : API 서버로부터 오는 클러스터 제어 요청를 받아서 실행하는 에이전트
  * 신규 Pod과 내부 컨테이너 실행
  * Pod 상태 검사 진행, 이슈가 발생하면 API 서버에게 전달
* kube-proxy : 서비스를 포함한 여러 객체를 묶어서 하나의 가상 네트워크 구현하는 k8s 프록시
* Container Runtime : 컨테이너 실행 및 수명 주기 관리
  * 옛날에는 Docker였지만 요즘에는 Docker의 lightweight 버전인 containerd

### Node

* 컨테이너가 실행되는 컴퓨팅 리소스 (EC2, Fargate, 물리 서버, VM 등등)
* 각 Node에 kubelet, kube-proxy, Container Runtime이 구성
* Pod을 비롯한 다양한 k8s 객체들이 배포
* 여러 Node를 하나의 Node Group으로 그룹화 가능

### Pod

* Stateless 애플리케이션의 배포 단위

* 각 Pod은 고유의 IP를 할당 받음

* 하나 이상의 컨테이너으로 구성된 그룹

  * 컨테이너는 동일한 namespace, 네트워킹, 볼륨을 공유함
  * 사이드카 컨테이너가 Pod에 존재해서 추가 로직(네트워크 프록시, 보안) 구현

  <div align="left"><figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2F9RpMf8KrpeFCEDqbRzWE%2Fimage.png?alt=media&#x26;token=2f1409a5-f467-40b8-bf99-883a4bc60908" alt="" width="375"><figcaption></figcaption></figure></div>

* PodSpec은 컨테이너를 실행하는 방법에 대한 매니페스트 파일
  * yaml 템플릿으로 작성하며 선언형으로 Pod을 배포

* Pod의 Health Check
  * Liveness Probe : Shell 명령어를 실행해서 애플리케이션이 실행 중인지 확인
    * 실패하면 컨테이너 재시작
  * Readiness Probe : Client의 트래픽을 받아 처리할 준비가 되었는지 확인
    * 실패하면 Pod의 IP를 서비스 엔드포인트에서 제거

<div align="left"><figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FIlGrypkQwfeINeaGymfM%2Fimage.png?alt=media&#x26;token=1b5534f3-a536-469f-a52f-b6819b886a9a" alt="" width="563"><figcaption></figcaption></figure></div>

{% code title="podspec.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: v1
kind: Pod #객체의 종류
metadata: #부가 정보
  name: myapp-web 
  labels:
    app: myapp #객체에 추가하는 key/value, 속성 지정
spec:  #컨테이너 정보
  containers:
  - name: myapp-container
    image: busybox:1.14.2 #Public registry with tag를 이미지로 사용
    command: ['sh', '-c', 'echo "Hello, Kubernetes!" && sleep 3600']
    ports:
    - containerPort: 80 #client의 요청을 받을 TCP 80 port 사용
    - protocol: TCP
    livenessProbe: #command를 실행해서 nginx가 실행 중인지 확인
          exec:
            command:
            - /bin/sh
            - -c
            - "[ -f /run/nginx.pid ] && ps -A | grep nginx"
          initialDelaySeconds: 10
          periodSeconds: 5
    readinessProbe:
      httpGet: #HTTP GET 요청을 해서 정상적으로 처리하는지 확인
        scheme: HTTP
        path: /index.html
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 5
```

{% endcode %}

### Service

* 여러 Pod의 논리적 세트 여기에 액세스하는 수단
* Client가 해당 서비스에 접근할 수 있도록 서비스에 IP 및 Port 정보를 설정

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FGem7ugd4iOwqyNSiyqbZ%2Fimage.png?alt=media&#x26;token=52a27c71-d53f-4dc5-946a-80ae748a57c6" alt=""><figcaption></figcaption></figure>

{% code title="service-clusterIP.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
kind: Service
apiVersion: v1
metadata:
  name: myapp-web-service
spec:
  selector:
    app: myapp
  type: ClusterIP
  ports:
    - port: 8080 #ClusterIP 서비스가 노출하는 포트
      targetPort: 80 #실제 pod이 노출하는 포트
```

{% endcode %}

### Pod Scheduling

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FxcnpPr9oDaK3QAGqBHhw%2Fimage.png?alt=media&#x26;token=ef08566d-8bae-4f66-bb5c-ebdf330e8f3f" alt=""><figcaption></figcaption></figure>

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2F3Zs040C8arkRS9Pkbrna%2Fimage.png?alt=media&#x26;token=48bfbbdc-86a7-4a2d-a684-efcdd2aaf21c" alt=""><figcaption></figcaption></figure>

* Reference
  * <https://kubernetes.io/ko/docs/reference/scheduling/config/>
* Resource Requests
  * Pod이 필요한 CPU, Memory 용량을 설정하여, 가능한 노드들을 필터링

{% code title="podspec-resource-request-limit.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod-with-resources
spec:
  containers:
  - name: my-container
    image: nginx:latest
    resources:
      requests: #Pod 배포시 컨테이너가 필요한 CPU와 메모리 용량 요청
        cpu: "200m" 
        memory: "256Mi"
      limits: #Pod의 컨테이너가 실행 된 이후에 리소스에 대한 유효한 한도 정의 
        cpu: "500m" #초과하는 경우 CPU의 쓰로틀링 발생 
        memory: "512Mi" #초과하는 경우 컨테이너 종료 
```

{% endcode %}

* Taint 및 Toleration
  * Taint : Node에서 구성하는 설정으로,  Pod가 해당 Node에 스케줄링되는 걸 제외
    * Taint 강도는 `NoSchedule` 또는 `PreferNoSchedule`
      * Toleration : Pod에서 하는 설정으로, Taint가 설정된 Node와 일치된다면 해당 Node에 스케줄링될 수 있도록 설정

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FzSTAGBlc4HSC6MJAkpLL%2Fimage.png?alt=media&#x26;token=b8d0046a-8727-4d66-b606-267c7a7074fb" alt=""><figcaption></figcaption></figure>

* nodeAffinity&#x20;
  * Taint와 반대로, Pod이 선호하는 특정 Node에서 스케줄링
  * 선호도 강도는 `requiredDuringSchedulingIgnoredDuringExecution` 또는 `preferredDuringSchedulingIgnoredDuringExecution`

{% code title="pod-with-node-affinity.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity: #Node 선호도
      requiredDuringSchedulingIgnoredDuringExecution: #Hard 선호도
        nodeSelectorTerms:
        - matchExpressions:
          - key: topology.kubernetes.io/zone
            operator: In
            values:
            - us-east-1a #버지니아 리전 a,b 가용 영역으로 선호도 설정
            - us-east-1b
  containers:
  - name: with-node-affinity
    image: registry.k8s.io/pause:2.0
```

{% endcode %}

### Label

* Pod을 비롯한 k8s 리소스에 구성하는 레이블, key-value 구조, AWS의 Tag의 역할
* Selector를 통해서 어떤 리소스를 선택할 것인가 사용
* Pod을 배포시 동일한 label을 가진 Node에 스케줄링할 수 있도록 활용

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FpUFXj6IEKCYRhei18tfJ%2Fimage.png?alt=media&#x26;token=30e62f24-5b92-489b-865c-cc720878b657" alt=""><figcaption></figcaption></figure>

### Kubectl&#x20;

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2FoYN5Y0WMLaXcqdYwhhUP%2Fimage.png?alt=media&#x26;token=5c60daba-9e19-43e9-a570-4f1c753f5ecb" alt=""><figcaption></figcaption></figure>

### Namespace

* 단일 클러스터 내에서 리소스 그룹을 격리하는 논리적 메커니즘을 제공
* k8s 기반 멀티 테넌트 환경을 구성하기 위한 목적
* 하나의 namespace 내부의 리소스 이름은 고유해야 함
* 범위 지정은 namespace 속성을 갖는 객체 (deployment, service 등) 에만 적용
  * Cluster level의 객체에는 (StorageClass, Nodes, PersistentVolumes 등) 미적용

<figure><img src="https://2198985478-files.gitbook.io/~/files/v0/b/gitbook-x-prod.appspot.com/o/spaces%2Fpq7ALktNgAwBpRLdKFoD%2Fuploads%2Fnn0oBAxFi0ujNRKSrSjl%2Fimage.png?alt=media&#x26;token=09cd57f7-c37e-4917-857f-053ad302a197" alt=""><figcaption></figcaption></figure>

### ConfigMap&#x20;

* key-value로 기밀이 아닌 데이터로, 애플리케이션이 사용할 구성 데이터를 저장하는 객체
* 단일 value를 가진 key와 구성 파일 내 여러 key-value 세트를 저장
* Pod의 컨테이너는 PodSpec의 환경 변수를 정의하거나, ConfigMap을 읽기 전용 볼륨으로 마운트하여 파일로 읽기

{% code title="configmap.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  # 속성과 비슷한 키; 각 키는 간단한 값으로 매핑됨
  player_initial_lives: "3"
  ui_properties_file_name: "user-interface.properties"

  # 파일과 비슷한 키
  game.properties: |
    enemy.types=aliens,monsters
    player.maximum-lives=5    
  user-interface.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true    
```

{% endcode %}

{% code title="configmap/configure-pod.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-demo-pod
spec:
  containers:
    - name: demo
      image: alpine
      command: ["sleep", "3600"]
      env:
        # 환경 변수 정의
        - name: PLAYER_INITIAL_LIVES # 참고로 여기서는 컨피그맵의 키 이름과 대소문자가 다름
          valueFrom:
            configMapKeyRef:
              name: game-demo           # 이 값의 컨피그맵
              key: player_initial_lives # 가져올 키
        - name: UI_PROPERTIES_FILE_NAME
          valueFrom:
            configMapKeyRef:
              name: game-demo # 이 값의 컨피그맵
              key: ui_properties_file_name # 가져올 키
      volumeMounts:
        - name: config
          mountPath: "/config"
          readOnly: true
  volumes: # 파드 레벨에서 볼륨을 설정한 다음, 해당 파드 내의 컨테이너에 마운트
    - name: config
      configMap:
        name: game-demo # 마운트하려는 configMap 이름
        items: # configMap에서 파일로 생성할 키 배열
          - key: "game.properties" #/config/game.properties 파일 생성
            path: "game.properties"
          - key: "user-interface.properties" #/config/user-interface.properties 파일 생성
            path: "user-interface.properties"
```

{% endcode %}

### Secret&#x20;

* key-value로 애플리케이션이 사용할 민감한 정보(ID, Password, 키 등등)를 저장하는 객체
* base64로 인코딩된 문자열 value를 가진 key를 secret에 정의
* Pod의 컨테이너는 PodSpec의 환경 변수를 정의하거나, Secret을 읽기 전용 볼륨으로 마운트하여 파일로 읽기
* base64로 인코딩하기 때문에, 기밀 데이터를 더 안전하게 저장하려면 추가 솔루션이 필요

{% code title="username, password의 base64 인코딩" overflow="wrap" lineNumbers="true" %}

```shell
echo -n 'admin' | base64
echo -n '1f2d1e2e67df' | base64
```

{% endcode %}

{% code title="username-password-secret.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
```

{% endcode %}

{% code title="secret/configure-pod.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: redis
    volumeMounts:
    - name: foo
      mountPath: "/etc/foo" 
      readOnly: true
  volumes: # 파드 레벨에서 볼륨을 설정한 다음, 해당 파드 내의 컨테이너에 마운트
  - name: foo 
    secret: 
      secretName: mysecret # 마운트하려는 secret 이름
      optional: false # 기본값임; "mysecret" 은 반드시 존재해야 함
```

{% endcode %}

### ReplicaSet

* Pod 복제본에 대한 수량을 지정하고 이를 유지하여 Stateless 애플리케이션의 가용성을 보장
* ReplicaSet의 대상 Pod을 식별하기 위해서 Label Selector를 사용
* 하지만 단독으로 사용하는 것보다 Deployment의 Spec으로 설정하여 사용하는 걸 권장

### Deployment

* Stateless 애플리케이션에 대한 배포를 deployment로 선언
* Pod와 ReplicaSet에 대한 선언적 업데이트를 제공
  * 기본적으로 Rolling 업데이트로 제공

{% code title="nginx-deployment.yaml " overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec: #deployment의 Description
  replicas: 3 #3개의 복제본을 배포하는 ReplicaSet
  selector:
    matchLabels: #관리 대상 Pod을 식별하기 위한 Label Selector
      app: nginx
  template: #Pod 템플릿, Container Spec이 포함
    metadata:
      labels: #Pod 에 설정할 Label 
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

{% endcode %}

### Job 및 Cronjob

* Job은 1회성 작업을 실행할 수 있는 객체, 이를 의해서 하나의 Pod을 실행하고 끝나면 종료
* Cronjob은 스케줄에 따라서 반복하여 1회성 작업을 실행할 수 있는 객체
  * Unix/Linux의 Cronjob과 동일한 형태이며 백업을 수행하거나, 이메일을 보내는 등의 반복 작업에 적합

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *" #분 시 일 월 요일
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure #실패시 재시작
```

### DeamonSets

* Linux Server의 Daemon처럼 항상 떠 있는 Pod을 배포할 때 사용하는 객체
* 모든 Node에 하나의 Pod을 배포하여, Node 레벨의 동작해야 하는 애플리케이션을 구현
* 모니터링 에이전트, 로그 수집 에이전트, 백업 에이전트를 배포
* 신규 Node가 추가되면, 그 곳에 DeamonSet Pod 하나 배포

{% code title="fluentd-elasticsearch-daemonset.yaml" overflow="wrap" lineNumbers="true" %}

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations: 
      # these tolerations are to have the daemonset runnable on control plane nodes
      # remove them if your control plane nodes should not run pods
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
```

{% endcode %}

### StatefulSet

* 고유한 식별자 및 순서가 지정된 수명 주기로 Stateful 애플리케이션을 실행
* 아래의 요구사항이 하나 이상 필요한 애플리케이션에 적합
  * 안정되고 지속성을 갖는 스토리지가 필요
  * 안정적이고 고유적인 네트워크 식별자
  * 순차적인 구성요소의 배포 및 스케일링

{% code title="application/mysql/mysql-statefulset.yaml " overflow="wrap" lineNumbers="true" %}

```yaml
# Stateful Pod의 안정적인 DNS 엔트리를 위한 헤드리스 서비스.
apiVersion: v1
kind: Service
metadata:
  name: mysql
  labels:
    app: mysql
    app.kubernetes.io/name: mysql
spec:
  ports:
  - name: mysql
    port: 3306
  clusterIP: None
  selector:
    app: mysql
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
      app.kubernetes.io/name: mysql
  serviceName: mysql
  replicas: 1
  template:
    metadata:
      labels:
        app: mysql
        app.kubernetes.io/name: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        env:
        - name: MYSQL_ALLOW_EMPTY_PASSWORD
          value: "true"
```

{% endcode %}

<table><thead><tr><th width="95">특징</th><th width="245">Deployment</th><th width="245">StatefulSet</th></tr></thead><tbody><tr><td>Endpoint</td><td>Service을 통하여 실행 중인 Pod에 부하 분산</td><td>Headless Service의 DNS 레코드를 통한 접근</td></tr><tr><td>Pod</td><td>1회용 리소스, 스케일링, 롤링 업데이트 지원</td><td>고유하고 안정적인 이름과 네트워크 ID</td></tr><tr><td>Storage</td><td>기본적으로 Stateless</td><td>기본적으로 영구 저장 스토리지 사용</td></tr></tbody></table>

```shell
ec2-user:~/environment:$ kubectl get pods --namespace catalog
NAME                      READY   STATUS    RESTARTS        AGE
catalog-5fdcc8c65-bqlp8   1/1     Running   3 (3h17m ago)   3h18m
catalog-mysql-0           1/1     Running   0               3h18m
```

*<mark style="background-color:$warning;">End of Module 1</mark>*
