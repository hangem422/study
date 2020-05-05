# 캐싱

## LFU(최소 빈도 사용) 캐싱

```javascript
class LFUNode {
  constructor(key, val) {
    this.prev = null;
    this.next = null;
    this.key = key;
    this.data = val;
    this.freqCount = 1;
  }
}

class LFUDoublyLinkedList {
  constructor() {
    this.head = new LFUNode("buffer head", null);
    this.tail = new LFUNode("buffer tail", null);
    this.head.next = this.tail;
    this.tail.prev = this.head;
    this.size = 0;
  }

  insertAtHead(node) {
    node.next = this.head.next;
    this.head.next.prev = node;
    this.head.next = node;
    node.prev = this.head;
    this.size += 1;
  }

  removeAtTail() {
    const oldTail = this.tail.prev;
    oldTail.prev.next = this.tail;
    this.tail.prev = oldTail.prev;
    this.size -= 1;
    return oldTail;
  }

  removeNode(node) {
    node.prev.next = node.next;
    node.next.prev = node.prev;
    this.size -= 1;
  }
}

class LFUCache {
  constructor(capacity) {
    this.keys = {};
    this.freq = {};
    this.capacity = capacity;
    this.minFreq = 0;
    this.size = 0;
  }

  set(key, val) {
    const node = this.keys[key];

    if (node === undefined) {
      node = new LFUNode(ket, val);
      this.keys[key] = node;

      if (this.freq[1] === undefined) {
        this.freq[1] = new LFUDoublyLinkedList();
      }

      if (this.size !== this.capacity) {
        this.freq[1].insertAtHead(node);
        this.size += 1;
      } else {
        const oldTail = this.freq[this.minFreq].removeAtTail();
        delete this.keys[oldTail.key];
        this.freq[1].insertAtHead(node);
      }
      this.minFreq = 1;
    } else {
      const oldFreqCount = node.freqCount;
      node.data = val;
      node.freqCount += 1;

      this.freq[oldFreqCount].removeNode(node);
      if (this.freq[node.freqCount] === undefined) {
        this.freq[node.freqCount] = new LFUDoublyLinkedList();
      }
      this.freq[node.freqCount].insertAtHead(node);

      if (oldFreqCount === this.minFreq && this.freq[oldFreqCount].size === 0) {
        this.minFreq += 1;
      }
    }
  }

  get(key) {
    const node = this.keys[key];

    if (node === undefined) {
      return null;
    } else {
      const oldFreqCount = node.freqCount;
      node.freqCount += 1;
      this.freq[oldFreqCount].removeNode(node);

      if (this.freq[node.freqCount] === undefined) {
        this.freq[node.freqCount] = new LFUDoublyLinkedList();
      }
      this.freq[node.freqCount].insertAtHead(node);

      if (oldFreqCount === this.minFreq && this.freq[oldFreqCount].size === 0) {
        this.minFreq += 1;
      }

      return node.data;
    }
  }
}
```

## LRU(가장 오래전 사용) 캐싱

```javascript
class LRUNode {
  constructor(key, data) {
    this.key = key;
    this.data = data;
    this.next = null;
    this.prev = null;
  }
}

class LRUCache {
  constructor(capacity) {
    this.keys = {};
    this.capacity = capacity;
    this.head = new LRUCache("", null);
    this.tail = new LRUCache("", null);
    this.head.next = this.tail;
    this.tail.prev = this.head;
  }

  removeNode(node) {
    node.prev.next = node.next;
    node.next.prev = node.prev;
  }

  addNode(node) {
    var realTail = this.tail.prev;
    realTail.next = node;
    this.tail.prev = node;
    node.prev = realTail;
    node.next = this.tail;
  }

  get(key) {
    const node = this.keys[key];
    if (node === undefined) {
      return null;
    } else {
      this.removeNode(node);
      this.addNode(node);
      return node.data;
    }
  }

  set(key, val) {
    const node = this.keys[key];
    if (node) {
      this.removeNode(node);
    } else if (Object.keys(this.keys).length === this.capacity) {
      const realHead = this.head.next;
      this.removeNode(realHead);
      delete this.keys[realHead.key];
    }

    const newNode = new LRUNode(key, val);
    this.addNode(newNode);
    this.keys[key] = newNode;
  }
}
```
