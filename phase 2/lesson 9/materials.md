### Представление графа

```cpp
// Список смежности (рекомендуется):
vector<vector<int>> adj(n);  // adj[v] = список соседей v
adj[u].push_back(v);

// Матрица смежности:
vector<vector<int>> mat(n, vector<int>(n, 0));
mat[u][v] = 1;
```

### BFS — O(V + E)

```cpp
vector<int> bfs(const vector<vector<int>>& adj, int src, int n) {
    vector<int> dist(n, -1);
    queue<int> q;
    dist[src] = 0; q.push(src);
    while (!q.empty()) {
        int v = q.front(); q.pop();
        for (int u : adj[v])
            if (dist[u] == -1) { dist[u] = dist[v] + 1; q.push(u); }
    }
    return dist;
}
```

### DFS — O(V + E)

```cpp
void dfs(const vector<vector<int>>& adj, int v, vector<bool>& vis) {
    vis[v] = true;
    for (int u : adj[v]) if (!vis[u]) dfs(adj, u, vis);
}
```

### Топологическая сортировка (алгоритм Кана)

```cpp
vector<int> topsort(int n, const vector<vector<int>>& adj) {
    vector<int> indegree(n, 0);
    for (int v = 0; v < n; v++) for (int u : adj[v]) indegree[u]++;
    queue<int> q;
    for (int v = 0; v < n; v++) if (indegree[v] == 0) q.push(v);
    vector<int> order;
    while (!q.empty()) {
        int v = q.front(); q.pop(); order.push_back(v);
        for (int u : adj[v]) if (--indegree[u] == 0) q.push(u);
    }
    return order.size() == n ? order : vector<int>{};  // пусто если цикл
}
```

### Дейкстра — O((V+E) log V)

```cpp
vector<int> dijkstra(int n, const vector<vector<pair<int,int>>>& adj, int src) {
    vector<int> dist(n, INT_MAX);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    dist[src] = 0; pq.push({0, src});
    while (!pq.empty()) {
        auto [d, v] = pq.top(); pq.pop();
        if (d > dist[v]) continue;
        for (auto [w, u] : adj[v])
            if (dist[v] + w < dist[u]) { dist[u] = dist[v] + w; pq.push({dist[u], u}); }
    }
    return dist;
}
```

### DSU (Disjoint Set Union)

```cpp
struct DSU {
    vector<int> parent, rank;
    DSU(int n) : parent(n), rank(n, 0) { iota(parent.begin(), parent.end(), 0); }
    int find(int x) { return parent[x] == x ? x : parent[x] = find(parent[x]); }
    bool unite(int x, int y) {
        x = find(x); y = find(y); if (x == y) return false;
        if (rank[x] < rank[y]) swap(x, y);
        parent[y] = x; if (rank[x] == rank[y]) rank[x]++;
        return true;
    }
};
```
