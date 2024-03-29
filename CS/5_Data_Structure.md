5_Data_Structure

# 자료 구조
- 데이터를 관리하고 수정, 삭제, 탐색, 저장할 수 있는 데이터 집합

## 복잡도
- 자료 구조에서 수행되는 연산의 시간 복잡도와 공간 복잡도를 나타냄
    - 알고리즘과 자료 구조의 효율성을 평가하는 데 중요한 지표

### 시간 복잡도
- 알고리즘이 입력 크기에 따라 소요되는 시간을 분석
- 일반적으로 빅오 표기법(Big O notation)을 사용
    - O(1): 상수 시간 복잡도. 입력 크기에 관계없이 항상 일정한 시간이 소요
    - O(log n): 로그 시간 복잡도. 입력 크기에 대해 로그 함수의 시간이 소요 > 일반적으로 효율적인 알고리즘
    - O(n): 선형 시간 복잡도. 입력 크기에 비례하여 선형적으로 시간이 소요
    - O(n^2): 제곱 시간 복잡도. 입력 크기의 제곱에 비례하여 시간이 소요

#### 자료 구조별 시간 복잡도 (평균/최악)

자료 구조|접근|탐색|삽입|삭제|비고
--|--|--|--|--|--
배열|O(1)|O(N)|O(N)|O(N)|
연결 리스트|O(N)|O(N)|O(1)|O(1)|
큐|O(N)|O(N)|O(1)|O(1)|
스택|O(N)|O(N)|O(1)|O(1)|
해시 테이블|O(1)/O(N)|O(1)/O(N)|O(1)/O(N)|O(1)/O(N)| 해싱이 제대로 되지 않아 하나의 버켓에 모든 데이터가 쌓였을 경우
이진 탐색 트리|O(logN)/O($N^2$)|O(logN)/O($N^2$)|O(logN)/O($N^2$)|O(logN)/O($N^2$)|한쪼기으로 편향되게 데이터가 쌓였을 경우
AVL 트리|O(logN)|O(logN)|O(logN)|O(logN)
레드 블랙 트리|O(logN)|O(logN)|O(logN)|O(logN)


### 공간 복잡도
- 알고리즘이 실행되는 동안 사용하는 추가 메모리 공간의 양
- O(1)은 추가 메모리를 사용하지 않는 상수 공간을 의미하고, O(n)은 입력 크기에 비례하는 공간


## 선형 자료 구조
- 요소가 일렬로 나열되어 있는 자료 구조

### Array
- 동일한 데이터 타입을 가진 요소들을 연속적으로 저장하는 선형 자료 구조
- 시간 복잡도
    1. 삽입 
        - 최선의 경우: O(1) (배열의 맨 끝에 요소를 추가하는 경우)
        - 최악의 경우: O(n) (배열의 맨 앞에 요소를 추가하는 경우, 모든 요소를 한 칸씩 오른쪽으로 이동해야 함)
        - 평균적인 경우: O(n) (배열의 중간에 요소를 추가하는 경우, 해당 위치 이후의 요소들을 한 칸씩 오른쪽으로 이동해야 함)
    1. 삭제
        - 최선의 경우: O(1) (배열의 맨 끝에 있는 요소를 삭제하는 경우)
        - 최악의 경우: O(n) (배열의 맨 앞에 있는 요소를 삭제하는 경우, 모든 요소를 한 칸씩 왼쪽으로 이동해야 함)
        - 평균적인 경우: O(n) (배열의 중간에 있는 요소를 삭제하는 경우, 해당 위치 이후의 요소들을 한 칸씩 왼쪽으로 이동해야 함)
    1. 탐색: 
        - 평균적인 경우: O(n) (요소의 위치를 정확히 모르는 경우 배열을 순차적으로 탐색해야 함)
    1. 접근
        - 평균적인 경우: O(1) (인덱스를 통해 요소에 접근하는 경우, 배열의 크기와 상관없이 고정된 시간이 소요됨)
- 장점
    - 크기가 고정되어 있음 > 정적인 자료구조
    - 배열은 요소들이 연속적으로 메모리에 저장 > 인덱스를 통한 빠른 접근
- 단점
    - 크기가 고정되어 있고, 요소 추가 및 삭제가 어려움
    - 크기를 변경하려면 새로운 배열을 생성하고 기존 요소를 복사해야 하기 때문에 비용이 크게 증가
    - 추가나 삭제 시에는 데이터를 이동시켜야 하므로 처리 비용이 높음

