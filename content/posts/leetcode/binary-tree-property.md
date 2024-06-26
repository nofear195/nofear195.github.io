---
title: "[ Leetcode ] Binary Tree - Property"
date: 2024-04-10
lastmod: 2024-05-14
draft: false
authors: ["nofear195"]
description: ""
categories: ["Leetcode"]
tags: ["Leetcode","Python"]
lightgallery: true

---


<!--more-->

## Base Questions

### Symmetric Tree

- [101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree)

  - postorder traversal
  - Recursive
    1. 參數與返回值
       - 參數：跟節點的兩個子樹 (left, right)
       - 返回值：是否對稱 bool
    2. 終止條件
       - 左右為空：return True
       - 左為空，右不為空： return False
       - 左不為空，右為空： return False
       - 左右皆不為空
          - 左有節點內容不同：return False
          - 左右節點內容相同：進入單層邏輯
    3. 單層邏輯
       - 左右節點內容相同，遞迴找在下一層
       - 比較外側是否對稱：left.left, right.right
       - 比較內側是否對稱：left.right, right.left
       - 若內外都對稱則回傳 True，有一側不相同則回傳 False

  ```Python
  def isSymmetric(root: Optional[TreeNode]) -> bool:
    
    def compare(left: Optional[TreeNoed], right: Optional[TreeNode]) -> bool:
      if left is None and right is None: return True
      elif left is None and right: return False
      elif left and right is None: return False
      elif left and right and left.val != right.val; return False

      outside = compare(left.left, right.right)
      inside = compare(left.right, right.left)
      isSame = outside and inside
      return isSame
    
    if root is None : return False
    return compare(root.left, root.right)
  ```

  - Iterative
  - 使用 Queue, or Stack 存放要比較的元素
  - 依照要比對的元素倆倆照順序放入容器，在一起拿出來比較
  - not a levelorder traversal

  ```Python
  from collections import deque
  def isSymmetric(root: Optional[TreeNode]) -> bool:
    if root is None: return False
    que = deque()
    que.append(root.left)
    que.append(root.right)

    while que:
      left = que.popleft()
      right = que.popleft()
      # 左右為空 -> 對稱
      if left is None and right is None: continue
      # 左右任一不為空 or 都不為空但值不同 -> 不對稱 False
      if left or right or left.val != right.val:
        return False
      
      que.append(left.left)
      que.append(right.right)
      que.append(left.right)
      que.append(right.left)
    return True
  ```

