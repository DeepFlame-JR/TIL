# 자료 구조
- 데이터를 관리하고 수정, 삭제, 탐색, 저장할 수 있는 데이터 집합

## 복잡도
- 복잡도는 시간 복잡도와 공간 복잡도로 나뉜다

### 시간 복잡도
- 어떤한 알고리즘의 로직이 얼마나 오래 걸리는지 나타내는 데 쓰임
- 보통 빅오 표기법으로 나타냄

#### 빅오 표기법
- 입력 범위 n을 기준으로 로직이 몇 번 반복되는지 나타냄
- $2N^2 + 10 > N^2$
    - '가장 영향을 많이 끼치는' 항의 상수 인자를 빼고 나머지 항을 없앰  
    - 입력 크기가 커질 수록 연산량이 가장 많이 커지는 항이 $N^2$이며 다른 것은 그에 비해 미미함

#### 자료 구조별 시간 복잡도 (평균/최악)

자료 구조|접근|탐색|삽입|삭제
--|--|--|--|--
배열|O(1)|O(N)|O(N)|O(N)
연결 리스트|O(N)|O(N)|O(1)|O(1)
큐|O(N)|O(N)|O(1)|O(1)
스택|O(N)|O(N)|O(1)|O(1)
해시 테이블|O(1)/O(N)|O(1)/O(N)|O(N)|O(N)
이진 탐색 트리|O(logN)/O($N^2$)|O(logN)/O($N^2$)|O(logN)/O($N^2$)|O(logN)/O($N^2$)
AVL 트리|O(logN)|O(logN)|O(logN)|O(logN)
레드 블랙 트리|O(logN)|O(logN)|O(logN)|O(logN)

최악의 경우  
- 해시 테이블: 해싱 제대로 되지 않아 하나의 버켓에 모든 데이터가 쌓였을 경우
- 이진 탐색 트리: 한쪽으로 편향되게 데이터가 쌓였을 경우


### 공간 복잡도
- 프로그램을 실행시켰을 때 필요로 하는 자원 공간의 양
- ```int arr[1004]``` 배열은 1004*4 바이트 크기를 가지게 된다

## 선형 자료 구조
- 요소가 일렬로 나열되어 있는 자료 구조

### Array
- 논리적 순서와 물리적 순서가 같다 (인접한 메모리 위치에 있는 데이터를 모아둔 집합)
- 시간 복잡도
    - Access: O(1)  
    랜덤 액세스(직접 접근)이 가능. 인덱스를 통해 바로 데이터 접근.
    - Search: O(n)  
    순차 탐색일 경우, 찾고자 하는 원소가 가장 끝에 위치하고 있는 최악의 경우 O(n)
    - Insertion/Deletion: O(n)  
    원소를 삽입/삭제하는 과정에서 많은 원소를 이동  
    예를들어 맨 처음 자리에 원소를 삽입한다면 나머지 원소들을 모두 이동해야한다.
- 데이터 탐색에 유리

### LinkedList
- 데이터를 감싼 노드를 포인터로 연결
- 시간 복잡도
    - Access: O(n)  
    연결 리스트는 순차 접근(Sequential Access)만 가능하므로 최악의 경우 O(n)이 된다.
    - Search: O(n)  
    맨 처음 노드부터 하나씩 탐색해야 하므로 최악의 경우 O(n)이 된다.
    - Insertion/Deletion: O(n)  
    삽입/삭제를 위해 원하는 위치를 찾는 과정에서 O(n)의 시간복잡도가 일어난다.
- 데이터 삽입 삭제에 유리
- 종류
    - 싱글 연결 리스트: next 포인터만 가짐
    - 이중 연결 리스트: prev, next 포인터를 가짐
    - 원형 이중 연결 리스트: 이중 연결 리스트와 같지만, 마지막 노드의 next가 head를 가리킴

### Stack
- Last In First Out (LIFO)의 특징을 가짐
- 재귀적인 함수/알고리즘, 웹 브라우저 방문 기록 등에 쓰임
- 시간 복잡도 
    - 삽입/삭제: O(1) 
    - 탐색: O(n)

### Queue
- First in Fisrt Out (FIFO)의 특징을 가짐
- CPU 작업을 기다리는 프로세스/스레드 행렬, BFS, 캐시 등에 이용됨
- 시간 복잡도 
    - 삽입/삭제: O(1) 
    - 탐색: O(n)

## 비선형 자료구조
- 일렬로 나열하지 않고 자료 순서나 관계가 복잡한 구조
- 일반적으로 트리나 그래프

### Graph
- 정점과 간선으로 이루어진 자료구조
    - 정점(vertex): 위치라는 개념
    - 간선(edge): 위치와의 관계
        - 단방향 간선: 한쪽으로만 이동
        - 양반향 간선: 양쪽으로 이동
- 차수(degree)
    - degree: 무방향 그래프에서 하나의 정점에 인접한 정점의 개수
    - in-degree: 방향 그래프에서 외부에서 오는 간선의 수
    - out-degree: 방향 그래프에서 외부로 향하는 간선의 수
