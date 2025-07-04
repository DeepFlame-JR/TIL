CKA_0_Practice

# CKA Practice

### Drectory 정리
- `/etc/kubernetes`
  - 마스터 노드에서 사용되는 Kubernetes 구성 파일들이 위치하는 디렉토리
  - API 서버, 컨트롤러 매니저, 스케줄러 등의 구성 파일과 인증서, kubeconfig 파일 등이 저장
- `/var/lib/kubelet`
  - 워커 노드에서 kubelet이 실행되며 관리하는 리소스들이 위치하는 디렉토리
  - StaticPod의 정의 파일들이 있는 `/etc/kubernetes/manifests` 디렉토리와 컨테이너의 데이터를 저장하는 containers 디렉토리 등이 포함
- `/var/lib/etcd`
  - 마스터 노드에서 etcd 데이터베이스가 저장되는 디렉토리
- `/var/log/kubernetes`
  - Kubernetes 서비스의 로그 파일들이 저장되는 디렉토리
- `/etc/cni/net.d`
  - CNI(Continer Network Interface) 플러그인의 구성 파일들이 위치하는 디렉토리
  - 네트워킹 관련 설정들이 이곳에 저장
- `/opt/cni/bin`
  - 플러그인 가능한 리스트 확인 가능


### Trouble Shooting
- Application Failure
  1. `curl http://ip:port`
  1. `k describe svc web-service`
      - Service의 selector와 pod의 label이 일치하는지 확인
- Control Plane
  1. status 확인 `service <service-name> status`
      - kube-apiserver, kube-controller-manager, kube-scheduler, kubelet, kube-proxy
  1. API 서버 로그
      - sudo journalctl -u kube-apiserver
      -
- Woker Node (kubelet 고장)
```bash
sudo journalctl -u kubelet 

vi /var/lib/kubelet/config.yaml
vi /etc/kubernetes/kubelet.conf
```
- Network
```bash
# k describe po 내용
## Failed to create pod sandbox: rpc error: code = Unknown desc = failed to setup network for sandbox "30890b3fd51a9fb788472228357a56f81c43b3c9fc20d4f9b2081d57fb65fff5": plugin type="weave-net" name="weave" failed (add): unable to allocate IP address: Post "http://127.0.0.1:6784/ip/30890b3fd51a9fb788472228357a56f81c43b3c9fc20d4f9b2081d57fb65fff5": dial tcp 127.0.0.1:6784: connect: connection refused

# weave 설치
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```


## 2. Core Concept

```bash
# 약어 확인
kubectl api-resources

kubectl apply -f <pod-definition.yaml>
kubectl replace --force -f <pod-definition.yaml>  # edit 후에 적용이 안 된 /tmp/ 파일 대상

# k expose: deploy, pod 등 생성된 리소스를 외부에 노출하는 명령어 (svc를 생성하고, 외부 Pod와 매핑)
kubectl expose deployment my-deployment --type=LoadBalancer --port=80 --target-port=8080

# k run: Pod를 생성하는 명령어 
kubectl run my-pod --image=my-container-image

# k set: 리소스의 속성을 변경하거나 업데이트하는 명령어
kubectl set image deployment/my-deployment my-container=my-new-container-image
kubectl set replicas deployment/my-deployment --replicas=3
```

## 3. Scheduling
```yaml
# nodeName 수동 설정
spec:
  nodeName: my-node
# Scheduler 설정
spec:
  schedulerName: my-scheduler

# selector 확인
kubectl get pods --selector env=prod,bu=finance,tier=frontend

# Node에서 taint 추가/제거
kubectl taint nodes node1 key1=value1:NoSchedule
kubectl taint nodes node1 key1=value1:NoSchedule-

# static pod 생성
# restart=never: StaticPod가 종료된 후 다시 시작되지 않음
# dry-run=client: 실제로 리소스를 생성하지 않고, 생성할 리소스의 내용을 확인
# -o yaml: 생성된 리소스를 yaml 형식으로 출력
# command -- sleep 1000: sleep 1000 명령어 실행
kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml

# 다른 노드에 있는 static pod 경로
ssh node01
vi /var/lib/kubelet/config.yaml # static pod 경로 확인
```

## 6. Cluster Maintenance
- 노드 활성화/비활성화
```bash
(https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)
k drain node-1  # 노드를 의도적으로 비움 (replicaset이 아닌 pod가 있으면 에러)
k cordon node-1  # 노드를 예약 불가능 상태로 만듬
k uncordon node-1  # 노드 재부팅 (새로운 Pod부터 예약됨)
```

