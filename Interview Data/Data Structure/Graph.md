# Graph

## 1. 용어 정리

### 1.1 Undirected Graph와 Directed Graph

말 그대로 정점과 간선의 연결관계에 있어서 **방향성**이 없는 그래프를 Undirected Graph라 하고, 간선에 방향성이 포함되어 있는 그래프를 Directed Graph라고 합니다.

### 1.2 Degree

Undirected Graph에서 각 **정점(Vertex)에 연결된 Edge의 개수**를 Degree라고 합니다. Directed Graph에서는 간선에 방향성이 존재하기 때문에 Degree가 두 개로 나뉘게 됩니다. 각 정점으로부터 나가는 간선의 개수를 Outdegree라 하고, 들어오는 간선의 개수를 Indegree라 합니다

### 1.3 가중치 그래프(Weight Graph)와 부분 그래프(Sub Graph)

가중치 그래프란 **간선에 가중치 정보를 두어서 구성한 그래프**를 말합니다. 반대의 개념인 비가중치 그래프는 모든 간선의 가중치가 동일한 그래프를 말합니다. 부분 집합과 유사한 개념으로 부분 그래프라는 것이 있습니다. 부분 그래프는 **본래의 그래프의 일부 정점 및 간선으로 이루어진 그래프**를 말합니다.

## 2. 그래프를 구현하는 두 방법

### 2.1 인접 행렬 (Adjacent Matrix): 정방 행렬을 사용하는 방법

그래프의 연결 관계를 이차원 배열로 나타내는 방식입니다. 인접 행렬을 adj[][]라고 한다면 adj[i][j]에 대해 노드 i에서 노드 j로 가는 간선이 있으면 1, 아니면 0입니다. 해당하는 위치의 Value 값을 통해서 Vertex 간의 연결 관계를 O(1)으로 파악할 수 있습니다. Edge 개수와는 무관하게 V^2의 Space Complexity를 갖습니다. Dense Graph(Node 수보다 Edge 수가 큰 그래프)를 표현할 때 적절한 방법입니다.

### 2.2 인접 리스트 (Adjacent List): 연결 리스트를 사용하는 방법

그래프의 연결 관계르 1차원 배열로 나타내는 방식입니다. adj[i]는 노드 i에 연결된 노드들을 원소로 갖습니다. Vertex의 Adjacent List를 확인해봐야 하므로 Vertex간 연결되어있는지 확인하는데 오래 걸립니다. Space Complexity는 O(E + V)입니다. Sparse Graph(Node 수보다 Edge수가 적은 그래프)를 표현하는데 적당한 방법입니다.

## 3. 그래프 탐색

그래프는 정점의 구성 뿐만 아니라 간선의 연결에도 규칙이 존재하지 않기 때문에 탐색이 복잡합니다. 따라서 그래프의 모든 정점을 탐색하기 위한 방법은 다음의 두 가지 알고리즘을 기반으로 합니다.

### 3.1 깊이 우선 탐색 (Depth First Search: DFS)

그래프 상에 존재하는 임의의 한 정점으로부터 연결되어 있는 한 정점으로만 나아간다라는 방법을 우선으로 탐색합니다. 연결할 수 있는 정점이 있을 때까지 계속 연결하다가 더이상 연결되는 정점이 없다면 바로 그 전단계의 정점으로 돌아거서 연결할 수 있는 정점이 있는지 살펴봐야 합니다. 갔던 길을 되돌아 오는 상황이 존재하는 미로찾기처럼 구성하면 됩니다. 자료구조 Stack을 사용합니다.

### 3.2 너비 우선 탐색 (Breadth First Search: BFS)

그래프 상에 존재하는 임의의 한 정점으로부터 연결되어 있느 모든 정점으로 나아갑니다. Tree에서의 Level Order Traversal 형식으로 진행됩니다. BFS에서는 자료구조 Queue를 사용합니다. 연락을 취할 정점의 순서를 기록하기 위한 것입니다.

## 4. Minimum Spanning Tree

그래프의 Spanning Tree 중 **Edge Weight의 합이 최소인 Spanning Tree**를 말합니다. 여기서 말하는 Spanning Tree란 그래프 G의 모든 Vertex가 Cycle이 없이 연결된 형태를 말합니다.

### 4.1 Kruskal Algorithm

초기화 작업으로 Edge 없이 Vertex 들만으로 그래프를 구성합니다. 그리고 Weight가 제일 작은 Edge부터 검토합니다. 그러기 위해서 Edge Set을 Non-decreasing으로 Sorting해야 합니다. 그리고 가장 작은 Weight에 해당하는 Edge를 추가하는데 추가할 때 그래프에 Cycle이 생기지 않는 경우에만 추가합니다. Spanning Tree가 완성되면 모든 Vertex들이 연결된 상태로 종료가 되고 완성될 수 없는 그래프에 대해서는 모든 Edge에 대해 판단이 이루어지면 종료됩니다.

#### 4.1.1 어떻게 Cycle 생성 여부를 판단하는가?

Graph의 각 Vertex에 set-id라는 것을 추가적으로 부여합니다. 그리고 초기화 과정에서 모두 1~n까지의 값으로 각각의 Vertex 들을 초기화 합니다. 여기서 0은 어떠한 edge와도 연결되지 않았음을 의미하게 됩니다. 그리고 연결할 때마다 set-id를 하나로 통일시키는데, 값이 동일한 set-id 개수가 많은 set-id 값으로 통일시킵니다.

#### 4.1.2 Time Complexity

1. Edge의 Weight를 기준으로 Sorting: O(E log E)
2. Cycle 생성 여부를 검사하고 set-id를 통일: O(E + V log V)
3. 전체 시간 복잡도: O(E log E)

### 4.2 Prim Algorithm

초기화 과정에서 한 개의 Vertex로 이루어진 초기 그래프 A를 구성합니다. 그리고나서 그래프 A 내부에 있는 Vertex로부터 외부에 있는 Vertex 사이의 Edge를 연결하는데 그 중 가장 작은 Weight의 Edge를 통해 연결되는 Vertex를 추가합니다. 어떤 Vertex건 간에 상관없이 Edge의 Weight를 기준으로 연결하는 것입니다. 이렇게 연결된 Vertex는 그래프 A에 포함됩니다. 위 과정을 반복하고 모든 Vertex들이 연결되면 종료합니다.

#### 4.2.1 Time Complexity

1. 전체 시간 복잡도: O(E log V)

## 5. 출처

- [Interview_Question_for_Beginner - JaeYeopHan](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/DataStructure#array-vs-linked-list)
- [[그래프] 인접 행렬과 인접 리스트 - 주니어 개발자의 대나무숲](https://sarah950716.tistory.com/12)
