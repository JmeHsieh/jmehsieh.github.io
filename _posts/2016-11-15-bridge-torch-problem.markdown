---
layout: post
title: 🚸 過橋
date: 2016-11-15 15:43:24 +0800
tags: algorighm, bfs, dfs, tree
---
# 🙉 🐰 🐥 🐌 ..
四人摸黑趕路，不知何故只帶一火把。大夥來到橋頭，發現路橋年久失修；目測估計，最多兩人同時過，再多橋會垮。橋下乃深淵，大夥面面相覷；彼此害怕的程度不一，過橋速度也因而不同。四人過橋分別費時 1, 2, 15, 17 分鐘。另，每兩人過橋，仍需一人將火把帶回。如此返覆，直到最後二人一同過橋，大夥便可繼續前進。

# 💡 🐒
走最快的 🙉 不耐等，直接替大家想好了辦法！<br>
快的 🙉🐰 一組，慢的 🐥🐌 一組。<br>
快組 🙉🐰 先過，🙉 將火把帶回。<br>
慢組 🐥🐌 再過，🐰 將火把帶回。<br>
快組 🙉🐰 重逢後一起過，大夥便可繼續前進 － 共費時 24 分。

# 🌳 🐇
走次快的 🐰 擔心這方法不夠快，而且也無法好好說服大家，於是打算寫 code 來解決問題。🐰 拿出他的電腦，畫 🌳 列出所有可能：<br><br>
![tree](/assets/img/crossing_bridge_tree.png)<br>

🐰 看著 🌳，發現每條從根到葉的路徑都是一個走法，每片葉子都是一個結果，因此有：<br>
= (任兩人去) * (任一人回) * (任兩人去) * (任一人回) * (任兩人去)<br>
= (左 4 取 2) * (右 2 取 1) * (左 3 取 2) * (右 3 取 1) * (左 2 取 2)<br>
= 108 片葉子<br>
= 108 種方法<br>
<br>
又，每條路徑可用一個 'moves' array 表示：<br>
moves = [ 1, 2, 1, 15, 17, 2, 1, 2]<br>
<br>
也就是：<br>
[ 🙉🐰 去 | 🙉 回 | 🐥🐌 去 | 🐰 回 | 🙉🐰 去 ]<br>
<br>
而對應的時間為：<br>
times = [ max(1, 2), 1, max(15, 17), 2, max(1, 2) ]<br>

# 💻 🐇
🐰 用一個 class 紀錄每次選擇的當下：

- left：此時橋左有哪些人
- right：此時橋右有哪些人
- moves：目前走了哪些步
- next_move：下一步是往左還往右
- total_weight：計算 moves 總共的時間

姑且將 class 命名為 Game，接著便用 BFS 或 DFS 等技巧，將所有可能列出，以 BFS 為例：

```python
def bfs(game):
    queue = [game]
    result = []
    while queue:
        game = queue.pop(0)
        if game.next_move == 'forward':
            left = list(game.left)
            for i in range(len(left) - 1):
                n1 = left[i]
                for j in range(i + 1, len(left)):
                    n2 = left[j]
                    game_ = deepcopy(game)
                    game_.forward(n1, n2)
                    if game_.next_move == 'finished':
                        result.append(game_)
                    else:
                        queue.append(game_)
        elif game.next_move == 'backward':
            for name in game.right:
                game_ = deepcopy(game)
                game_.backward(name)
                queue.append(game_)
    return result
```
<br>
以 DFS 為例：<br>

```python
def dfs(game):
    def go(r, g):
        if g.next_move == 'forward':
            left = list(g.left)
            for i in range(len(left) - 1):
                n1 = left[i]
                for j in range(i + 1, len(left)):
                    n2 = left[j]
                    g_ = deepcopy(g)
                    g_.forward(n1, n2)
                    go(r, g_)
        elif g.next_move == 'backward':
            for name in g.right:
                g_ = deepcopy(g)
                g_.backward(name)
                go(r, g_)
        else:
            r.append(g)

    result = []
    go(result, game)
    return result
```
<br>
一旦得知所有走法 (108 個 game instances)，便可計算各走法所需的時間：<br>

```python
if __name__ == '__main__':
    weights = [1, 2, 15, 17]
    names = ['🙉', '🐰', '🐥', '🐌']
    game = Game({n: w for n, w in zip(names, weights)})

    print('--- BFS ---')
    bfs_result = bfs(game)
    bfs_best = min(bfs_result, key=lambda g: g.total_weight)
    print('There are {} possibilities.'.format(len(bfs_result)))
    print('Best Move: {} --> {}'.format(bfs_best.moves_weights, bfs_best.total_weight))

    print('--- DFS ---')
    dfs_result = dfs(game)
    dfs_best = min(bfs_result, key=lambda g: g.total_weight)
    print('There are {} possibilities.'.format(len(dfs_result)))
    print('Best Move: {} --> {}'.format(dfs_best.moves_weights, dfs_best.total_weight))

    #=> --- BFS ---
    #=> There are 108 possibilities.
    #=> Best Move: [1, 2, 1, 17, 15, 2, 1, 2] --> 24
    #=> --- DFS ---
    #=> There are 108 possibilities.
    #=> Best Move: [1, 2, 1, 17, 15, 2, 1, 2] --> 24
```

# 🎉 🙌
在 108 種可能中，最快的走法為：<br>
[1, 2, 1, 15, 17, 2, 1, 2] = 24(mins)<br>
沒想到與 🙉 的建議如出一轍，這下大夥便安心的繼續前進了！<br>
<br>
([註：完整程式](https://gist.github.com/JmeHsieh/334835a778dba4b106990facbc63f156))<br>
