# 二叉树遍历

二叉树的遍历有前序遍历、中序遍历和后序遍历三种，用程序实现时，又分别有递归和迭代两种解法



## 递归

递归是遍历二叉树最简单无脑的解法了，前序中序后序只需要控制节点的放置顺序即可

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
function preorderTraversal(root) {
    if (!root) return []
    let result = [root.val]
    result = result.concat(preorderTraversal(root.left))
    result = result.concat(preorderTraversal(root.right))
    return result
}

/**
 * @param {TreeNode} root
 * @return {number[]}
 */
function inorderTraversal(root) {
    if (!root) return []
    let result = inorderTraversal(root.left)
    result.push(root.val)
    result = result.concat(inorderTraversal(root.right))
    return result
}

/**
 * @param {TreeNode} root
 * @return {number[]}
 */
function postorderTraversal(root) {
    if (!root) return []
    let result = inorderTraversal(root.left)
    result = result.concat(inorderTraversal(root.right))
    result.push(root.val)
    return result
}
```



## 迭代

### 前序遍历

需要借助额外的栈来实现

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
function preorderTraversal(root) {
    let stack = [root]
    let result = []
    let item
    while (stack.length) {
        item = stack.pop()
        if (item) {
            result.push(item.val)
            stack.push(item.right)
            stack.push(item.left)
        }
    }
    return result
}
```



### 中序遍历

中序遍历相对前序遍历会稍微麻烦些，需要先把全部左节点放到栈中，再判断有没有右节点

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    let stack = []
    let result = []
    let item
    while (root || stack.length) {
        if (root) {
            stack.push(root)
            root = root.left
        } else {
            item = stack.pop()
            result.push(item.val)
            root = item.right
        }
    }
    return result
}
```



### 后序遍历

后序遍历可以通过仿照前序遍历后再翻转数组的方式来实现

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var postorderTraversal = function(root) {
    let stack = [root]
    let result = []
    let item
    while (stack.length) {
        item = stack.pop()
		if (item) {
            result.push(item.val)
            stack.push(item.left)
            stack.push(item.right)
        }
    }
    return result.reverse()
};
```





## 层序遍历

扫描二叉树的每一层，将每一层的数据合成一个二维数组

迭代：

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var levelOrder = function(root) {
    if (!root) return []
 	let nextLevel = [root]
    let curLevel = []
	let item
    let result = []
    let subResult
    while (nextLevel.length) {
        curLevel = [nextLevel...]
       	nextLevel = []
        subResult = []
        while(curLevel.length) {
            item = curLevel.shift()
			subResult.push(item.val)
            if (item.left) {
                nextLevel.push(item.left)
            }
            if (item.right) {
                nextLevel.push(item.right)
            }
        }
        result.push(subResult)
    }
    return result
}
```



## 构造二叉树

### 利用中序和后序组件构建二叉树

采用递归的方式实现，先构建右子树，再构建左子树

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {number[]} inorder
 * @param {number[]} postorder
 * @return {TreeNode}
 */
var buildTree = function(inorder, postorder) {
    return helper(inorder, postorder, 0, inorder.length-1)
};

function helper(inorder, postorder, start, end) {
    if (start > end) return null
    
    let rootVal = postorder.pop()
    let index = inorder.indexOf(rootVal)
    
    let root = new TreeNode(rootVal)
    root.right = helper(inorder, postorder, index+1, end)
    root.left = helper(inorder, postorder, start, index-1)
    
    return root
}
```



### 利用前序和中序组件构建二叉树

前序+中序与上面中序和后序构建二叉树的方法类似，