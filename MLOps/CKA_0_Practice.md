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



## 1. Core Concept

```powershell
# 약어 확인
kubectl api-resources

kubectl apply -f <pod-definition.yaml>
kubectl replace --force -f <pod-definition.yaml>  # edit 후에 적용이 안 된 /tmp/ 파일 대상

# k create: 리소스 생성 명령어
kubectl create pod my-pod --image=my-container-image
kubectl create service clusterip my-service --tcp=80:8080
kubectl create deployment my-deployment --image=my-container-image

# k expose: deploy, pod 등 생성된 리소스를 외부에 노출하는 명령어 (svc를 생성하고, 외부 Pod와 매핑)
kubectl expose deployment my-deployment --type=LoadBalancer --port=80 --target-port=8080

# k run: Pod를 생성하는 명령어 
kubectl run my-pod --image=my-container-image

# k set: 리소스의 속성을 변경하거나 업데이트하는 명령어
kubectl set image deployment/my-deployment my-container=my-new-container-image
kubectl set replicas deployment/my-deployment --replicas=3
```

## 2. Scheduling
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


## 4. Application Lifecycle Management

```yaml
# 롤링업데이트
kubectl set image deployment <deployment-name> <container-name>=<new-image>

# Command
# yaml 파일에 command가 설정되어 있다면 덮어씀
  containers:
  - name: my-container
    image: my-image
    # case 1
    command: ["sleep", "5000"]
    # case2
    command: ["echo", "Hello, Kubernetes!"]
    # case3
    command: ["echo"]
    args: ["Hello, Kubernetes!"]

# secret 생성
kubectl create secret generic my-secret --from-literal=username=myuser --from-literal=password=mypassword

# env 설정
  containers:
    - name: my-container
      image: my-image
      envFrom:
        - secretRef:
            name: my-secret
        - configMapRef:
            name: my-configmap

k drain node-1  # 노드를 의도적으로 비움 (replicaset이 아닌 pod가 있으면 에러)
k cordon node-1  # 노드를 예약 불가능 상태로 만듬
k uncordon node-1  # 노드 재부팅 (새로운 Pod부터 예약됨)
```

``` yaml
# K8s 버전 업그레이드
apt-get update
apt-get install kubeadm=1.27.0-00
apt-get install kubelet=1.27.0-00 
systemctl daemon-reload
systemctl restart kubelet

kubeadm upgrade plan  # 업그레이드 버전 확인

k cordon controlplane 
kubeadm upgrade apply v1.27.0  # 마스터노드 업그레이드

ssh node01
kubeadm upgrade node

# etcd 환경변수
# 이 URL을 통해 클라이언트는 etcd 서버에 접속하여 데이터를 조회하거나 변경
--listen-client-urls=https://127.0.0.1:2379,https://192.14.117.9:2379
# 메트릭을 수집하는 URL. 메트릭 정보는 모니터링과 성능 관련 용도로 사용.
--listen-metrics-urls=http://127.0.0.1:2381
# 각 멤버는 피어 간에 이 URL을 통해 통신하여 클러스터 상태를 동기화하고 분산 데이터베이스로 동작
--listen-peer-urls=https://192.14.117.9:2380

# backup/restore
ETCDCTL_API=3 etcdctl --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
  snapshot save <백업파일경로>

ETCDCTL_API=3 etcdctl snapshot restore <백업파일경로> --data-dir <데이터디렉토리경로>

vi /etc/kubernetes/manifests/etcd.yaml
volumes:
- hostPath:
    path: /var/lib/etcd-from-backup
    type: DirectoryOrCreate
  name: etcd-data


k config view # cluster 현황 확인
k config use-context cluster1 # cluster 이동
```

## 5. Storage
```yaml
# PVC가 Pending 상태
# PVC를 활용한 POD가 아직 생성되지 않았기 때문에
Events:
  Type    Reason                Age                From                         Message
  ----    ------                ----               ----                         -------
  Normal  WaitForFirstConsumer  11s (x3 over 28s)  persistentvolume-controller  waiting for first consumer to be created before binding
```

