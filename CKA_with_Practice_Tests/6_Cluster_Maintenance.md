6_Cluster_Maintenance

# Cluster Maintenance

## OS 업그레이드
- `k drain node-1`: 노드를 의도적으로 비움
    - replica가 있는 경우, 컨테이너들은 다른 노드로 이동
    - replica가 없는 경우, 해당 명령을 활용할 수 없음 > deployment로 변경 후, 진행
- `k cordon node-2`: 노드를 예약 불가능의 상태로 만듬
- `k uncordon node-1`: 노드를 재부팅
    - 이후 새로운 Pod를 받게 됨
