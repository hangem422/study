# 힙

## 이진 힙

```javascript
class Heap {
  constructor() {
    this.items = [];
  }

  swap(index1, index2) {
    const temp = this.items[index1];
    this.items[index1] = this.items[index2];
    this.items[index2] = temp;
  }

  parentIndex(index) {
    return Math.floor((index - 1) / 2);
  }

  leftChildIndex(index) {
    return index * 2 + 1;
  }

  rigthChildIndex(index) {
    return index * 2 + 2;
  }

  parent(index) {
    return this.items[this.parentIndex(index)];
  }

  leftChild(index) {
    return this.items[this.leftChildIndex(index)];
  }

  rightChild(index) {
    return this.items[this.rigthChildIndex(index)];
  }

  peak() {
    return this.items[0];
  }

  size() {
    return this.items.length;
  }
}
```

## 최소 힙

```javascript
class MinHeap extends Heap {
  bubbleDown() {
    let index = 0;
    while (this.leftChild(index)) {
      const smallerIndex =
        this.rightChild(index) && this.rightChild(index) < this.leftChild(index)
          ? this.rigthChildIndex(index)
          : this.leftChildIndex(index);
      if (this.items[index] < this.items[smallerIndex]) break;
      this.swap(smallerIndex, index);
      index = smallerIndex;
    }
  }

  bubbleUp() {
    let index = this.size() - 1;
    while (this.parent(index) && this.parent(index) > this.items[index]) {
      this.swap(this.parentIndex(index), index);
      index = this.parentIndex(index);
    }
  }

  add(item) {
    this.items.push(item);
    this.bubbleUp();
  }

  poll() {
    const item = this.peak();
    this.items[0] = this.items[this.size() - 1];
    this.items.pop();
    this.bubbleDown();
    return item;
  }
}
```

## 최대 힙

```javascript
class MaxHeap extends Heap {
  bubbleDown() {
    let index = 0;
    while (this.leftChild(index)) {
      const biggerIndex =
        this.rightChild(index) && this.rightChild(index) > this.leftChild(index)
          ? this.rightChildIndex(index)
          : this.leftChildIndex(index);
      if (this.items[index] > this.items[biggerIndex]) break;
      this.swap(biggerIndex, index);
      index = biggerIndex;
    }
  }

  bubbleUp() {
    let index = this.size() - 1;
    while (this.parent(index) && this.parent(index) < this.items[index]) {
      this.swap(this.parentIndex(index), index);
      index = this.parentIndex(index);
    }
  }

  add(item) {
    this.items.push(item);
    this.bubbleUp();
  }

  poll() {
    const item = this.peak();
    this.items[0] = this.items[this.size() - 1];
    this.items.pop();
    this.bubbleDown();
    return item;
  }
}
```
