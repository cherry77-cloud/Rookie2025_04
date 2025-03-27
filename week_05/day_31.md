## 二叉树的直径

- 链：从子树中的叶子节点到当前节点的路径。把最长链的长度，作为 `dfs` 的返回值。根据这一定义，空节点的链长是 `−1`，叶子节点的链长是 `0`。
- 直径：等价于由两条（或者一条）链拼成的路径。我们枚举每个 `node`，假设直径在这里「拐弯」，也就是计算由左右两条从下面的叶子节点到 `node` 的链的节点值之和，去更新答案的最大值。
- `dfs` 返回的是链的长度，不是直径的长度。
- `dfs` 返回的是当前子树的最大链长（也可以理解为子树的高度），不包含当前节点和其父节点的这条边。

---

```cpp
class Solution {
public:
    int diameterOfBinaryTree(TreeNode* root) {
        int ans = 0;
        auto dfs = [&](this auto&& dfs, TreeNode* node) -> int {
            if (node == nullptr) {
                return -1;
            }
            int l_len = dfs(node->left) + 1;   // 左子树最大链长+1
            int r_len = dfs(node->right) + 1;  // 右子树最大链长+1
            ans = max(ans, l_len + r_len);     // 两条链拼成路径
            return max(l_len, r_len);          // 当前子树最大链长
        }; 
        dfs(root);
        return ans;
    }
};
```

---
