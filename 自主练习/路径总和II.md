# [路径总和 II](https://leetcode.cn/problems/path-sum-ii/)

![image-20250502122351818](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250502122351883.png)

## 算法原理

维护一个路径数组, 定义`dfs(int sum)`, 对数进行深搜, 搜索到叶节点, 如果当前的路径和就是目标和, 就把路径数组压入答案., 其它节点就正常的把自己的节点值加到自己的`sum`上, 然后交给自己的左右子树.

## 代码编写

```cpp
class Solution {
    int targetSum;
    vector<int> path;
    vector<vector<int>> result;

    void dfs(TreeNode* root, int sum = 0)
    {
        if(root == nullptr) return;

        sum += root->val;
        path.emplace_back(root->val);

        if(!root->left && !root->right)
        {
            if(sum == targetSum)
            {
                result.emplace_back(path);
            }
        }
        else
        {
            dfs(root->left, sum);
            dfs(root->right, sum);    
        }
        
        path.pop_back();
    }

public:
    vector<vector<int>> pathSum(TreeNode* root, int _targetSum) {
        targetSum = _targetSum;
        dfs(root);
        return result;
    }
};
```

# 完