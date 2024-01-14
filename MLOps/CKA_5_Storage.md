CKA_5_Storage

# Storage
- 스토리지는 컨테이너화된 애플리케이션의 데이터를 저장하고 관리
- 데이터의 지속성, 공유성, 확장성 등을 보장

### Volume
- 컨테이너 내부의 파일 시스템과 별도로 데이터를 저장
- Pod 내의 여러 컨테이너 간에 데이터를 공유할 수 있으며, Pod이 재시작되어도 데이터가 유지
- Type: EmptyDir, HostPath, NFS, AWS EBS, GCE Persistent Disk 등
```yaml
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:  # 컨테이너 내부에서 볼륨을 마운트하기 위함
        - name: my-volume
          mountPath: /data  # 컨테이너 내부의 /data와 설정한 Volume을 연결

--- # EmptyDir
  volumes:
    - name: my-volume
      emptyDir: {}  # 빈 directory 볼륨을 생성

--- # HostPath
  volumes:
    - name: my-volume
      hostPath:
        path: /path/on/host  # 파일 시스템 경로를 마운트 (Pod가 접근할 수 있어야함)

--- # NFS
  volumes:
    - name: my-volume
      nfs:
        server: nfs-server  # NFS 서버 주소
        path: /exported/path  # 공유된 디렉토리 경로

--- # AWS EBS
  volumes:
    - name: my-volume
      awsElasticBlockStore:  # AWS EBS(Elastic Block Store)를 생성
        volumeID: <volume-id>
        fsType: ext4
```


### Persistent Volume
- 클러스터 전체에서 공유되는 스토리지 볼륨으로, Pod 간에 데이터를 공유
- 스토리지 프로비저너를 사용하여 동적으로 생성하거나, 사전에 정의된 스토리지를 사용
- 옵션
    - accessModes
        - ReadWriteOnce: 단일 노드에서 읽기/쓰기 접근을 허용. PV는 하나의 노드에만 마운트될 수 있으며, PVC는 한 번에 하나의 Pod에만 연결 가능
        - ReadOnlyMany: 여러 노드에서 읽기 접근을 허용
        - ReadWriteMany: 여러 노드에서 읽기/쓰기 접근을 허용. 이 모드를 지원하는 스토리지 백엔드는 제한적일 수 있음
    - persistentVolumeReclaimPolicy
        - Retain
            - PV이 삭제되거나 PVC와의 바인딩이 해제되어도 PV와 그 내용을 보존 (사용자가 수동으로 PV를 삭제해야 함)
            - 데이터의 영구 보존이 필요한 경우 사용
        - Delete
            - PV이 삭제되거나 PVC와의 바인딩이 해제되면 PV와 그 내용이 자동으로 삭제
            - PV를 재사용하지 않고 삭제할 때 사용
- 상태
    - Available: PV이 새로 생성되었고 아직 클레임에 바인딩되지 않은 상태
    - Bound: PV이 클레임에 바인딩되어 사용 중인 상태. PV이 클레임과 매핑되었으므로 해당 클레임으로부터 PV에 액세스할 수 없음
    - Released: PV이 클레임과의 바인딩이 해제된 상태. 클레임이 삭제되었을 때 PV과 바인딩이 해제되며, 이후 다른 클레임에 바인딩할 수 있도록 다시 Available 상태로 돌아감.

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi  # 용량 1Gi
  accessModes:
    - ReadOnlyMany 
  persistentVolumeReclaimPolicy: Retain  
  storageClassName: standard
  hostPath:
    path: /data/my-pv