- 업그레이드 시에 control-plan 노드부터 업그레이드되는데, 기존에 떠 있는 pod들은 계속 접근 가능
```bash
# K8s 버전 업그레이드
(https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/#upgrading-control-plane-nodes)

# kubeadm/kubectl 업그레이드
sudo apt update
sudo apt-cache madison kubeadm

# 업그레이드 버전 확인
kubeadm upgrade plan 

# kubeadm 업그레이드
sudo apt-mark unhold kubeadm && \
sudo apt-get update && sudo apt-get install -y kubeadm='1.32.0-1.1' && \
sudo apt-mark hold kubeadm

#kubectl, kubelet 업그레이드 
sudo apt-mark unhold kubelet kubectl && \
sudo apt-get update && sudo apt-get install -y kubelet='1.32.0-1.1' kubectl='1.32.0-1.1' && \
sudo apt-mark hold kubelet kubectl

sudo systemctl daemon-reload
sudo systemctl restart kubelet

# 워커노드에서도 똑같이
ssh node01
```

- Backup & Restore
```bash
#-- etcd 환경 변수는 command에 있음
k describe po -n kube-system {etcd pod}

# 이 URL을 통해 클라이언트는 etcd 서버에 접속하여 데이터를 조회하거나 변경 (2379가 etcd 포트임)
--listen-client-urls=https://127.0.0.1:2379,https://192.14.117.9:2379
# 메트릭을 수집하는 URL. 메트릭 정보는 모니터링과 성능 관련 용도로 사용.
--listen-metrics-urls=http://127.0.0.1:2381
# 각 멤버는 피어 간에 이 URL을 통해 통신하여 클러스터 상태를 동기화하고 분산 데이터베이스로 동작
--listen-peer-urls=https://192.14.117.9:2380

#-- backup
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
  snapshot save <backup-file-location>

#-- restore
etcdutl --data-dir /var/lib/etcd-restored snapshot restore snapshot.db

vi /etc/kubernetes/manifests/etcd.yaml # etcd 스펙 수정

k config view # cluster 현황 확인
k config use-context cluster1 # cluster 이동
```

## 7. Security
- CA 교체
```bash
https://kubernetes.io/docs/tasks/administer-cluster/certificates/

# ${MASTER_IP} = /CN=kube-admin/O=system:masters
openssl genrsa -out ca.key 2048
openssl req -x509 -new -nodes -key ca.key -subj "/CN=kube-admin/O=system:masters" -out ca.crt
```

- CA 스펙 확인
```bash
$ openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 3392553705033807428 (0x2f14c59ec5f9b644)
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = kubernetes  # 인증서 발급자 정보
        Validity
            Not Before: Jun 26 13:22:39 2025 GMT
            Not After : Jun 26 13:27:39 2026 GMT
        Subject: CN = kube-apiserver # 인증서 소유자 정보
```

- k8s 통신 디버깅 디버깅
```bash
# apiserver 로그를 먼저 확인하고, 그 후 문제를 조치한다
docker ps -a
docker logs <container-id>

cd /etc/kubernetes/pki/etcd
vi /etc/kubernetes/manifests/etcd.yaml

# crictl (CRI 호환 컨테이너 커맨드라인 인터페이스)
crictl ps -a
crictl logs <container-id>

# etcd가 --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt 이기에
# kube-apiserver도 --etcd-cafile=/etc/kubernetes/pki/etcd/ca.crt 이어야 한다.
```

- CertificateSigningRequest (csr)
```bash
# https://kubernetes.io/docs/tasks/tls/certificate-issue-client-csr/

cat akshay.csr | base64 -w 0

k certificate approve akshay
```

- Role Checking
```bash
# https://kubernetes.io/docs/reference/access-authn-authz/authorization/#checking-api-access
kubectl auth can-i list secrets --namespace dev --as dave
```