### LinkedList
- 데이터 요소들이 연결된 노드(Node)로 구성된 선형 자료구조
    - 각 노드는 데이터 요소와 다음 노드를 가리키는 포인터(참조)로 이루어짐
- 시간 복잡도
    - 삽입
        - 처음 위치에 삽입: O(1) (head를 업데이트)
        - 마지막 위치에 삽입: O(1) (tail을 업데이트)
        - 중간 위치에 삽입: O(n) (삽입 위치를 찾기 위해 순차적 탐색 필요)
    - 삭제    
        - 처음 위치 삭제: O(1) (head를 업데이트)
        - 마지막 위치 삭제: O(1) (tail을 업데이트)
        - 중간 위치 삭제: O(n) (삭제 위치를 찾기 위해 순차적 탐색 필요)
    - 탐색
        - 인덱스로 탐색: O(n) (탐색 위치까지 순차적으로 탐색)
        - 값으로 탐색: O(n) (모든 노드를 순차적으로 탐색해야 함)
    - 접근
        - 인덱스로 접근: O(n) (탐색 위치까지 순차적으로 탐색)
        - 값으로 접근: O(n) (모든 노드를 순차적으로 탐색해야 함)
- 장점
    - 삽입 및 삭제가 상대적으로 간단하고 빠름 > 동적인 자료 구조
    - 해당 노드의 주변 노드들만 조작
- 단점
    - 특정 위치의 요소에 접근하려면 첫 번째 노드부터 순차적으로 탐색 > 접근 시간이 선형적으로 증가
    - 데이터 요소와 다음 노드를 가리키는 포인터를 저장해야 하므로, 배열에 비해 메모리 공간을 더 많이 차지 > 추가적인 오버헤드, 메모리 공간이 필요
    - 접근이 많이 필요한 경우 성능 저하
- 종류
    - 싱글 연결 리스트: next 포인터만 가짐
    - 이중 연결 리스트: prev, next 포인터를 가짐
    - 원형 이중 연결 리스트: 이중 연결 리스트와 같지만, 마지막 노드의 next가 head를 가리킴

### Stack
- 데이터를 후입선출(LIFO, Last-In-First-Out)의 원칙에 따라 저장하는 자료구조
    - 제한된 접근: 스택의 맨 위에 있는 데이터에만 접근
    - 제한된 연산: 주요 연산은 푸시(Push)와 팝(Pop)
    - 크기 제한: 스택은 일반적으로 고정된 크기를 가지며, 최대 크기를 초과하여 데이터를 추가할 수 없음
- 장점
    - 구현이 간단하고 사용이 편리
    - 데이터의 추가 및 제거가 상수 시간(O(1))
    - 재귀 알고리즘, 괄호 검사, 함수 호출, 브라우저의 방문 기록(back button), 수식 평가 등 다양한 상황에서 활용
- 단점
    - 제한된 접근과 연산을 가지므로 데이터의 임의 접근이나 중간 데이터의 삽입/삭제가 어려움
    - 크기가 미리 정해져 있으며, 초과하는 데이터는 추가할 수 없음

### Queue
- 선입선출(FIFO, First-In-First-Out)의 원칙에 따라 데이터를 저장하는 자료구조
- 장점
    - 데이터의 순서를 보존
    - 삽입과 삭제 연산이 상수 시간에 이루어짐
    - 운영체제의 작업 스케줄링, 네트워크 패킷 처리, 버퍼 관리 등 다양한 분야에서 활용
- 단점
    - 중간 데이터에 접근하기 어려움
    - 큐의 크기에 제한이 있을 수 있음
    - 여러 스레드가 동시에 큐에 접근하여 데이터를 처리하는 경우, 동기화 문제가 발생


## 비선형 자료구조
- 데이터를 저장하고 조직화하는 방식에서 선형 자료구조와 달리 계층적이거나 상호 연결된 형태
- 트리, 그래프, 해시 테이블 등이 포함

### Tree
- 비선형 자료구조로서 계층적인 구조를 가지는 모델을 표현하는데 사용
- 구성
    - **Node**: 트리를 구성하고 있는 요소
    - **Edge**: 트리를 구성하기 위해 노드와 노드를 연결하는 선
    - **Root Node**: 트리 구조에서 최상위에 있는 노드
    - **Terminal Node(=leaf Node)**: 하위에 다른 노드가 연결되어 있지 않은 노드
    - **Internal Node**: leaf Node를 제외한 모든 노드로 루트 노드
    - **Level**: 각 층별로 숫자를 매긴 값 (루트 노드의 레벨은 0이다.)
    - **Height**: 트리의 최고 레벨
