# 연결 리스트

## 단일 연결 리스트

```javascript
class SinglyLinkedListNode {
  constructor(data) {
    this.data = data;
    this.next = null;
  }
}

class SinglyLinkedList {
  constructor() {
    this.head = null;
    this.size = 0;
  }

  isEmpty() {
    return this.size === 0;
  }

  print() {
    let cur = this.head;
    const datas = [];
    while (cur) {
      datas.push(cur.data);
      cur = cur.next;
    }
    return datas;
  }

  // 삽입
  insert(val) {
    if (this.head === null) {
      this.head = new SinglyLinkedListNode(val);
    } else {
      const temp = this.head;
      this.head = new SinglyLinkedListNode(val);
      this.head.next = temp;
    }
    this.size += 1;
  }

  // 값에 의한 삭제
  remove(val) {
    let cur = this.head;
    if (cur.data === val) {
      this.head = cur.next;
      this.size -= 1;
    } else {
      while (cur.next) {
        const next = cur.next;
        if (next.data === val) {
          cur.next = next.next;
          this.size -= 1;
          break;
        } else {
          cur = next;
        }
      }
    }
  }

  // 헤드 항목 삭제
  deleteAthHead() {
    let toReturn = null;
    if (this.head !== null) {
      toReturn = this.head.data;
      this.head = this.head.next;
      this.size -= 1;
    }
    return toReturn;
  }

  // 검색
  find(val) {
    const cur = this.head;
    while (cur) {
      if (cur.data === val) return true;
      cur = cur.next;
    }
    return false;
  }
}
```

## 이중 연결 리스트

```javascript
class DoublyLinkedListNode {
  constructor(data) {
    this.data = data;
    this.next = null;
    this.prev = null;
  }
}

class DoublyLinkedList {
  constructor() {
    this.head = null;
    this.tail = null;
    this.size = 0;
  }

  isEmpty() {
    return this.size === 0;
  }

  print() {
    let cur = this.head;
    const datas = [];
    while (cur) {
      datas.push(cur.data);
      cur = cur.next;
    }
    return datas;
  }

  // 헤드에 항목에 삽입하기
  addAtFront(val) {
    if (this.head === null) {
      this.head = new DoublyLinkedListNode(val);
      this.tail = this.head;
    } else {
      const temp = new DoublyLinkedListNode(val);
      temp.next = this.head;
      this.head.prev = temp;
      this.head = temp;
    }
    this.size += 1;
  }

  // 테일에 항목 삽입하기
  insertAtTail(val) {
    if (this.tail === null) {
      this.tail = new DoublyLinkedListNode(val);
      this.head = this.tail;
    } else {
      const temp = new DoublyLinkedListNode(val);
      temp.prev = this.tail;
      this.tail.next = temp;
      this.tail = temp;
    }
    this.size += 1;
  }

  // 헤드의 항목 삭제하기
  deleteAtHead() {
    const toReturn = null;
    if (this.head !== null) {
      toReturn = this.head.data;
      if (this.tail === this.head) {
        this.head = null;
        this.tail = null;
      } else {
        this.head = this.head.next;
        this.head.prev = null;
      }
      this.size -= 1;
    }
    return toReturn;
  }

  // 테일의 항목 삭제하기
  deleteAtTail() {
    const toReturn = null;
    if (this.tail !== null) {
      toReturn = this.tail.data;
      if (this.tail === this.head) {
        this.head = null;
        this.tail = null;
      } else {
        this.tail = this.tail.prev;
        this.tail.next = null;
      }
      this.size -= 1;
    }
    return toReturn;
  }

  // 검색
  findStartingHead(val) {
    const cur = this.head;
    while (cur.next) {
      if (cur.data === val) return true;
      cur = cur.next;
    }
    return false;
  }

  findStartringTail(val) {
    const cur = this.tail;
    while (cur) {
      if (cur.data === val) return true;
      cur = cur.prev;
    }
    return false;
  }
}
```