- 그래프 탐색
    - **DFS**  
        - 임의의 정점으로부터 연결되어 있는 한 정점으로만 나아간다. 
        - 연결된 정점이 없다면 그 전의 정점으로 돌아와 연결된 정점이 있는지 확인한다. 
        - 재귀함수(Stack 자료구조) 사용
    - **BFS**  
        - 임의의 정점으로부터 연결되어 있는 모든 정점으로 나아간다. 
        - 최초 정점을 Queue에 넣고, dequeue를 통해 추출된 정점에서 연결되어있는 모든 정점을 Queue에 넣는 과정을 반복한다.

### Tree
- 그래프 중 하나로 트리 구조로 배열된 계층적 데이터의 집합
- 특징
    1. 부모, 자식 계층 구조를 가짐
    1. $(노드 수) = (간선 수)-1$ (root 외에 모두 간선이 연결되어 있음)
    1. 임의의 두 노드 사이의 경로는 무한개로 존재 (모두 연결되어 있음)
- 용어
    - level: 루트 노드에서 특정 노드로 최단 거리로 갔을 때의 거리
    - height: 트리의 최대 레벨

### Binary Tree (이진 트리)
- 자식 노드 수가 2개 이하인 트리
- 종류
    - 정 이진 트리: 자식 노드가 0 또는 2개인 이진 트리
    - 완전 이진 트리: 왼쪽에서부터 채워져 있는 이진 트리
    - 포화 이진 트리: 모든 레벨이 꽉 찬 이진 트리
    - 균형 이진 트리: 왼쪽과 오른쪽 노드의 높이 차이가 1이하인 이진 트리

### BST (Binary Search Tree)
- 효율적인 탐색을 위한 데이터 저장 방법
- 규칙
    1. 노드에 저장된 키는 유일
    1. 왼쪽 자식 노드의 키가 부모 키보다 작다
    1. 오른쪽 자식 노드의 키가 부모 키보다 크다
    1. 왼쪽, 오른쪽 서브 트리도 BST이다
- 구현
    - 탐색
        1. 노드 값과 비교한다.
        2. 노드 값보다 작으면 왼쪽으로 이동하고, 크다면 오른쪽으로 이동한다.
    - 삽입
        1. 노드 값과 비교한다.
        2. 노드 값보다 작으면 왼쪽으로 이동하고, 크다면 오른쪽으로 이동한다.
        3. 이동할려는 위치에 노드가 없다면 해당 위치에 노드를 생성하고, 리턴한다.
    - 삭제 
        1. 삭제할 노드를 탐색한다. 
        2. 해당 노드가 리프 노드일 경우, 해당 노드를 제거한다. 
        3. 자식이 한 쪽 노드만 있을 경우, 해당 노드를 제거하고 자식 노드를 올린다.
        4. 자식이 두 쪽 노드에 있을 경우, 오른쪽 노드에서 가장 작은 수를 올린다. (오른쪽으로 한번가고, 왼쪽으로 계속 가면 나옴)
- 장점
    - $O(logn)$의 시간 복잡도
- 단점
    - 최악의 경우(편향 트리) O(n)의 시간 복잡도를 가짐
    - 배열보다 많은 메모리를 사용했지만 탐색에 필요한 시간은 같게되는 비효율적 상황 발생

### AVL(Adelson-Velsky and Landis) Tree
- 회전(Rotate)과정을 통해 스스로 균형을 잡는 BST
- 탐색/삽입/삭제 모두 $O(logn)$의 시간복잡도를 가짐
- Balance Factor(BF) = height(left sub tree) - height(right sub tree)
    - BF는 -1~1의 값을 가짐

#### Rotate
- 삽입, 삭제 시 노드의 배열에 따라 (LL, RR, LR, RL) 불균형이 발생함
- 각 상황에 따라 rotation 방향에 따라 균형을 맞춤

1. LL(Left Left) Case
    - Right Rotate를 통해 균형을 맞춘다.
    - y 노드가 새로운 루트 노드가 된다.
    - y 노드의 오른쪽 자식 노드는 z 노드의 왼쪽 자식 노드가 된다.  
    (y 노드의 오른쪽 자식 노드는 z 노드보다 작은 수들이다)

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FxLIeV%2Fbtq2Xb7eZdF%2F0tfPz6aL4PEFaIJC6CvTs1%2Fimg.png">

2. RR(Right Right) Case
    - Left Rotate를 통해 균형을 맞춘다.
    - y 노드가 새로운 루트 노드가 된다.
    - y 노드의 왼쪽 자식 노드는 z 노드의 오른쪽 자식 노드가 된다.  
    (y 노드의 왼쪽 자식 노드는 z 노드보다 큰 수들이다)

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FMgydF%2Fbtq2ZpcT9dF%2FWNzhK8Ka9KmiuX6iqj5Ws0%2Fimg.png">

3. LR(Left Right) Case
    - Left Rotate > Right Rotate를 통해 균형을 맞춘다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtMu3I%2Fbtq21Mk69Ei%2FTToajHJiFvy3FmNYlbagj0%2Fimg.png">

4. RL(Right Left) Case
    - Right Rotate > Left Rotate를 통해 균형을 맞춘다.

    <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbrTQV1%2Fbtq2TcMbXA3%2FmhrY8bPspDrRT90kkGDIR1%2Fimg.png">

### Red Black Tree