## 6. Network
```yaml
# Node Network
ip address  # node와 연관된 network/interface 정보 (IP, Mac Address 등)
lo: loopback 인터페이스로, 자기 자신으로의 루프백(Loopback) 트래픽을 처리 (127.0.0.1/8)
flannel.1: flannel 네트워크 인터페이스로, Kubernetes 클러스터에서 노드 간 통신을 위해 사용
cni0: CNI (Container Network Interface) 인터페이스로, 컨테이너들 간의 통신을 처리
veth85879640@if2 및 vethee201166@if2: CNI 네트워크 인터페이스에 속하는 가상 인터페이스들로, 각각 cni0 인터페이스에 속함
eth0@if8256 및 eth1@if8260: 호스트 노드에 있는 실제 물리 인터페이스

# node와 관련된 route 정보 
ip route  
# 기본 라우트. 어떤 목적지로 가는 패킷이 192.168.0.1을 통해 전송되어야 함. dev etho0는 네트워크 인터페이스
# 노드 내부에서 사용되는 flannel 네트워크에 대한 라우트. 10.244.0.0/32 범위는 flannel.1을 통해 전송
# 다른 네트워크의 eth1로 가는 패킷에 대한 라우트. 172.25.0.0/24 범위는 eth1를 통해 전송
# 다른 네트워크의 eth0로 가는 패킷에 대한 라우트. 192.168.0.0/24 범위는 eth0을 통해 전송
default via 192.168.0.1 dev eth0 proto static 
10.244.0.0/32 dev flannel.1 proto kernel scope link src 10.244.0.0 
172.25.0.0/24 dev eth1 proto kernel scope link src 172.25.0.14 
192.168.0.0/24 dev eth0 proto kernel scope link src 192.168.0.100 


# network 연결과 라우팅 테이블 정보 확인
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
Proto Recv-Q Send-Q Local Address           Foreign Address         State      
tcp        0      0 127.0.0.1:2379          0.0.0.0:*               LISTEN     
tcp        0      0 127.0.0.1:2380          0.0.0.0:*               LISTEN     
tcp        0      0 192.168.1.10:22         192.168.1.20:12345      ESTABLISHED
tcp6       0      0 :::80                   :::*                    LISTEN     
udp        0      0 0.0.0.0:53              0.0.0.0:*                          
udp6       0      0 :::53                   :::*    


# 현재 실행 중인 프로세스
ps
-a: 모든 사용자의 프로세스
-u <user>: 특정 user의 프로세스
-x: 헤더를 표시하지 않음
-e: 모든 프로세스를 표시
-f: 전체 형식으로 프로세스 표시
-aux: 모든 사용자의 모든 프로세스를 상세정보와 함께

# CNI 정보 확인 (Path: /etc/cni/net.d/xxx)
cat /etc/cni/net.d/10-flannel.conflist 
{
  "name": "cbr0",
  "cniVersion": "0.3.1",
  "plugins": [
    {
      "type": "flannel", # flannel 실행 후 
      "delegate": {
        "hairpinMode": true,
        "isDefaultGateway": true
      }
    },
    {
      "type": "portmap", # portmap 실행
      "capabilities": {
        "portMappings": true
      }
    }
  ]
}

# Ingress
k get ingress -A  # HOST 정보 확인
k get ingress <name> -o yaml # 만약 path가 정의된 것이 없으면 no service로 감 (404 에러 페이지)
```

## 7. Security
```yaml
/etc/kubernetes/manifests/kube-apiserver.yaml
spec:
  containers:
  - command:
    - kube-apiserver
      <content-hidden>
    image: k8s.gcr.io/kube-apiserver-amd64:v1.11.3
    name: kube-apiserver
    volumeMounts:
    - mountPath: /tmp/users
      name: usr-details
      readOnly: true
  volumes:
  - hostPath:
      path: /tmp/users
      type: DirectoryOrCreate
    name: usr-details

curl -v -k https://localhost:6443/api/v1/pods -u "user1:password123"
```