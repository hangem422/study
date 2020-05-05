# 그래프

## 무지향성 그래프

```javascript
class UndirectedGraph {
  constructor() {
    this.edges = {};
  }

  addVertex(vertex) {
    this.edges[vertex] = {};
  }

  addEdge(vertex1, vertex2, weight = 0) {
    if (this.edges[vertex1] && this.edges[vertex2]) {
      this.edges[vertex1][vertex2] = weight;
      this.edges[vertex2][vertex1] = weight;
    }
  }

  removeEdge(vertex1, vertex2) {
    if (this.edges[vertex1] && this.edges[vertex1][vertex2] !== undefined) {
      delete this.edges[vertex1][vertex2];
    }
    if (this.edges[vertex2] && this.edges[vertex2][vertex1] !== undefined) {
      delete this.edges[vertex2][vertex1];
    }
  }

  removeVertex(vertex) {
    for (const adjacentVertex in this.edges[vertex]) {
      this.removeEdge(adjacentVertex, vertex);
    }
    delete this.edges[vertex];
  }
}
```

## 지향성 그래프

```javascript
class DirectedGraph {
  constructor() {
    this.edges = {};
  }

  addVertex(vertex) {
    this.edges[vertex] = {};
  }

  addAdge(orignVertex, destVertex, weight = 0) {
    this.edges[orignVertex][destVertex] = weight;
  }

  removeEdge(orignVertex, destVertex) {
    if (
      this.edges[orignVertex] &&
      this.edges[orignVertex][destVertex] !== undefined
    ) {
      delete this.edges[orignVertex][destVertex];
    }
  }

  removeVertex(vertex) {
    for (const adjacentVertex in this.edges) {
      this.removeEdge(adjacentVertex, vertex);
    }
    delete this.edges[vertex];
  }

  // 너비 우선 검색
  traverseBFS(vertex, fn) {
    const queue = [vertex];
    const visited = {};

    while (queue.length > 0) {
      const cur = queue.shift();
      if (!visited[cur]) {
        visited[cur] = true;
        fn(cur);
        for (const adjacentVertex in this.edges[cur]) {
          queue.push(adjacentVertex);
        }
      }
    }
  }

  // 깊이 우선 검색
  traverseDFS(vertex, fn, visited = {}) {
    visited[vertex] = true;
    fn(vertex);
    for (const adjacentVertex in this.edges[vertex]) {
      if (!visited[adjacentVertex]) {
        this.traverseDFS(adjacentVertex, visited, fn);
      }
    }
  }

  // 가중치가 있을 때 최단 경로
  dijkstra(source) {
    const notVisited = {};
    const dist = {};

    const isEmpty = () => {
      return Object.keys(notVisited).length === 0;
    };

    const extractMin = () => {
      let minimumDist = Infinity;
      let nodeWithMinimumDist = null;

      for (const node in notVisited) {
        if (dist[node] <= minimumDist) {
          minimumDist = dist[node];
          nodeWithMinimumDist = node;
        }
      }
      return nodeWithMinimumDist;
    };

    for (const vertex in this.edges) {
      dist[vertex] = Infinity;
      notVisited[vertex] = true;
    }
    dist[source] = 0;

    while (!isEmpty()) {
      const u = extractMin();
      delete notVisited[u];

      for (const neighbor in this.edges[u]) {
        const alt = dist[u] + this.edges[u][neighbor];
        if (alt < dist[neighbor]) dist[neighbor] = alt;
      }
    }
    return dist;
  }

  // 위상 정렬
  topologicalSort() {
    const visited = {};
    const stack = [];

    const topologicalSortUtil = (vertex) => {
      visited[vertex] = true;
      for (const item in this.edges[vertex]) {
        if (!visited[item]) {
          topologicalSortUtil(item, visited, stack);
        }
      }
      stack.unshift(vertex);
    };

    for (const item in this.edges) {
      if (!visited[item]) {
        topologicalSortUtil(item, visited, stack);
      }
    }
    return stack;
  }
}
```
