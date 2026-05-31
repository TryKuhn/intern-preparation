0–10 мин. BFS vs DFS для кратчайшего пути.
BFS обходит уровень за уровнем. Первый раз когда достигли вершины — кратчайший путь (по рёбрам). DFS может идти длинным путём. В BFS каждая вершина достигается по минимальному числу рёбер от источника.

10–40 мин. BFS и DFS реализации.
```cpp
// BFS — кратчайший путь в невзвешенном:
vector<int> bfs_dist(vector<vector<int>>& adj, int src, int n) {
    vector<int> dist(n, -1);
    queue<int> q; dist[src] = 0; q.push(src);
    while (!q.empty()) {
        int v = q.front(); q.pop();
        for (int u : adj[v]) {
            if (dist[u] == -1) { dist[u] = dist[v] + 1; q.push(u); }
        }
    }
    return dist;
}

// DFS — рекурсивный:
void dfs(vector<vector<int>>& adj, int v, vector<bool>& visited) {
    visited[v] = true;
    for (int u : adj[v])
        if (!visited[u]) dfs(adj, u, visited);
}
```

40–65 мин. Топсорт, Дейкстра, DSU.
Топсорт (Kahn): BFS с in-degree. Если остались вершины → цикл.
Дейкстра: priority_queue с (dist, vertex), жадно берём минимальное.
DSU: union-find с path compression + rank.

65–82 мин. Задачи на связность (LC 200). Выдача ДЗ.