- 특징
    - 부모, 자식 계층 구조를 가짐
    - $(노드 수) = (간선 수)-1$ (root 외에 모두 간선이 연결되어 있음)
    - 임의의 두 노드 사이의 경로는 무한개로 존재 (모두 연결되어 있음)

### Binary Tree (이진 트리)
- 자식 노드 수가 2개 이하인 트리
    - 나누어진 두 서브 트리도 이진 트리
    - 공집합, 노드도 이진 트리
- 종류
    - 정 이진 트리: 자식 노드가 0 또는 2개인 이진 트리
    - 완전 이진 트리: 왼쪽에서부터 채워져 있는 이진 트리
    - 포화 이진 트리: 모든 레벨이 꽉 찬 이진 트리
    - 균형 이진 트리: 왼쪽과 오른쪽 노드의 높이 차이가 1이하인 이진 트리
- 1차원 표현
    - 완전 이진 트리와 정 이진 트리는 1차원으로 표현 가능
    ```py
    left = 2*parent+1
    right = 2*parent+2
    ```

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
- 장단점
    - 장점
        - 검색 속도가 매우 빠름 `O(log n)`
        - 데이터를 삽입하는 동시에 정렬된 상태를 유지
        - 데이터의 삽입과 삭제에 효율적
    - 단점
        - 편향된 트리 구조일 경우 효율성이 떨어짐 `O(n)`
        - 균형을 유지하는 작업이 필요
        - 추가적인 메모리 공간이 필요
            - 가장 오른쪽에 데이터가 삽입될 경우, 좌측 공간이 모두 낭비됨

### AVL(Adelson-Velsky and Landis) Tree
- 회전(Rotate)과정을 통해 스스로 균형을 잡는 BST
    - 탐색/삽입/삭제 모두 `O(lonN)`의 시간복잡도를 가짐
    - BF는 -1~1의 값을 가짐
        - Balance Factor(BF) = height(left sub tree) - height(right sub tree)
- 과정
    1. RR Case
        - RR Rotate를 통해 균형을 맞춤
        - 중앙 값을 parent로 올리고, parent는 좌측으로 내린다
        ```
        50              70
          \            /   \
            70        50   90
           /  \        \
         80     90      80
        ```
    1. RL Case
        - RL Rotate를 통해 균형을 맞춤
        - RR Case로 바꾼 후 RR Rocate
        ```
        A      A         B
         \      \       / \
          C      B     A   C
         /        \
        B          C
        ```

        ```
        50      50         60
         \       \        / \
          70      60     50  70
         /  \       \         \
        60   80      70        80
        ```
        

### Red Black Tree
- 이진 검색 트리의 일종으로, 각 노드에 "레드" 또는 "블랙"의 색상을 할당하여 균형을 유지하는 트리
- 규칙
    1. 루트 노드는 블랙이다.
    1. 모든 리프 노드는 블랙이다.
    1. 레드 노드의 자식은 모두 블랙이다. (빨간색 노드가 연속으로 나올 수 없음)
    1. 어떤 노드로부터 리프 노드까지의 모든 경로에는 동일한 개수의 블랙 노드가 있다.
- 장점
    - 탐색, 삽입, 삭제 연산의 평균 시간 복잡도는 `O(log n)`
    - AVL 트리보다 약간의 균형 조건을 덜 엄격하게 갖기 때문에, AVL 트리에 비해 삽입 및 삭제 연산이 더 빠름
    - 데이터의 동적인 삽입 및 삭제가 빈번하게 발생하는 경우에 적합
- 단점
    - AVL 트리에 비해 삽입 및 삭제 연산이 더 복잡하고 오버헤드가 큼
    - 메모리 사용량이 더 많을 수 있음
