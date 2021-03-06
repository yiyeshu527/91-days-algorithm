# 1319.连通网络的操作次数

https://github.com/leetcode-pp/91alg-1/issues/73

## 题目描述

```
用以太网线缆将 n 台计算机连接成一个网络，计算机的编号从 0 到 n-1。线缆用 connections 表示，其中 connections[i] = [a, b] 连接了计算机 a 和 b。

网络中的任何一台计算机都可以通过网络直接或者间接访问同一个网络中其他任意一台计算机。

给你这个计算机网络的初始布线 connections，你可以拔开任意两台直连计算机之间的线缆，并用它连接一对未直连的计算机。请你计算并返回使所有计算机都连通所需的最少操作次数。如果不可能，则返回 -1 。

示例 1：



输入：n = 4, connections = [[0,1],[0,2],[1,2]]
输出：1
解释：拔下计算机 1 和 2 之间的线缆，并将它插到计算机 1 和 3 上。
示例 2：



输入：n = 6, connections = [[0,1],[0,2],[0,3],[1,2],[1,3]]
输出：2
示例 3：

输入：n = 6, connections = [[0,1],[0,2],[0,3],[1,2]]
输出：-1
解释：线缆数量不足。
示例 4：

输入：n = 5, connections = [[0,1],[0,2],[3,4],[2,3]]
输出：0


提示：

1 <= n <= 10^5
1 <= connections.length <= min(n*(n-1)/2, 10^5)
connections[i].length == 2
0 <= connections[i][0], connections[i][1] < n
connections[i][0] != connections[i][1]
没有重复的连接。
两台计算机不会通过多条线缆连接。
```

## 思路

一开始的思路：

-   先构建并查集，这样我们就能知道一共有多少个网络了，接着我们需要把网络连接起来。
-   要连接 n 个网络，需要 n - 1 根网线，那怎么知道我们有多少条多余的网线？
-   很简单，在构建网络时，也就是合并不交集的时候，如果两个节点原本就在同一集合中，那我们就多出来一根网线了。
-   最后判断一下多余网线是否不小于 n - 1 即可。

但其实没有必要记录多余的网线，只要算一下数学就好了：

-   如果连接数小于 `n - 1`，那就直接返回 -1；
-   否则，说明网线是足够连接 n 个节点的，构建完并查集之后，返回集合数量减一即可。

## 代码

TypeScript Code

```ts
function makeConnected(n: number, connections: number[][]): number {
    const cables: number = connections.length
    if (cables < n - 1) return -1

    const uf: UnionFind = new UnionFind(n)
    for (let c of connections) {
        uf.unionSet(c[0], c[1])
    }
    return uf.size() - 1
}
```

```ts
class UnionFind {
    private parents: Array<number>
    private rank: Array<number>
    private numOfSets: number

    constructor(size: number) {
        this.parents = Array(size)
            .fill(0)
            .map((_, i) => i)
        this.rank = Array(size).fill(0)
        this.numOfSets = size
    }

    size(): number {
        return this.numOfSets
    }

    findSet(x: number): number {
        if (x !== this.parents[x]) {
            this.parents[x] = this.findSet(this.parents[x])
        }
        return this.parents[x]
    }

    unionSet(x: number, y: number): void {
        const px: number = this.findSet(x)
        const py: number = this.findSet(y)
        if (px === py) return
        if (this.rank[px] > this.rank[py]) {
            this.parents[py] = px
        } else {
            this.parents[px] = py
            this.rank[px] === this.rank[py] && ++this.rank[py]
        }
        this.numOfSets--
    }
}
```
