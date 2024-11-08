# 树中的递归

我们知道，树可以以递归的方式定义为一个节点（根节点），它包括一个值和一个指向其他节点指针的列表。递归是树的特性之一。因此，许多树问题可以通过递归的方式来解决。对于每个递归层级，我们只能关注单个节点内的问题，并通过递归调用函数来解决其子节点问题。通常，我们可以通过“自顶向下”或“自底向上”的递归来解决树问题。

## “自顶向下”的解决方案

“自顶向下”意味着在每个递归层级，我们将首先访问节点来计算一些值，并在递归调用函数时将这些值传递到子节点。所以“自顶向下”的解决方案可以被认为是一种前序遍历。

```java
1. return specific value for null node
2. update the answer if needed // answer <-- params
3. left_ans = top_down(root.left, left_params) // left_params <-- root.val, params
4. right_ans = top_down(root.right, right_params) // right_params <-- root.val, params
5. return the answer if needed // answer <-- left_ans, right_ans

// n叉树如下

1. 对于 null 节点返回一个特定值
2. 如果有需要，对当前答案 answer 进行更新 // answer <-- params
3. for each child node root.children[k]:
4.      ans[k] = top_down(root.children[k], new_params[k]) // new_params <-- root.val, params
5. 如果有需要，返回答案 answer // answer <-- all ans[k]
```

## “自底向上”的解决方案

“自底向上”是另一种递归方法。在每个递归层次上，我们首先对所有子节点递归地调用函数，然后根据返回值和根节点本身的值得到答案。这个过程可以看作是后序遍历的一种。

```java
1. return specific value for null node
2. left_ans = bottom_up(root.left) // call function recursively for left child
3. right_ans = bottom_up(root.right) // call function recursively for right child
4. return answers // answer <-- left_ans, right_ans, root.val

// n叉树如下

1.对于 null 节点返回一个特定值
2.for each child node root.children[k]:
3.    ans[k] = bottom_up(root.children[k]) // 为每个子节点递归地调用函数
4. 返回答案 answer  // answer <- root.val, all ans[k]
```

## 如何选择

当遇到树问题时，请先思考一下两个问题：你能确定一些参数，从该节点自身解决出发寻找答案吗？你可以使用这些参数和节点本身的值来决定什么应该是传递给它子节点的参数吗？如果答案都是肯定的，那么请尝试使用“自顶向下”的递归来解决此问题。

或者你可以这样思考：对于树中的任意一个节点，如果你知道它子节点的答案，你能计算出该节点的答案吗？如果答案是肯定的，那么“自底向上”的递归可能是一个不错的解决方法。
