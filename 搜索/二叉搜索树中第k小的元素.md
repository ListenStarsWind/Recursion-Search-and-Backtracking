# [二叉搜索树中第 K 小的元素](https://leetcode.cn/problems/kth-smallest-element-in-a-bst/)

![image-20250418120930053](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250418120930139.png)

## 算法原理

我们知道二叉搜索树的中序遍历是升序的, 所以我们直接用中序遍历的方式去找第`k`小的数,  引入一个全局变量, 存储`k`, 每到一个具体的节点, 就减减`k`, 当`k`变成零, 那就是我们要找的数

![image-20250418122504779](https://md-wind.oss-cn-nanjing.aliyuncs.com/md/20250418122504834.png)

## 代码编写

```cpp
class Solution {
    int t;
    int bfs(TreeNode* root)
    {
        if(root == nullptr) return -1;

        int left = bfs(root->left);
        if(left != -1) return left;

        --t;
        if(t == 0) return root->val;

        int right = bfs(root->right);
        if(right != -1) return right;

        return -1;
    }

public:
    int kthSmallest(TreeNode* root, int k) {
        t = k;
        return bfs(root);
    }
};
```

# 完