- 과정
    - 삽입
        - BST와 같이 삽입 > 색상을 레드
        - 조정 작업
            - 루트 노드에 삽입된 경우: 루트 노드를 블랙으로 만듬
            - 레드 노드의 부모가 레드인 경우
                - 레드 노드의 부모와 삼촌(부모의 형제)이 모두 레드인 경우: 부모와 삼촌을 블랙으로 변경하고, 부모의 부모(즉, 조부모)를 레드로 변경. 이러한 변경으로 인해 조부모와 삼촌이 레드로 연속될 수 있으므로, 조부모부터 다시 조정 작업을 수행
                - 레드 노드의 부모는 레드이지만, 삼촌이 블랙인 경우: 삽입된 노드의 위치에 따라 회전 연산을 수행하여 균형을 재조정합니다. LL, LR, RL, RR 네 가지 형태의 회전이 가능
    - 삭제
        - BST와 같이 제거 
        - 조정 작업
            - 삭제된 노드가 레드인 경우: 추가 작업 X
            - 삭제된 노드가 블랙인 경우
                - 삭제된 노드의 형제 노드가 레드인 경우: 형제와 부모의 색상을 교환하고 회전 연산을 수행합니다. 이를 통해 블랙 높이를 조절하여 균형을 유지합니다.
                - 삭제된 노드의 형제 노드가 블랙인 경우:
                    - 형제의 자식 노드가 모두 블랙인 경우: 형제를 레드로 변경하고 부모 노드를 삭제된 노드의 위치로 이동시킵니다. 삭제된 노드의 부모 노드에서 조정 작업을 수행합니다.
                    - 형제의 자식 노드 중 적어도 하나가 레드인 경우: 형제와 자식 노드의 색상을 조정하고 회전 연산을 수행하여 균형을 유지합니다.

참고: https://code-lab1.tistory.com/62
<img src="https://user-images.githubusercontent.com/40620421/248542943-1789cf61-676a-4173-91fc-b3d8977041ba.png" width="500">

### Heap
- Tree 중 배열에 기반한 완전 이진 트리 형식
    - 최댓값이나 최솟값을 빠르게 찾아내는 데에 주로 사용
    - 우선순위 큐에 사용됨
- 단점
    - 삽입과 삭제 연산이 `O(logn)`
    - 임의의 요소에 접근하는 데는 효율적이지 않음
    - 메모리 공간을 연속적으로 할당 받아, 메모리 낭비가 발생할 수 있음
- 고정
    - 삽입
        1. 새로운 요소를 Heap의 마지막 노드에 추가
        1. 추가된 요소를 부모 노드와 비교하여 힙의 특성을 유지하도록 조정
            - 최대 힙: 부모 노드보다 크다면 두 노드를 교환
            - 최소 힙: 부모 노드보다 작으면 두 노드를 교환
    - 삭제
        1. Root 노드를 제거, 마지막 노드와 교환
        1. 추가된 요소를 부모 노드와 비교하여 힙의 특성을 유지하도록 조정
            - 최대 힙: 부모 노드보다 크다면 두 노드를 교환
            - 최소 힙: 부모 노드보다 작으면 두 노드를 교환

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

### Hash Table
- (Key, Value)로 데이터를 저장하는 자료구조
- average case에 대하여 모든 작업의 시간 복잡도가 O(1)
    - Collision: 어설픈 해시 함수를 사용했을 때, 동일한 key 값에 복수 개의 데이터가 존재 > 시간 복잡도 증가

#### Hash Function
- 인덱스로 저장되는 key 값이 불규칙하다는 문제가 있음 (string, double 등)
- Hash Function수를 이용하여 key 값을 고유한 숫자 `hashcode`로 만들어 낸 뒤 이를 인덱스로 사용
- 대표 Hash Function  
    1. Division Method: 입력값을 테이블의 크기로 나누어 계산한다. (입력값%테이블의 크기)
    2. Digit Folding: 각 key의 문자열을 ASCII 코드로 바꾸고 값을 합하나 값을 사용한다.
    3. Multiplication Method: h(k) = (kA mod1)*m (0<A<1, m: 2의 제곱수)
    4. Univeral Hashing: 무작위로 해시함수를 선택해 hashcode를 만듬
- 좋은 Hash Function의 조건
    - 키의 일부분을 참조하여 해쉬 값을 만들지 않고 키 전체를 참조하여 해쉬 값을 생성
    - hash function를 무조건 1:1 로 만드는 것보다 `Collision 을 최소화하는 방향으로 설계`하고 발생하는 `Collision 에 대비해 어떻게 대응`할 것인가가 더 중요
    - 1:1 대응이 되도록 만드는 것이 거의 불가능하기도 하지만 그런 hash function를 만들어봤자 그건 array 와 다를바 없고 메모리를 너무 차지하게 됨
