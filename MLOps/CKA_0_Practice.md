CKA_0_Practice

# CKA Practice

## 1. Core Concept

```powershell
# 약어 확인
kubectl api-resources

kubectl apply -f <pod-definition.yaml>

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