## 9. Network
- K8s 네트워크 탐방
```bash
### Node Network
controlplane $ ip address  # node와 연관된 network/interface 정보 (IP, Mac Address 등)
lo: loopback 인터페이스로, 자기 자신으로의 루프백(Loopback) 트래픽을 처리 (127.0.0.1/8)
flannel.1: flannel 네트워크 인터페이스로, Kubernetes 클러스터에서 노드 간 통신을 위해 사용
cni0: CNI (Container Network Interface) 인터페이스로, 컨테이너들 간의 통신을 처리
veth85879640@if2 및 vethee201166@if2: CNI 네트워크 인터페이스에 속하는 가상 인터페이스들로, 각각 cni0 인터페이스에 속함
eth0@if8256 및 eth1@if8260: 호스트 노드에 있는 실제 물리 인터페이스


### node와 관련된 route 정보 
controlplane $ ip route  
# 기본 라우트. 어떤 목적지로 가는 패킷이 192.168.0.1을 통해 전송되어야 함. dev etho0는 네트워크 인터페이스
# 노드 내부에서 사용되는 flannel 네트워크에 대한 라우트. 10.244.0.0/32 범위는 flannel.1을 통해 전송
# 다른 네트워크의 eth1로 가는 패킷에 대한 라우트. 172.25.0.0/24 범위는 eth1를 통해 전송
# 다른 네트워크의 eth0로 가는 패킷에 대한 라우트. 192.168.0.0/24 범위는 eth0을 통해 전송
default via 192.168.0.1 dev eth0 proto static 
10.244.0.0/32 dev flannel.1 proto kernel scope link src 10.244.0.0 
172.25.0.0/24 dev eth1 proto kernel scope link src 172.25.0.14 
192.168.0.0/24 dev eth0 proto kernel scope link src 192.168.0.100 


### network 연결과 라우팅 테이블 정보 확인
netstat 
-a: 모든 연결과 소켓 정보 표시
-n: 주소를 숫자형식으로 표시 
-p: 프로세스 정보 표시
-l: Listening 중인 소켓 표시
-t: TCP 연결정보만 표시
-u: UDP 연결정보만 표시
netstat -anp  # 모든 연결 및 소켓 정보를 숫자 형식으로 표시
# (LISTEN) Local Address가 Foreign Address로부터 요청을 대기하고 있다
# (ESTABLISHED) SSH 클라이언트와 서버 간의 연결이 수립되어 있다

### 현재 실행 중인 프로세스
ps
-a: 모든 사용자의 프로세스
-u <user>: 특정 user의 프로세스
-x: 헤더를 표시하지 않음
-e: 모든 프로세스를 표시
-f: 전체 형식으로 프로세스 표시
ps -aux # 모든 사용자의 모든 프로세스를 상세정보와 함께
```

- CNI
```bash
### CNI 정보 확인 
https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/

/opt/cni/bin # 지원 CNI 종류
/etc/cni/net.d # CNI Configmap

### CNI 설치해보기
https://learn.kodekloud.com/user/courses/udemy-labs-certified-kubernetes-administrator-with-practice-tests/module/739eaf7b-4898-4732-9aa1-290db7d422d1/lesson/650b5199-b09b-450f-ade6-46116028201b

https://docs.tigera.io/calico/latest/getting-started/kubernetes/self-managed-onprem/onpremises
```


## 11. Install K8s with kubeadm
1. container runtime 설정
https://v1-32.docs.kubernetes.io/docs/setup/production-environment/container-runtimes/

```bash
# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.ipv4.ip_forward = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system
```

2. kubelet kubeadm kubectl 설치
https://v1-32.docs.kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

3. ControllerNode initializer
https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/
```bash
kubeadm init --apiserver-advertise-address=<ip add 에서 etho0의 값> 
kubeadm init --apiserver-advertise-address=192.168.183.205 --apiserver-cert-extra-sans=controlplane --pod-network-cidr=172.17.0.0/16 --service-cidr=172.20.0.0/16
```

4. kubeconfig 설정
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

5. Worker Node에 설정
```bash
ssh node01
kubeadm token create --print-join-command # 여기서 나온 join 명령어를 입력하면 됨
```

6. CNI 설치
```bash
# podCidr 확인
sudo cat /etc/kubernetes/manifests/kube-controller-manager.yaml | grep -i cluster-cidr

# 설치
kubectl create ns kube-flannel
kubectl label --overwrite ns kube-flannel pod-security.kubernetes.io/enforce=privileged

helm repo add flannel https://flannel-io.github.io/flannel/
helm install flannel --set podCidr="172.17.0.0/16" --namespace kube-flannel flannel/flannel

# deamonset args에 "- --iface=eth0" 추가
```

## 12. Helm & Kusotmize
- helm
```bash
helm search hub # 인터넷에 있는 전 세계의 공개 Helm 차트 전체를 검색
helm search repo # 내 컴퓨터에 등록한 저장소(helm repo add로 추가한 곳)만 검색

helm repo add bitnami https://charts.bitnami.com/bitnami
helm install amaze-surf bitnami/apache --version 18.3.6 # chart version

helm rollback dazzling-web 3 # REVISION
```

