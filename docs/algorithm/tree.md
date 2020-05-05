# 트리

## 일반적인 트리

```javascript
class TreeNode {
  constructor(val) {
    this.value = val;
    this.children = [];
  }
}
```

## 이진 트리

```javascript
class BinaryTreeNode {
  constructor(val) {
    this.value = val;
    this.left = null;
    this.right = null;
  }
}

class BinaryTree {
  constructor() {
    this._root = null;
  }

  // 선순위 순회
  traversePreOreder(node = this._root) {
    if (node instanceof BinaryTreeNode) {
      console.log(node.value);
      if (node.left) this.traversePreOreder(node.left);
      if (node.right) this.traversePreOreder(node.right);
    }
  }

  // 재귀를 사용하지 않는 선순위 순회
  traversePreOrederIterative(node = this._root) {
    const nodeStack = [];
    if (node instanceof BinaryTreeNode) nodeStack.push(node);

    while (nodeStack.length > 0) {
      const cur = nodeStack.pop();
      console.log(cur.value);

      if (cur.right) nodeStack.push(cur.right);
      if (cur.left) nodeStack.push(cur.left);
    }
  }

  // 중순위 순회
  traverseInOrder(node = this._root) {
    if (node instanceof BinaryTreeNode) {
      if (node.left) this.traverseInOrder(node.left);
      console.log(node.value);
      if (node.right) this.traverseInOrder(node.right);
    }
  }

  // 재귀를 사용하지 않는 중순위 선회
  traverseInOrderOterative(node = this._root) {
    const cur = node;
    const stack = [];
    let done = false;

    while (!done) {
      if (cur !== null) {
        stack.push(cur);
        cur = cur.left;
      } else {
        if (stack.length > 0) {
          cur = stack.pop();
          console.log(cur.value);
          cur = cur.right;
        } else {
          done = true;
        }
      }
    }
  }

  // 후순위 선회
  traversePostOrder(node = this._root) {
    if (node instanceof BinaryTreeNode) {
      if (node.left) this.traversePostOrder(node.left);
      if (node.right) this.traversePostOrder(node.right);
      console.log(node.value);
    }
  }

  // 재귀를 사용하지 않는 후순위 순회
  traversePostOrderIterative(node = this._root) {
    const stack1 = [];
    const stack2 = [];
    if (node instanceof BinaryTreeNode) stack1.push(node);

    while (stack1.length > 0) {
      const cur = stack1.pop();
      stack2.push(cur.value);

      if (cur.left) stack1.push(cur.left);
      if (cur.right) stack1.push(cur.right);
    }

    while (stack2.length > 0) {
      console.log(stack2.pop());
    }
  }

  // 단계순위 순회
  traverseLevelOrder(node = this._root) {
    const queue = [];
    if (node instanceof BinaryTreeNode) queue.push(node);

    while (queue.length > 0) {
      const cur = queue.shift();
      console.log(cur.value);
      if (cur.left) queue.push(cur.left);
      if (cur.right) queue.push(cur.right);
    }
  }
}
```

## 이진 검색 트리

```javascript
class BinaryTreeNode {
  constructor(val) {
    this.value = val;
    this.left = null;
    this.right = null;
  }
}

class BinarySearchTree {
  constructor() {
    this._root = null;
  }

  // 삽입
  insert(val) {
    const node = new BinaryTreeNode(val);
    if (!this._root) this._root = node;
    else {
      const cur = this._root;
      while (1) {
        if (cur.value > val) {
          if (cur.left) cur = cur.left;
          else {
            cur.left = node;
            break;
          }
        } else if (cur.value < val) {
          if (cur.right) cur = cur.right;
          else {
            cur.right = node;
            break;
          }
        } else break;
      }
    }
  }

  // 삭제
  remove(val) {
    function findMin(node) {
      const cur = node;
      while (cur.left) {
        cur = cur.left;
      }
      return cur;
    }

    function deleteRecursively(node, data) {
      if (!node) return null;
      if (data < node.value) {
        node.left = deleteRecursively(node.left, data);
      } else if (data > node.value) {
        node.right = deleteRecursively(node.right, data);
      } else {
        if (!node.left && !node.right) return null;
        if (!node.left || !node.right) return node.left || node.right;
        if (node.left && node.right) {
          const temp = findMin(node.right);
          node.value = temp.value;
          node.right = deleteRecursively(node.right, temp.value);
        }
      }
      return node;
    }

    return deleteRecursively(this._root, val);
  }

  // 검색
  findNode(val) {
    const cur = this._root;

    while (cur) {
      if (cur.value > val) cur = cur.left;
      else if (cur.value < val) cur = cur.right;
      else return true;
    }

    return false;
  }
}
```
