5_Data_Structure

# 목차
* [복잡도](#복잡도)
    + [시간 복잡도](#시간-복잡도)
    + [공간 복잡도](#공간-복잡도)
* [선형 자료 구조](#선형-자료-구조)
    + [Array](#array)
    + [LinkedList](#linkedlist)
    + [Stack](#stack)
    + [Queue](#queue)
* [비선형 자료구조](#비선형-자료구조)
    + [Graph](#graph)
    + [Tree](#tree)
    + [Binary Tree (이진 트리)](#binary-tree-이진-트리)
    + [BST (Binary Search Tree)](#bst-binary-search-tree)
    + [AVL(Adelson-Velsky and Landis) Tree](#avladelson-velsky-and-landis-tree)
    + [Red Black Tree](#red-black-tree)
    + [Heap](#heap)
    + [Hash Table](#hash-table)

<br>
<br>
<br>

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

#### 규칙
1. 노드에 저장된 키는 유일
1. 왼쪽 자식 노드의 키가 부모 키보다 작다
1. 오른쪽 자식 노드의 키가 부모 키보다 크다
1. 왼쪽, 오른쪽 서브 트리도 BST이다

#### 구현
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

#### 장단점
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
- Balanced Binary Search Tree
- 탐색, 삽입, 삭제 모두 시간 복잡도가 $O(logN)$을 가짐

#### 조건
1. 모든 노드는 레드 또는 블랙이다.
1. 루트 노드의 색깔은 블랙이다.
1. 모든 리프 노드(NIL)는 블랙이다. (NIL: Null Leaf, 자료를 가지지 않고 끝을 나타내는 노드)
1. 레드 노드의 자식은 블랙이다. == 빨간색 노드가 연속으로 나올 수 없다.
1. 모든 리프노드에서 Black Depth는 같다. == 리프노드에서 루트노드까지 가는 경로에서 만나는 블랙노드의 개수는 같다

#### Balancing
항상 새로운 노드를 레드 노드로 삽입한다.  
이때 레드 노드가 연속으로 나와서 4번 조건에 위배된다.  

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FblkAJy%2FbtrpiHjqbii%2Ftj9F3pQv8oZwVgD7ffX4w1%2Fimg.png" width="500">
  
   
이를 해결하기 위해서 2가지 전략을 사용한다.   
```
N(New): 새로 삽입할 노드  
P(Parent): 부모 노드  
G(Grand Parent): 조상 노드  
U(Uncle): 삼촌 노드  
```
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbYG3yV%2FbtrpoxGRp6g%2FfBAd1VvrqdWy6QRRSKTX3k%2Fimg.png" width="500">


**Restructuring**  
1. N(New), P(Parent), G(Grand Parent)를 오름차순으로 정렬한다.
1. 셋 중 중간값을 부모로 만들고 나머지 둘은 자식으로 만든다
1. 새로 부모가 된 노드를 검은색으로 만들고 나머지를 빨간색으로 만든다

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FMASjd%2Fbtrpq6WhqWJ%2F6Vg1qcMarQEqQDk1oKGi51%2Fimg.png" width="700">


**Recoloring**
1. P(Parent), U(Uncle)를 블랙으로 바꾸고, G(Grand Parent)를 레드로 바꾼다.
    1. G가 루트 노드라면 블랙으로 변경한다.
    1. G를 레드로 변경했을 때 또다시 Double Red가 발생하면 Restructuring이나 Recoloring을 수행한다.

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FvBBus%2FbtrpjwouNiw%2FcBnlbiBxKyKUb8XRBvf4D1%2Fimg.png" width="700">
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbawsxN%2FbtrpoyMAmYw%2F3KxRGRUuFwJU5KTsmkwNJ0%2Fimg.png" width="700">


참고: https://code-lab1.tistory.com/62

### Heap
- Tree 중 배열에 기반한 `완전 이진 트리 형식(왼쪽에서 오른쪽으로 순서대로 차곡차곡 채워진 이진 트리)`이다.
- 우선순위 큐에서 사용되는 자료 구조이며, 최대값과 최소값을 찾는 데 소요되는 시간이 $O(1)$이다.
- 하지만 힙구조 유지를 위해 제거된 루트 노드를 대체한 다른 노드가 필요하다. 이 과정이 heapify 과정이며, $O(logn)$의 시간 복잡도를 가진다.
- 완전 이진 트리이기 때문에 삽입, 삭제가 가장 끝에서 일어난다.

### Hash Table
- (Key, Value)로 데이터를 저장하는 자료구조
- average case에 대하여 모든 작업의 시간 복잡도가 O(1)이다. 
    - Collision: 어설픈 해시 함수를 사용했을 때, 동일한 key 값에 복수 개의 데이터가 존재할 수 있다.  
    그렇다면 시간복잡도는 증가하게 된다.

#### Hash Function  
인덱스로 저장되는 key 값이 불규칙하다는 문제가 있는데(string, double 등), 해시테이블은 해시함수를 이용하여 key 값을 고유한 숫자 `hashcode`로 만들어 낸 뒤 이를 인덱스로 사용한다.  

대표 Hash Function  
1. Division Method: 입력값을 테이블의 크기로 나누어 계산한다. (입력값%테이블의 크기)
2. Digit Folding: 각 key의 문자열을 ASCII 코드로 바꾸고 값을 합하나 값을 사용한다.
3. Multiplication Method: h(k) = (kA mod1)*m (0<A<1, m: 2의 제곱수)
4. Univeral Hashing: 무작위로 해시함수를 선택해 hashcode를 만든다.

좋은 해시함수의 조건?
- 키의 일부분을 참조하여 해쉬 값을 만들지 않고 키 전체를 참조하여 해쉬 값을 만들어 낸다. 
- hash function를 무조건 1:1 로 만드는 것보다 `Collision 을 최소화하는 방향으로 설계`하고 발생하는 `Collision 에 대비해 어떻게 대응`할 것인가가 더 중요하다. 1:1 대응이 되도록 만드는 것이 거의 불가능하기도 하지만 그런 hash function를 만들어봤자 그건 array 와 다를바 없고 메모리를 너무 차지하게 된다.

#### Collision에 대응하기

1. 분리 연결법
    - 동일한 인덱스에 복수의 데이터를 저장하는 방식이다.
    - **장점:** 해시 테이블의 확장이 필요없고, 간단히 구현가능.
    - **단점:** 데이터의 수가 많아지면 동일한 index에 많은 데이터가 연결되며 효율성이 떨어짐.  
    <img src="https://velog.velcdn.com/images%2Flky9303%2Fpost%2F2cb1d3b7-9962-4a3f-b88b-f0a47233bcf4%2Fimage.png" width="400">

2. 개방 주소법  
추가적인 구조를 사용하지 않고, 해시 테이블의 공간을 활용하는 방식이다.
    1. **Linear Probing**: Collision이 발생할 때, index로부터 고정 폭 만큼 씩 이동하여 차례대로 비어있는 공간에 데이터를 저장한다.
    예) index 1에서 충돌 > index 2에 데이터 저장
    2. **Quadratic Probing**: 해시의 저장순서 폭을 제곱으로 저장하는 방식이다. 예) index 1에서 첫번째 충돌 > 1 + $1^2$ = 2에 저장
    index 1에서 두번째 충돌 > 1 + $2^2$ = 5에 저장
    index 1에서 세번째 충돌 > 1 + $3^2$ = 10에 저장
    3. **Double Hashing Probing**: 해시된 값을 한번 더 해싱하여 해시의 규칙성을 없애버리는 방식이다. → 다른 공간에 값이 골고루 저장될 확률이 높아진다.
    최초 Hashcode를 얻기 위해서 해싱 > 충돌이 났을 경우 두 번째 Hashcode를 탐사 이동 폭을 얻기 위해서 사용  
    <img src="https://velog.velcdn.com/images%2Flky9303%2Fpost%2Fbf2bd58c-5efd-46f8-b7c7-5cf71c5ca95b%2Fimage.png" width="400">