```


### Persistent Volume Claim
- Pod에서 Persistent Volume을 요청하기 위해 사용되는 추상화된 개념
    - 원하는 용량, 액세스 모드, 스토리지 클래스 등 지정
- 관리자가 Persistent Volume을 생성 >> 사용자는 Persistent Volume Claim을 생성
    - PVC가 생성되면 PV은 클레임을 기반으로 바인딩 됨
    - 만약 가능한 PV가 없는 경우, PVC는 보류 상태로 유지됨
        - 용량이 필요한 만큼 없음
        - accessmode가 다름
- Pod에서 사용 중이라면 삭제할 수 없음 (Terminating 상태)
- 옵션
    - accessModes
        - ReadWriteOnce: PVC를 사용하는 단일 노드에서 동시에 읽고 쓸 수 음
        - ReadOnlyMany: 여러 노드에서 PVC를 동시에 readonly
        - ReadWriteMany: 여러 노드에서 PVC를 동시에 읽고 쓸 수 있음. 이 접근 모드는 일부 스토리지 백엔드에서 지원하지 않을 수 있음
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:
        - name: my-volume
          mountPath: /data
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: my-pvc
```

#### PV와 PVC를 나눈 이유
- 추상화
    - 클러스터 관리자는 스토리지 자원을 프로비저닝하고 관리하고, 개발자는 Pod에서 필요한 스토리지를 요청하는 것에 집중
- 재사용성
    - PV는 여러 PVC에 바인딩될 수 있으며, PVC는 여러 Pod에서 사용될 수 있음
    - 스토리지 자원의 재사용성을 높이고, 여러 Pod 간의 스토리지 공유를 가능하게 함
    - PV는 개수가 제한적임
- 독립성
    - PV와 PVC의 분리는 Pod과 스토리지 자원 사이의 독립성을 제공 
    - 만약 PV에 바로 연결한다면 Pod는 같은 클러스터 내 너무 많은 데이터를 공유함



### Storage Class
- PersistentVolume을 동적으로 프로비저닝하는 방법을 정의하는 리소스
  - 러스터의 스토리지 관리를 편리하게 해주고, PVC를 통해 사용자가 스토리지 리소스를 요청할 때 유연하게 스토리지를 프로비저닝하는 기능을 제공
  - 다양한 스토리지 프로비저너(플러그인)와 연결
- 개념/특징
  - 프로비저너(Provisioner)
    - 스토리지 클래스는 스토리지 프로비저너를 지정
    - PersistentVolume을 동적으로 생성하고 관리하는데 사용되는 스토리지 플러그인을 의미
    - AWS의 Elastic Block Store(EBS)나 Google Cloud의 Persistent Disk 등
  - 볼륨 프로비저닝
    - 스토리지 클래스를 사용하여 PersistentVolumeClaim(PVC)을 생성하면, 해당 스토리지 클래스가 정의한 프로비저너를 사용하여 PVC에 대응하는 PersistentVolume을 동적으로 프로비저닝
  - 매개 변수화(Parameters)
    - 스토리지 클래스는 프로비저너에게 전달되는 매개 변수를 정의할 수 있음
    - 스토리지 프로비저너에 따라 다르며, 스토리지 클래스를 사용하여 스토리지를 프로비저닝할 때 이러한 매개 변수들을 제공
  - 동적 프로비저닝(Dynamic Provisioning)
    - 스토리지 클래스를 사용하면 PVC와 바인딩되는 PV를 미리 정의하고 생성할 필요 없이, PVC를 생성할 때 스토리지 클래스를 지정하면 해당 클래스가 정의한 프로비저너를 사용하여 PV를 동적으로 프로비저닝
- Provisional의 종류
  - kubernetes.io/aws-ebs
  - kubernetes.io/gce-pd (Persistence Disk)
  - kubernetes.io/azure-disk
  - kubernetes.io/nfs (NFS(Network File System))
  - kubernetes.io/local
  - kubernetes.io/no-provisioner (Provisioner 없음)

```yaml
# Storage Calass 정의
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: nfs-storage
provisioner: kubernetes.io/nfs
parameters:
  nfs.server: 192.168.1.100   # NFS 서버 IP 주소
  nfs.path: /exports/data     # NFS 서버 내의 디렉토리 경로

# PVC 정의
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  storageClassName: nfs-storage   # 위에서 정의한 StorageClass 이름
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi   # 요청하는 스토리지 용량

# POD 정의
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
      volumeMounts:
        - name: my-pvc-volume
          mountPath: /usr/share/nginx/html
  volumes:
    - name: my-pvc-volume
      persistentVolumeClaim:
        claimName: my-pvc   # 위에서 생성한 PVC 이름
```