- Collision에 대응
    1. 분리 연결법
        - 동일한 인덱스에 복수의 데이터를 저장하는 방식
        - **장점:** 해시 테이블의 확장이 필요없고, 간단히 구현가능
        - **단점:** 데이터의 수가 많아지면 동일한 index에 많은 데이터가 연결되며 효율성이 떨어짐  
    1. 개방 주소법  
        - 추가적인 구조를 사용하지 않고, 해시 테이블의 공간을 활용하는 방식
            1. **Linear Probing**
                - Collision이 발생할 때, index로부터 고정 폭 만큼 씩 이동하여 차례대로 비어있는 공간에 데이터를 저장
                - 예. index 1에서 충돌 > index 2에 데이터 저장
            2. **Quadratic Probing**
                - 해시의 저장순서 폭을 제곱으로 저장하는 방식
                - 예시
                    - index 1에서 첫번째 충돌 > 1 + $1^2$ = 2에 저장
                    - index 1에서 두번째 충돌 > 1 + $2^2$ = 5에 저장
                    - index 1에서 세번째 충돌 > 1 + $3^2$ = 10에 저장
            3. **Double Hashing Probing**
                - 해시된 값을 한번 더 해싱하여 해시의 규칙성을 없애버리는 방식
                - 최초 Hashcode를 얻기 위해서 해싱 > 충돌이 났을 경우 두 번째 Hashcode를 탐사 이동 폭을 얻기 위해서 사용  


# Algorithm

## 정렬 알고리즘
- 데이터를 정렬하는 알고리즘
    - DB에서 데이터를 정렬할 때, 수많은 데이터를 얼마나 빠르게 정렬할 수 있는가는 중요한 문제
        - 탐색을 위해서 정렬 필요
    - 정렬 되어있지 않다면 순차 탐색 `O(n)`이지만, 정렬되어 있다면 이진 탐색 `O(logN)`으로 가능

### 버블 정렬
- 인접한 두 요소를 비교하여 순서가 잘못된 경우 위치를 교환
- 가장 큰 값이 뒤로 이동하며, 반복적으로 비교와 교환을 수행합니다.
- 시간 복잡도: 평균 및 최악의 경우 O(n^2), 최선의 경우 O(n) (이미 정렬된 경우)
```py
def bubble_sort(arr):
    n = len(arr)
    
    # 첫번째는 0~N-1, 두번째는 0~N-2 이런 식으로 진행되는데 그 이유는 1회 실시할 때 최댓값은 이미 맨 뒤로 저장되어 있기 때문
    for i in range(n-1):
        for j in range(n-1-i): 
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
    return arr
```

### 선택 정렬
- 최솟값을 찾아 첫 번째 요소와 교환 > 남은 부분에서 최솟값을 찾아 두 번째 요소와 교환 > 반복
- 시간 복잡도: 평균, 최악, 최선의 경우 모두 O(n^2)
```py
def selection_sort(arr):
    n = len(arr)
    
    for i in range(n-1):
        min_idx = i
        # 최솟값을 찾음
        for j in range(i+1, n):
            if arr[j] < arr[min_idx]:
                min_idx = j
        
        # 최솟값과 현재 요소를 교환
        arr[i], arr[min_idx] = arr[min_idx], arr[i]
    
    return arr
```

### 삽입 정렬
- 두 번째 요소부터 시작하여 이전에 정렬된 부분과 비교하여 올바른 위치에 삽입
- 정렬된 부분은 계속하여 증가하며, 삽입할 요소를 찾을 때까지 비교와 이동이 수행
- 시간 복잡도: 평균 및 최악의 경우 O(n^2), 최선의 경우 O(n) (이미 정렬된 경우)
- 예시
    - 예시 배열: [5, 3, 8, 4, 2]
        - 1번 패스: key = 3, key를 정렬된 부분 배열 [5]에 삽입: [3, 5, 8, 4, 2]
        - 2번 패스: key = 8, key를 정렬된 부분 배열 [3, 5]에 삽입(변화 없음): [3, 5, 8, 4, 2]
        - 3번 패스: key = 4, key를 정렬된 부분 배열 [3, 5, 8]에 삽입: [3, 4, 5, 8, 2]
        - 4번 패스: key = 2, key를 정렬된 부분 배열 [3, 4, 5, 8]에 삽입: [2, 3, 4, 5, 8]
```py
def insertion_sort(arr):
    n = len(arr)
    
    for i in range(1, n):
        key = arr[i]
        j = i - 1
        
        # key를 적절한 위치에 삽입
        while j >= 0 and arr[j] > key:
            arr[j+1] = arr[j]
            j -= 1
        
        arr[j+1] = key
    
    return arr
```

