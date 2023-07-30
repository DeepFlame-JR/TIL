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

## 1. Core Concept

```powershell
# 약어 확인
kubectl api-resources

kubectl apply -f <pod-definition.yaml>
kubectl replace --force -f <pod-definition.yaml>  # edit 후에 적용이 안 된 /tmp/ 파일 대상

kubectl get po my-pod -o yaml > my-pod.yaml

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
# kube system의 POD 확인
k get po -n kube-system

# nodeName 수동 설정
spec:
  nodeName: my-node

# selector 확인
kubectl get pods --selector env=prod,bu=finance,tier=frontend

# Node에서 taint 추가/제거
kubectl taint nodes node1 key1=value1:NoSchedule
kubectl taint nodes node1 key1=value1:NoSchedule-

# static pod 현황 확인 (보통 kube-system에 생성됨)
kubectl get pods -o wide -A

# static pod 생성
# restart=never: StaticPod가 종료된 후 다시 시작되지 않음
# dry-run=client: 실제로 리소스를 생성하지 않고, 생성할 리소스의 내용을 확인
# -o yaml: 생성된 리소스를 yaml 형식으로 출력
# command -- sleep 1000: sleep 1000 명령어 실행
kubectl run --restart=Never --image=busybox static-busybox --dry-run=client -o yaml --command -- sleep 1000 > /etc/kubernetes/manifests/static-busybox.yaml

# 다른 노드에 있는 static pod 경로
ssh node01
vi /var/lib/kubelet/config.yaml # static pod 경로 확인

# Scheduler 설정
spec:
  schedulerName: my-scheduler
```

## 3. Logging & Monitoring

```powershell
# 리소스 사용량 확인
k top node
k top pod

k logs <pod-name>
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
```