- kustomize
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
```

```yaml
# patch 방법 1
patches:
  - target:
      kind: Deployment
      name: mongo-deployment
    patch: |-
      - op: remove
        path: /spec/template/metadata/labels/org

# patch 방법 2
patches:
  - label-patch.yaml

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
spec:
  template:
    spec:
      containers:
        - $patch: delete
          name: memcached
```


## Mock Exam

### Pod 관련
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mc-pod
  namespace: mc-namespace
spec:
  containers:
  - name: mc-pod-1
    image: nginx:1-alpine
    env:
    - name: NODE_NAME
      valueFrom:
        fieldRef:
          fieldPath: spec.nodeName
  - name: mc-pod-2
    image: busybox:1
    command: ["/bin/sh"]
    args: ["-c", "while true; do date > /var/log/shared/date.log; sleep 1;done"]
    volumeMounts:
    - mountPath: /var/log/shared
      name: cache-volume
  - name: mc-pod-3
    image: busybox:1
    command: ["/bin/sh"]
    args: ["-c", "while true; do cat /var/log/shared/date.log; sleep 1;done"]
    volumeMounts:
    - mountPath: /var/log/shared
      name: cache-volume
  volumes:
  - name: cache-volume
    emptyDir:
      sizeLimit: 500Mi
```

```yaml
      affinity:                                             # add
        podAntiAffinity:                                    # add
          requiredDuringSchedulingIgnoredDuringExecution:   # add
          - labelSelector:                                  # add
              matchExpressions:                             # add
              - key: id                                     # add
                operator: In                                # add
                values:                                     # add
                - very-important                            # add
            topologyKey: kubernetes.io/hostname             # add
```

```bash
# resources가 없으면 node 리소스가 없을 때 가장 먼저 삭제됨
k get pods -n project-c13 -o jsonpath="{range .items[*]}{.metadata.name} {.status.qosClass}{'\n'}"
```

```bash
curl -k https://kubernetes.default/api/v1/secrets -H "Authorization: Bearer ${TOKEN}"
```

### 선언형 명령어
https://kubernetes.io/docs/reference/kubectl/quick-reference/
```bash
k expose pod messaging --port=6379 --name=messaging-service
kubectl expose deployment/hr-web-app --type="NodePort" --port 8080 --name=hr-web-app-service
kubectl expose pod nginx-resolver --name=nginx-resolver-service --port=80 --target-port=80 --type=ClusterIP
```

```bash
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service

kubectl run nginx-resolver --image=nginx
kubectl expose pod nginx-resolver --name=nginx-resolver-service --port=80 --target-port=80 --type=ClusterIP

kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup nginx-resolver-service > /root/CKA/nginx.svc

kubectl get pod nginx-resolver -o wide
kubectl run test-nslookup --image=busybox:1.28 --rm -it --restart=Never -- nslookup <P-O-D-I-P.default.pod> > /root/CKA/nginx.pod
```

### Autoscale
```bash
kubectl autoscale deployment <name> --cpu-percent=50 --min=1 --max=5 --dry-run=client -o yaml
```

```yaml
  metrics:
  - type: Pods
    pods:
      metric:
        name: requests_per_second
      target:
        type: AverageValue
        averageValue: "1000"
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

### Gateway
```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: cka5673
spec:
  gatewayClassName: kodekloud
  listeners:
    - name: https
      protocol: HTTPS
      port: 443
      ###
      hostname: kodekloud.com
      tls:
        certificateRefs:
          - name: kodekloud-tls
```

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np-backend
  namespace: project-snake
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Egress                    # policy is only about Egress
  egress:
    - to:                           # first condition "to"
      - podSelector:
          matchLabels:
            app: db1
      ports:                        # second condition "port"
      - protocol: TCP
        port: 1111
    - to:                           # first condition "to"
      - podSelector:
          matchLabels:
            app: db2
      ports:                        # second condition "port"
      - protocol: TCP
        port: 2222
```

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: traffic-director
  namespace: project-r500
spec:
  parentRefs:
    - name: main
  hostnames:
    - "r500.gateway"
  rules:
    - matches:
        - path:
            type: PathPrefix
            value: /auto
          headers:
          - type: Exact
            name: user-agent
            value: mobile
      backendRefs:
        - name: web-mobile
          port: 80
    - matches:
        - path:
            type: PathPrefix
            value: /auto
      backendRefs:
        - name: web-desktop
          port: 80
```

### crictl
```bash
crictl inspect
crictl ps
```