#### 병합 정렬
- 분할 정복 방식을 사용하여 배열을 절반으로 나눔 > 정렬된 부분 배열을 병합하여 전체 배열을 생성
- 시간 복잡도: 평균, 최악, 최선의 경우 모두 `O(nlogn)`
    - 높이가 `logn`, 각 층에서 `O(n)`연산
- 임시 저장 공간을 확보해야하는 부담이 있음
- 예시
    - 분할 단계: [5, 3, 8, 4, 2] -> [5, 3], [8, 4, 2] -> [5], [3], [8], [4, 2]
    - 병합 단계
        - [5], [3]을 병합하여 [3, 5] 생성
        - [8], [4, 2]를 병합하여 [2, 4, 8] 생성
        - [3, 5], [2, 4, 8]을 병합하여 [2, 3, 4, 5, 8] 생성
```py
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    
    mid = len(arr) // 2
    left_half = arr[:mid]
    right_half = arr[mid:]
    
    # 재귀적으로 왼쪽과 오른쪽 부분 배열을 정렬
    left_half = merge_sort(left_half)
    right_half = merge_sort(right_half)
    
    return merge(left_half, right_half)

def merge(left, right):
    result = []
    i = 0
    j = 0
    
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    
    # 남은 요소들을 결과에 추가
    while i < len(left):
        result.append(left[i])
        i += 1
    
    while j < len(right):
        result.append(right[j])
        j += 1
    
    return result
```

### 퀵 정렬 
- 분할 정복 방식을 사용하여 배열을 피벗을 기준으로 두 개의 부분 배열로 분할
- 피벗보다 작은 요소는 왼쪽에, 큰 요소는 오른쪽에 위치
- 시간 복잡도: 평균의 경우 `O(n log n)`, 최악의 경우 `O(N^2)` (계속해서 최솟값 또는 최대값을 뽑았을 경우)
```py
def quick_sort(arr):
    if len(arr) <= 1:
        return arr
    
    pivot = arr[len(arr) // 2]
    left = []
    right = []
    equal = []
    
    for num in arr:
        if num < pivot:
            left.append(num)
        elif num > pivot:
            right.append(num)
        else:
            equal.append(num)
    
    return quick_sort(left) + equal + quick_sort(right)
```

### 힙 정렬
- 힙 자료구조를 기반으로 한 정렬 알고리즘
- 초기 배열을 최대 힙(Max Heap)으로 구성 > 최댓값(루트 노드)을 꺼내 배열의 마지막 요소와 교환 > 가장 큰 요소는 배열의 마지막에 위치 > 힙의 크기를 1 감소시키고, 변경된 힙 구조에서 루트 노드부터 다시 최대 힙을 구성
- 시간 복잡도: 평균 시간 복잡도와 최악 시간 복잡도가 모두 `O(n log n)`으로 일정
```py
def heapify(arr, n, i):
    largest = i
    left = 2 * i + 1
    right = 2 * i + 2
    
    if left < n and arr[left] > arr[largest]:
        largest = left
    if right < n and arr[right] > arr[largest]:
        largest = right
    
    # 최댓값이 루트가 아니라면 교환
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        # 변경된 부분 힙에 대해 재귀적으로 heapify 호출
        heapify(arr, n, largest)

def heap_sort(arr):
    n = len(arr)
    
    for i in range(n // 2 - 1, -1, -1):
        heapify(arr, n, i)
    
    # 최대 힙에서 요소를 하나씩 꺼내 정렬
    for i in range(n - 1, 0, -1):
        arr[i], arr[0] = arr[0], arr[i]  # 최댓값과 마지막 요소 교환
        heapify(arr, i, 0)  # 변경된 부분 힙에 대해 heapify 호출
    
    return arr
```

## 그래프 알고리즘

### 최단 경로
- 다익스트라
    - 하나의 시작 정점에서 다른 모든 정점까지의 최단 경로를 찾는 알고리즘
        - 다익스트라 알고리즘은 음의 가중치를 가진 간선이 없는 그래프에서 사용 가능
        - 현재까지의 최단 거리를 기준으로 정점을 선택하고, 이를 통해 더 짧은 거리를 갱신
    - 시간 복잡도는 `O(V^2)` 또는 `O((V+E)logV)`입니다.
        - 우선순위 큐를 사용하여 정점을 선택하는 시간: `O(logV)`
        - 각 정점은 한번씩 방문되어 한번씩 검사: `O(V+E)`
    - 코드
        ```py
        import heapq
        import sys

        def dijkstra(graph, start):
            num_vertices = len(graph)
            distance = [sys.maxsize] * num_vertices
            distance[start] = 0
            
            pq = [(0, start)]  # (거리, 정점) 튜플을 우선순위 큐에 저장
            
            while pq:
                curr_distance, curr_vertex = heapq.heappop(pq)
                
                # 현재 정점에 도달할 수 있는 최소 거리 조건이 아니라면 continue
                if curr_distance > distance[curr_vertex]:
                    continue
                
                for next_vertex in range(num_vertices):
                    weight = graph[curr_vertex][next_vertex]
                    if weight != 0:
                        new_distance = distance[curr_vertex] + weight
                        # 새로운 정점에 최소 거리 조건으로 도달할 수 있다면 이동
                        if new_distance < distance[next_vertex]:
                            distance[next_vertex] = new_distance
                            heapq.heappush(pq, (new_distance, next_vertex))
            
            return distance
        ```