- Related
  - [100. Same Tree](https://leetcode.com/problems/same-tree)
  - [572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree)

### Complete Tree

- [222. Count Complete Tree Nodes](https://leetcode.com/problems/count-complete-tree-nodes)

  - Complete Tree : 除了最後一層節點不一定有值外，其餘層數一定填滿，最後一層若沒填滿，以左邊節點開始填

  - postorder traversal
  - Recursive
    1. 參數與返回值
       - 參數：當前節點
       - 返回值：以當前節點為根的節點數量
    2. 終止條件
       - 若節點為空，回傳高度為 0
    3. 單層邏輯
       - 計算左子樹節點數量
       - 計算右子樹節點數量
       - 當前節點 + 左右子樹的節點數量總和

  ```Python
  def countNodes(root: Optional[TreeNode]) -> int:
    
    def getSum(current: Optional[TreeNoed]) -> int:
      if node is None: return 0

      leftSum = getSum(node.left) # 左
      righSum = getSum(node.right) # 右
      return 1 + min(leftDepth + rightDepth) # 中
    
    return getSum(root)
  ```

  - levelorder traversal

  ```Python
  from collections import deque
  def countNodes(root: Optional[TreeNode]) -> int:
    if root in None: return  0

    Sum = 0
    que = deque([root])
    while que:
      size = len(que)
      for _ in range(size):
        node = que.popleft()
        Sum += 1
        if node.left: que.append(node.left) # 左
        if node.right: que.append(node.right) # 右
    return Sum
  ```

### Balanced Tree

- [110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree)

  - height-balanced binary tree : 樹的子樹高度差不可大於１
  - postorder traversal
  - 以計算子樹高度來判斷是否為平衡樹，若高度落差大於１，則用`-1`表示非平衡
  - Recursive
    1. 參數與返回值
       - 參數：當前節點
       - 返回值：以當前節點為根的節點的樹高度，用`-1`作為標記表示不為平衡樹
    2. 終止條件
       - 若節點為空，回傳高度為 0
    3. 單層邏輯
       - 計算左右子樹的高度落差，若差值小於等於１，則返回當前高度，否則返回 -1

  ```Python
  def isBalanced(self, root: Optional[TreeNode]) -> bool:
    
    def getHeight(current: Optional[TreeNoed]) -> int:
      if current is None: return 0

      leftHeight = getHeight(current.left)
      if leftHeight == -1: return -1
      rightHeight = getHeight(current.right)
      if rightHeight == -1: return -1

      diff = abs(leftHeight - rightHeight)
      if dirr > 1:
        return -1
      else:
        return 1 + max(leftHeight, rightHeight)

    return not (getHeight(root) == -1)
  ```

### Tree Paths

- [257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths)

  - preorder traversal
  - Recursive
    1. 參數與返回值
       - 參數：當前節點、路徑、存放路徑結果
       - 返回值：None
    2. 終止條件
       - 若節點為葉節點，加入完整路徑 (root -> leaf)
    3. 單層邏輯
       - 將當前節點加入到路徑中
       - 使用 `回溯` 加入到路徑參數，遍歷左右子樹

  ```Python
  def binaryTreePaths(root: Optional[TreeNode]) -> List[str]:

    def traversal(current: Optional[TreeNode], path: str, result: List[str]) -> None:
      
      path += str(current.val) # 中 (當前節點)

      if current.left is None and current.right is None:
        result.append(path) # 加入完整路徑
        return
      # 回朔 path + "->" (只在下一層加入，回到當前層後取消)
      if current.left: traversal(current.left, path + "->", result) # 左 
      if current.right: traversal(current.right, path + "->", result) # 右
    
    result = []
    traversal(root, "", result)
    return result
  ```

  - levelorder traversal
  - Iterative
    - 使用兩個 stack，一個遍歷時需保存的節點順序、一個保存遍歷路徑

  ```Python
  def binaryTreePaths(root: Optional[TreeNode]) -> List[str]:
    if root is None: return []

    result = []
    treeStack = [root]
    pathStack = [str(root.val)]

    while treeStack:
      node = treeStack.pop() # 中
      path = pathStack.pop()
      if node.left is None and node.right is None:
        result.append(path)

      if node.right: # 右
        treeStack.append(node.right)
        pathStack.append(path + "->" + str(node.right.val))

      if node.left: # 左
        treeStack.append(node.left)
        pathStack.append(path + "->" + str(node.left.val))

    return result
  ```

### Sum of Left Leaves

- [404. Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves)

  - postorder traversal
  - Recursive
    1. 參數與返回值
       - 參數：根節點，與題目輸入參數相同，直接用題目做遞迴
       - 返回值：左葉節點總和
    2. 終止條件 *5
       - 只有通過父節點才能決定子節點是否為左葉節點，而非葉節點本身
       - 若當前節點為葉節點，其左節點必為０
    3. 單層邏輯
       - 遇到左葉節點時紀錄數值
       - 遞迴求左右子樹的左葉節點總和
       - 加總所有總和

  ```Python
  def sumOfLeftLeaves(root: Optional[TreeNode]) -> int:
    if root is None: return 0
    if root.left is None and root.right is None: return 0

    leftValue = self.sumOfLeftLeaves(root.left) # 左
    # 判定是否有左節點且左節點是否為葉節點
    if root.left and root.left.left is None and root.left.right is None:
      leftValue = root.left.val

    rightValue = self.sumOfLeftLeaves(root.right) # 右

    Sum = leftValue + rightValue # 中
    return Sum
  ```

  - preorder traversal
  - Iterative

  ```Python
  def sumOfLeftLeaves(root: Optional[TreeNode]) -> int:
    if root is None : return 0
    
    result = 0
    stack = [root]
    while stack:
      node = stack[-1]
      if node:
        stack.pop()
        if node.right: stack.append(node.right) # 右
        if node.left: stack.append(node.left) # 左
        stack.append(node) # 前
        stack.append(None)
      else:
        stack.pop()
        node = stack.pop()
        if node.left and node.left.left is None and node.left.right is None:
          result += node.left.val
    return result
  ```

### Find Bottom Left Tree Value

- [513. Find Bottom Left Tree Value](https://leetcode.com/problems/find-bottom-left-tree-value)

  - 與 traversal order 較無關，優先找左子樹的葉節點以更新結果
  - Recursive
    1. 參數與返回值
       - 參數：根節點、當前深度
       - 返回值：無
       - 全域變數：最大深度、最大深度節點的值
    2. 終止條件
       - 當前節點為葉節點，更新全域變數的深度、節點值
    3. 單層邏輯
       - 遞迴求左右子樹

  ```Python
  def findBottomLeftValue(root: Optional[TreeNode]) -> int:
    self.maxDepth = float("-inf")
    self.result = None

    def traversal(root, depth) -> None:
      if root.left is None and root.right is None:
        if depth > self.maxDepth:
          self.maxDepth = depth
          self.result = root.val
      return
    
      if root.left : traversal(root.left, depth + 1) # 優先找左子樹
      if root.right : traversal(root.right, depth + 1)
    
    traversal(root, 1)
    return self.result
  ```

  - level-order traversal
  - Iterative

  ```Python
  from collections import deque
  def findBottomLeftValue(root: Optional[TreeNode]) -> int:
    if root is None: return 0
    result = 0
    que = deque([root])
    while que:
        size = len(que)
        result = que[0].val # 紀錄單層最左邊的節點
        for i in range(size):
            node = que.popleft()
            if node.left: que.append(node.left)
            if node.right: que.append(node.right)
    return result
  ```

### Path Sum

- [112. Path Sum](https://leetcode.com/problems/path-sum)

  - 用遞減的方式，每次減去遍歷路上節點的值，若最後計數器的值為 0，則找到目標總和
  - Recursive
    1. 參數與返回值
       - 參數：當前節點、計數器
       - 返回值：boolean 表達是否找到目標總和
    2. 終止條件
       - 遇到葉節點，判斷計數器當前是否為 0
    3. 單層邏輯
       - 遞迴求左右子樹

  ```Python
  def hasPathSum(root: Optional[TreeNode], targetSum: int) -> bool:

      def traversal(node: TreeNode, count: int) -> bool:
          if node.left is None and node.right is None:
              if count == 0:
                  return True
              else:
                  return False
          if node.left:
              if traversal(node.left, count - node.left.val): return True
          if node.right:
              if traversal(node.right, count - node.right.val): return True
          return False

      if root is None: return False
      return traversal(root, targetSum - root.val)
  ```

  - level-order traversal
  - Iterative
    - 使用 list (stack) 紀錄節點與從根節點遍歷到當前節點的數值總和

  ```Python
  def hasPathSum(root: Optional[TreeNode], targetSum: int) -> bool:
      if root is None: return False
      stack = [(root, root.val)]

      while stack:
          node, current_sum = stack.pop()
          if node.left is None and node.right is None and current_sum == targetSum:
              return True
          if node.right:
              stack.append((node.right, current_sum + node.right.val))
          if node.left:
              stack.append((node.left, current_sum + node.left.val))
      return False
  ```

- Related
  - [113. Path Sum II](https://leetcode.com/problems/path-sum-ii)

## Reference

[代码随想录](https://github.com/youngyangyang04/leetcode-master)
