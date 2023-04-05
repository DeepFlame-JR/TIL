CKA_4_Logging_Monitoring


### K8S 모니터링 
- K8S 리소스 소비를 모니터링
    - 클러스터의 노드 수, 상태
    - 각 포드의 성능 측정항목
- 표준 솔루션은 없지만, 다양한 오픈 소스가 존재
    - Metrics Server, Prometheus, Elstic Stack, Datadog, Dynatrace 등

#### Metrics Server
- 기존에 Heapster에서 업그레이드된 버전
- 노드 > `kubelet` > `cAdvisor` > Kubelet API > `Metrics Server`
- GitHub를 통한 설치 진행
- 가장 많은 리소스를 사용한 요소 확인해보기
    - `kubectl top node`
    - `kubectl top pod`

### Application Logging
- pod Log보기
    ```powershell
    kubectl logs podName containerName
    # 스트림 모드
    kubectl logs -f podName containerName
    ```
    