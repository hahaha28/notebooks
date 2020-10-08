# BFS算法

> 转载自[这里](https://labuladong.gitbook.io/algo/di-ling-zhang-bi-du-xi-lie/bfs-kuang-jia)

## 基本思想

从一个点开始向四周扩散，适用于

1. 求最短路径长度
2. 每条路径情况类似或相同

```java
// 计算从起点start到终点target的最终距离
int BFS(Node start, Node target){
    Queue<Node> queue = new LinkedList<>();  // 队列
    Set<Node> visited = new Set<>();  // 避免走回头路
    
    queue.offer(start);  // 起点加入队列
    visited.add(start);
    int step = 0;  // 记录扩散的步数
    
    while(!queue.isEmpty()){
        int size = queue.size(); //一定要用这里获取到的大小
        // 将队列中每一个点向四周扩散
        for(int i=0;i < size;++i){
            Node cur = queue.poll();
            // 判断是否到终点
            if(cur.equals(target)){
                return step;
            }
            // 将相邻节点加入队列
            for(Node x : cur.相邻节点){
                // 不走回头路
                if(!visited.contains(x)){
                    queue.offer(x);
                    visited.add(x);
                }
            }
        }
        // 步数更新
    	step++;
    }
    return step;
}
```

