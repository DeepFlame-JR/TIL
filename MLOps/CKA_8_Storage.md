CKA_8_Storage

# Storage

## Storage
- Storage Driver: 이미지와 컨테이너의 storage 관리를 도움
- Volume Driver: 볼륨을 관리함. Storage를 유지하기 위해서는 Volume을 생성해야함
    - `docker run -it --name mysql --volume-driver rexray/ebs` 볼륨 연결 가능

#### CSI (Container Storage Interface)
- CRI(Container Runtime Interface)
    - 과거에는 K8S가 docker만 컨테이너 엔진으로 활용 >> 현재는 여러 가지 엔진이 활용됨
        - 이것이 Interface가 발생한 계기
    - 표준만 지킨다면 쉽게 K8S에 적용할 수 있음
- CSI 표준을 기반으로 하는 솔루션들은 많은 부분을 건드리지 않고도 쉽게 활용할 수 있음
    - CreateVolume
    - DeleteVolume
    - ControllerPublishVolume

### Volume
- Pod가 삭제되어도 데이터가 유지됨
    - 기존에는 Pod가 삭제되면 데이터도 함께 삭제됨
- 생성
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    name: test-pd
    spec:
    containers:
    - image: registry.k8s.io/test-webserver
      name: test-container
      # 컨테이너 내부에서 접근할 수 있는 경로
      ## 정의한 volume 중 마운트할 volume을 설정
      volumeMounts:
        - mountPath: /test-pd
          name: host-volume
    volumes:
    # host에 볼륨을 지정
    ## 이 경우에는 컨테이너가 어느 Node에 생성될지 모르기 때문에 위험성이 있음
    - name: host-volume
        hostPath:
            path: /data
            type: Directory
    # AWS EBS에 볼륨을 지정
    - name: aws-volume
        awsElasticBlockStore:
            volumeID: "<volume id>"
            fsType: ext4
    # PVC 사용
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
    ```

#### Persistent Volume (PV)
- 관리자가 대규모 스토리지 풀을 생성하고, 사용자가 필요에 따라 조각을 사용할 수 있도록 함
    - 기존에는 Pod 안에서 항상 Volume을 정의해야 하는 번거러움이 존재
- 생성
    ```yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
    name: pv0003
    spec:
        capacity:
            storage: 5Gi
        volumeMode: Filesystem
        accessModes:
            - ReadOnlyMany
            - ReadWriteOnce
            - ReadWriteMany
        awsElasticBlockStore:
            volumeID: "<volume id>"
            fsType: ext4
    ```

#### Persistent Volume Claim (PVC)
- 관리자가 Persistent Volume을 생성 >> 사용자는 Persistent Volume Claim을 생성
    - PVC가 생성되면 PV은 클레임을 기반으로 바인딩 됨
    - label과 selector를 통해서 1대1 매칭이 되도록 한다
    - 만약 가능한 PV가 없는 경우, PVC는 보류 상태로 유지됨
        - 용량이 필요한 만큼 없음
        - accessmode가 다름
- Pod에서 사용 중이라면 삭제할 수 없음
- 생성
    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
        name: myclaim
    spec:
        accessModes:
            - ReadWriteOnce
        resources:
            requests:
                storage: 8Gi
        volumeMode: Filesystem
        storageClassName: slow
        selector:
            matchLabels:
                release: "stable"
            matchExpressions:
                - {key: environment, operator: In, values: [dev]}
    ```