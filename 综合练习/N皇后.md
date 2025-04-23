# [N 皇后](https://leetcode.cn/problems/n-queens/)

![image-20250423105008987](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250423105009208.png)

## 题目解析

给你一个大小为`n*n`的棋盘, 请在其中放入`n`个皇后(其实就是每行(或者你也可以认为是每列)放一个皇后), 使得皇后之间不能相互攻击.  皇后的攻击范围有四种, 分别是同行攻击(处于同一行的皇后相互攻击), 同列攻击(处于同一列的皇后相互攻击), 对角线攻击(包括两种, 第一种是斜率为正的对角线, 我们叫做主斜线, 第二种是斜率为负的对角线),

![image-20250423110204215](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250423110204297.png)

你将返回一个`vector<vector<string>>`, 其中的每个`vector<string>`都代表着一种可能得方案, 我们约定, 用字符`'Q'`代表皇后, 字符`'.'`代表空格

## 算法原理

还是老样子, 我们去画决策树, 为了让图不会太大, 我们选择`N = 3`的情况

首先我们先规定一下坐标系, 坐标系长这样
![image-20250423111008731](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250423111008787.png)

首先我们有两种思路, 一种是一个格子一个格子地放, 先放`[0,0]`, 再考虑能不能放`[0,1]`...... 但很明显, 一行最多放一个皇后, 所以我们可以一个一个行的来放, 我们从一行中选择一个位置, 放上`Q`, 这行的其它格子都放`.`, 这样我们其实就不用再考虑同行攻击了.

![image-20250423113208253](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250423113208415.png)

其实N皇后的决策树还是很好画的, N皇后的难点就是怎么把决策树转换成代码

我们看到, 对于能攻击到的方案, 我们需要将其进行剪枝, 行攻击不用考虑, 下面我们的重点就是想想怎么用代码的形式确定它会攻击, 毕竟决策树我们是用眼看出来的, 机器又做不到. 我们先看列攻击, 这个其实比较简单, 我们直接准备一个全局的布尔数组`cols`, 其中`cols[j]`表示第`j`列是否有皇后, 有, 那就剪枝, 没有就看其他攻击方式

重点中的重点, 那就是对于侧线攻击的判断, 首先我们看一下主侧线, 我们看到, 对于同一个主侧线, 其实都是下标同加同减. 

![image-20250423114141608](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250423114141668.png)

比如我们的皇后坐标是`[1,0]`, 再往右上角走, 那就是`[1,2]`这意味着什么呢, 那就是行数减去列数是固定的, 或者我们还可以从数学的角度来看, 上图中的每条主侧线, 其函数表达式都类似于`y = x + b`, 那么不就有`y-x`为固定值吗, 这样的话, 比如我在`[1,0]`放了皇后, 那就对`1`这个`b`标记一下, 比如我们可以拿一个数组来存储各种`b`的状态, 是`true`那就说明能攻击到, 另外, 我们还要注意一下, 这个`b`可能是负数, 数组很显然没有负数下标, 所以为此我们需要对`b`加一个偏移量, 比如我们拿`[0,2]`举例, `0-2`等于`-2`, 哪有`-2`下标, 所以我们可以将`-2+n`得到`1`, 这样的话, 我们的`vector<bool> dig1`,规模应该是`n*2`

 对于副侧线, 我们也可以用相同的方法, 副侧线其实就是形如`y = -x + b`的那些函数图像, 而且此时的`b`不会为负数

![image-20250423120823515](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250423120823552.png)

因此我们不需要再加偏移量了, `vector<bool> dig2`的规模也是`n*2`

然后其实就没有什么好说的了, 代码逻辑已经说明白了, 直接写就行

## 代码编写

```cpp
class Solution {
    int n;
    vector<bool> cols;
    vector<bool> dig1;
    vector<bool> dig2;
    vector<string> path;
    vector<vector<string>> result;

    void dfs(int row = 0)
    {
        if(row == n)
        {
            result.emplace_back(path);
            return;
        }

        for(int col = 0; col < n; ++col)
        {
            if(cols[col]) continue;
            if(dig1[row-col+n]) continue;
            if(dig2[row+col]) continue;

            
            cols[col] = true;
            dig1[row-col+n] = true;
            dig2[row+col] = true;
            
            path.emplace_back(string(n, '.'));
            path[row][col] = 'Q';
            dfs(row+1);
            path.pop_back();

            cols[col] = false;
            dig1[row-col+n] = false;
            dig2[row+col] = false;
        }
    }

public:
    vector<vector<string>> solveNQueens(int _n) {
        n = _n;
        cols.resize(n);
        dig1.resize(2*n);
        dig2.resize(2*n);
        path.reserve(n);
        dfs();
        return result;
    }
};
```

# 完