- 벨만포드
    - 음의 가중치를 가진 간선이 포함된 그래프에서 최단 경로를 찾는 알고리즘
        - 모든 정점을 순회하며 해당 정점으로 가는 최단 거리를 갱신. 이를 정점의 수만큼 반복하여 최단 경로를 찾습니다.
      - 벨만-포드 알고리즘은 음의 가중치를 가진 간선이 있어도 사용 가능하지만, 음수 사이클이 있는 경우에는 정확한 최단 경로를 찾을 수 없음
    - 시간 복잡도는 O(VE)
    - 코드
        ```py
        import sys

        def bellman_ford(graph, start):
            num_vertices = len(graph)
            distance = [sys.maxsize] * num_vertices
            distance[start] = 0
            
            # 모든 간선에 대해 (정점 개수 - 1)번 반복
            for _ in range(num_vertices - 1):
                for u in range(num_vertices):
                    for v in range(num_vertices):
                        weight = graph[u][v]
                        if weight != 0:
                            new_distance = distance[u] + weight
                            if new_distance < distance[v]:
                                distance[v] = new_distance
            
            # 음수 사이클 여부 검사
            for u in range(num_vertices):
                for v in range(num_vertices):
                    weight = graph[u][v]
                    if weight != 0:
                        new_distance = distance[u] + weight
                        if new_distance < distance[v]:
                            # 음수 사이클이 존재하여 최단 거리를 구할 수 없음
                            return None
            
            return distance
        ```

### 최소 신장 트리 (MST)
### 위상 정렬

## 동적 프로그래밍
- 큰 문제를 작은 하위 문제로 분할하여 풀고, 작은 하위 문제의 해결 방법을 저장하고 재활용하여 전체 문제를 해결하는 알고리즘 설계 기법
    - "작은 문제의 최적해는 항상 큰 문제의 최적해에 기여한다"라는 원칙
- 조건
    1. 부분 반복 문제 (Overlapping Subproblems): 작은 문제가 반복되고, 작은 하위 문제들 간에 중복되는 계산이 발생 > 계산 결과를 저장하여 재활용
    1. 최적 부분 구조 (Optimal Substructure): 작은 하위 문제들의 최적해를 결합하여 전체 문제의 최적해를 구할 수 있어야 함
- 재귀적인 방법
    1. 하향식(Top-down): 재귀적인 호출을 이용하여 작은 문제들을 해결
    1. 상향식(Bottom-up): 상향식은 작은 문제부터 시작하여 반복적으로 최적해를 계산

#### 피보나치 수열
- 문제 파악
    - 부분 반복 문제
        - F(5)를 구하기 위해서는 F(4)와 F(3)의 값을 알아야 합니다. F(4)를 구하기 위해서는 F(3)과 F(2)의 값을 알아야 하고, 이러한 과정이 반복
            - F(2), F(3)을 반복해서 계속 구해야 함
    - 최적 부분 구조
        - 작은 하위 문제들을 해결하여 큰 문제인 F(N)의 값을 구할 수 있음
- 풀이
    ```py
    # Top-Down
    def fibonacci(n):
        if n <= 1:
            return n
        return fibonacci(n-1)+fibonacci(n-2)

    # Bottom-Up
    def fibonacci(n):
        fib = [0] * (n+1)
        fib[0] = 0 ; fib[1] = 1

        for i in range(2, n+1):
            fib[i] = fib[i-1]+fib[i-2]
        return fib[i]
    ```

