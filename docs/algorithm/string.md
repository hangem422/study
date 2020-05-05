# 고급 문자열

## 트라이(접두사 트리)

```javascript
class TrieNode {
  constructor() {
    this.children = {};
    this.endOfWord = false;
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  insert(word) {
    let cur = this.root;
    for (let i = 0; i < word.length; i += 1) {
      const ch = word.charAt(i);
      let node = cur.children[ch];
      if (!node) {
        node = new TrieNode();
        cur.children[ch] = node;
      }
      cur = node;
    }
    cur.endOfWord = true;
  }

  search(word) {
    let cur = this.root;
    for (let i = 0; i < word.length; i += 1) {
      const ch = word.charAt(i);
      const node = cur.children[ch];
      if (!node) return false;
      cur = node;
    }
    return cur.endOfWord;
  }

  delete(word) {
    const deleteRecursively = (cur, index) => {
      if (index === word.length) {
        if (!cur.endOfWord) return false;
        cur.endOfWord = false;
        return Object.keys(cur.children).length === 0;
      }

      const ch = word.charAt(index);
      const node = cur.children[ch];
      if (!node) return false;

      const shouldDeleteCurrentNode = deleteRecursively(node, index + 1);
      if (shouldDeleteCurrentNode) {
        delete cur.children[cur];
        return Object.keys(cur.children).length === 0;
      }
      return false;
    };

    deleteRecursively(this.root, 0);
  }
}
```

## 보이어-무어 문자열 검색

```javascript
function buildBadMatchTable(str) {
  const tableObj = {};
  const strLength = str.length;

  for (let i = 0; i < strLength - 1; i += 1) {
    tableObj[str[i]] = strLength - 1 - i;
  }
  if (tableObj[str[strLength - 1]] === undefined) {
    tableObj[str[strLength - 1]] = strLength;
  }
  return tableObj;
}

function boyermoore(str, pattern) {
  const badMatchTable = buildBadMatchTable(pattern);
  const patternLastIndex = pattern.length - 1;
  const maxOffset = str.length - pattern.length;
  let offset = 0;
  let scanIndex = patternLastIndex;

  while (offset <= maxOffset) {
    scanIndex = 0;
    while (pattern[scanIndex] === str[scanIndex + offset]) {
      if (scanIndex === patternLastIndex) return offset;
      scanIndex += 1;
    }
    const badMatchString = str[offset + patternLastIndex];
    offset += badMatchTable[badMatchString] || 1;
  }
  return -1;
}
```

## 카누스-모리스-플랫 문자열 검색

```javascript
function longestPrefix(str) {
  const prefix = new Array(str.length);
  let maxPrefix = 0;

  prefix[0] = 0;
  for (let i = 1; i < str.length; i += 1) {
    while (str.charAt(i) !== str.charAt(maxPrefix) && maxPrefix > 0) {
      maxPrefix = prefix[maxPrefix - 1];
    }
    if (str.charAt(maxPrefix) === str.charAt(i)) {
      maxPrefix += 1;
    }
    prefix[i] = maxPrefix;
  }
  return prefix;
}

function KMP(str, pattern) {
  const prefixTable = longestPrefix(pattern);
  let patternIndex = 0;
  let strIndex = 0;

  while (strIndex < str.length) {
    if (str.charAt(strIndex) !== pattern.charAt(patternIndex)) {
      if (patternIndex !== 0) patternIndex = prefixTable[patternIndex - 1];
      else strIndex += 1;
    } else {
      strIndex += 1;
      pattern += 1;
    }

    if (patternIndex === pattern.length) return true;
  }
  return false;
}
```

## 라빈 카프 검색

```javascript
class RabinKarpSearch {
  constructor() {
    this.prime = 101;
  }

  rabinkarpFingerprintHash(str, endLength) {
    let hashInt = 0;
    if (!endLength) endLength = str.length;
    for (let i = 0; i < (endLength || str.length); i += 1) {
      hashInt += str.charCodeAt(i) * Math.pow(this.prime, i);
    }
    return hashInt;
  }

  recalculateHash(str, oldIndex, newIndex, oldHash, patternLength) {
    if (!patternLength) patternLength = str.length;
    let newHash = oldHash - str.charCodeAt(oldIndex);
    newHash = Math.floor(newHash / this.prime);
    newHash +=
      str.charCodeAt(newIndex) * Math.pow(this.prime, patternLength - 1);
    return newHash;
  }

  strEquals(str1, startIndex1, endIndex1, str2, startIndex2, endIndex2) {
    if (endIndex1 - startIndex1 !== endIndex2 - startIndex2) return false;
    while (startIndex1 <= endIndex1 && startIndex2 <= endIndex2) {
      if (str1[startIndex1] !== str2[startIndex2]) return false;
      startIndex1 += 1;
      startIndex2 += 1;
    }
    return true;
  }

  rabinkarpSearch(str, pattern) {
    const strLength = str.length;
    const patternLength = pattern.length;
    const patternHash = this.rabinkarpFingerprintHash(pattern, patternLength);
    let textHash = this.rabinkarpFingerprintHash(str, patternLength);

    for (let i = 0; i < strLength - patternLength + 1; i += 1) {
      if (
        patternHash === textHash &&
        this.strEquals(
          str,
          i,
          i + patternLength - 1,
          pattern,
          0,
          patternLength - 1
        )
      ) {
        return i;
      }
      if (i < strLength - patternLength) {
        textHash = this.recalculateHash(
          str,
          i,
          i + patternLength,
          textHash,
          patternLength
        );
      }
    }
    return -1;
  }
}
```