#### 부분합 문제
- 문제 파악
    - 부분 반복 문제
        - 주어진 수열의 앞부터 특정 위치까지의 연속된 부분 수열의 합을 구하는 문제로 나눌 수 있음
            - 예를 들어, 수열의 첫 번째부터 i번째 위치까지의 부분합을 구하는 문제와 수열의 첫 번째부터 (i-1)번째 위치까지의 부분합을 구하는 문제로 나눔
    - 최적 부분 구조
        - 작은 하위 문제들의 최적해를 결합하여 전체 문제의 최적해를 구할 수 있음
            - 예를 들어, 수열의 첫 번째부터 i번째 위치까지의 부분합을 구하는 문제에서, i번째 수를 포함하는 경우와 포함하지 않는 경우로 나눌 수 있음
- 풀이
    ```py
    # Top-Down
    def partial_sum(arr, n, cache):
        if n == 0:
            return arr[0]
        
        if cache[n] != -1:
            return cache[n]
        
        cache[n] = max(arr[n], arr[n] + partial_sum(arr, n-1, cache))
        return cache[n]

    # 사용 예시
    arr = [1, -2, 3, 4, -5, 2, 7]
    n = len(arr)
    cache = [-1] * n
    result = partial_sum(arr, n-1, cache)
    print(result)

    # Bottom-Up
    def partial_sum(arr):
        n = len(arr)
        dp = [0] * n
        dp[0] = arr[0]
        max_sum = dp[0]

        for i in range(1, n):
            dp[i] = max(arr[i], arr[i] + dp[i-1])
            max_sum = max(max_sum, dp[i])

        return max_sum

    # 사용 예시
    arr = [1, -2, 3, 4, -5, 2, 7]
    result = partial_sum(arr)
    print(result)
    ```

## 그리디 알고리즘
- 각 단계에서 가장 최적인 선택을 하는 방식으로 문제를 해결하는 알고리즘
    - 현재 상황에서 가장 이익이나 최대값을 추구하면서 단계별로 선택을 진행
    - 그 순간의 최적해를 구할 수 있지만, 이것이 전체적으로 최적해가 되지 않을 수도 있음 > 따라서 그리디 알고리즘은 항상 최적해를 보장하지는 않음 > 그러나 특정 조건에서는 그리디 알고리즘이 최적해를 구하는 데에 유용하게 사용
    - 근사해를 구하는 것에서도 사용
- 조건
    - 최적 부분 구조: 작은 하위 문제들의 최적해를 결합하여 전체 문제의 최적해를 구할 수 있는 성질
    - 탐욕 선택 속성: 각 단계에서의 최적 선택이 전체적인 최적해를 보장하는 속성 > 최적 선택이 다음 단계에서의 최적 선택과 독립적이고 영향을 주지 않을 때 탐욕 선택 속성을 가진 문제

#### 동전 문제
- 조건
    - 탐욕 선택 속성: 각 단계에서 가장 큰 단위의 동전을 선택. 이는 현재 상황에서 가장 최적인 선택
    - 최적 부분 구조: 작은 부분 문제들의 최적해를 결합하여 전체 문제의 최적해를 구할 수 있음
- 코드
    ```py
    def make_change(coins, amount):
        coins.sort(reverse=True)  # 동전을 큰 순서로 정렬합니다.
        num_coins = 0

        for coin in coins:
            num_coins += amount // coin  # 현재 동전으로 만들 수 있는 최대 개수를 구합니다.
            amount %= coin  # 남은 거스름돈을 갱신합니다.
        return num_coins

    # 사용 예시
    coins = [500, 100, 50, 10]
    amount = 1260
    result = make_change(coins, amount)
    print(result)
    ```


## 백트래킹
- 해를 찾기 위해 모든 가능한 경우의 수를 탐색하는 알고리즘
    - 해를 찾기 위해 후보군을 탐색하면서 조건을 만족하는지 검사하고, 조건을 만족하지 않으면 이전 단계로 돌아가 다른 후보군을 탐색
    - 재귀적인 방식으로 구현
- 예시
```py
def combinations(nums, k):
    result = []
    
    def backtrack(start, combination):
        if len(combination) == k:
            result.append(combination[:])
            return
        
        for i in range(start, len(nums)):
            combination.append(nums[i])
            backtrack(i + 1, combination)
            combination.pop()
    
    backtrack(0, [])
    return result

---

def permutations(nums):
    result = []
    
    def backtrack(nums, permutation):
        if len(permutation) == len(nums):
            result.append(permutation[:])
            return
        
        for num in nums:
            if num in permutation:
                continue
            permutation.append(num)
            backtrack(nums, permutation)
            permutation.pop()
    
    backtrack(nums, [])
    return result

```

