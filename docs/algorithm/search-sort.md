# 검색

## 선형 검색

```javascript
function linearSearch(arr, n) {
  return arr.some((val) => val === n);
}
```

## 이진 검색

이분탐색은 정답을 기계적으로 찾기 힘들지만(eg. 보통 brute-force를 고려하면 지수적으로 나옴), 정답의 범위가 정해졌으며, 추측한 답과 정답과의 오차(+/- 를 알 수 있어야)를 선형시간에 판단 할 수 있는 조건의 문제에 효율적으로 적용 할 수 있다.

```javascript
function binarySearch(arr, n) {
  let lowIndex = 0;
  let highIndex = arr.length - 1;

  while (lowIndex <= highIndex) {
    const midIndex = Math.floor((highIndex + lowIndex) / 2);
    if (arr[midIndex] === n) return midIndex;
    else if (n > arr[midIndex]) lowIndex = midIndex;
    else highIndex = midIndex;
  }

  return -1;
}
```

# 정렬

## 거품 정렬

```javascript
function swap(arr, index1, index2) {
  const temp = arr[index1];
  arr[index1] = arr[index2];
  arr[index2] = temp;
}

function bubbleSort(arr) {
  for (let i = arr.length - 1; i > 0; i -= 1) {
    for (let j = 0; j < i; j += 1) {
      if (arr[j] > arr[j + 1]) swap(arr, j, j + 1);
    }
  }
}
```

## 선택 정렬

```javascript
function swap(arr, index1, index2) {
  const temp = arr[index1];
  arr[index1] = arr[index2];
  arr[index2] = temp;
}

function selectionSort(arr) {
  const size = arr.length;

  for (let i = 0; i < size - 1; i += 1) {
    let min = i;
    for (let j = i + 1; j < size; j += 1) {
      if (arr[j] < arr[min]) min = j;
    }
    if (i !== min) swap(arr, i, min);
  }
}
```

## 삽입 정렬

```javascript
function insertionSort(arr) {
  const size = arr.length;
  for (let i = 1; i < size; i += 1) {
    const val = arr[i];
    let j = i - 1;
    // 정렬된 부분의 값이 정렬되지 않은 부분의 값보다 큰 경우 하나씩 이동시킨다.
    // 이는 값을 삽입할 공간을 만든다.
    for (; j >= 0 && arr[j] > val; j -= 1) {
      arr[j + 1] = arr[j];
    }
    arr[j + 1] = val;
  }
}
```

## 빠른 정렬

```javascript
function swap(arr, index1, index2) {
  const temp = arr[index1];
  arr[index1] = arr[index2];
  arr[index2] = temp;
}

function partition(arr, left, right) {
  const pivot = arr[Math.floor((left + right) / 2)];
  while (left <= right) {
    while (pivot > arr[left]) {
      left += 1;
    }
    while (pivot < arr[right]) {
      right -= 1;
    }

    if (left <= right) {
      if (left !== right) swap(arr, left, right);
      left += 1;
      right -= 1;
    }
  }
  return left;
}

function quickSortHelper(arr, left, right) {
  if (arr.length > 1) {
    const index = partition(arr, left, right);
    if (left < index - 1) quickSortHelper(arr, left, index - 1);
    if (index < right) quickSortHelper(arr, index, right);
  }
}

function quickSort(arr) {
  quickSortHelper(arr, 0, arr.length - 1);
}
```

## 빠른 선택

```javascript
function swap(arr, index1, index2) {
  const temp = arr[index1];
  arr[index1] = arr[index2];
  arr[index2] = temp;
}

function partition(arr, left, right) {
  const pivot = arr[Math.floor((left + right) / 2)];
  let leftIndex = left;
  let rightIndex = right;
  while (leftIndex <= rightIndex) {
    while (pivot > arr[leftIndex] && leftIndex <= right) leftIndex += 1;
    while (pivot < arr[rightIndex] && rightIndex >= left) rightIndex -= 1;
    if (leftIndex <= rightIndex) {
      if (leftIndex !== rightIndex) swap(arr, leftIndex, rightIndex);
      leftIndex += 1;
      rightIndex -= 1;
    }
  }
  return leftIndex;
}

function quickSelectInPlace(arr, k) {
  let left = 0;
  let right = arr.length - 1;
  while (left < right) {
    const index = partition(arr, left, right);
    if (index > k - 1) right = index - 1;
    else left = index;
  }
  return arr[k - 1];
}
```

## 병합 정렬

```javascript
function merge(left, right) {
  let result = [];
  let leftIndex = 0;
  let rightIndex = 0;

  while (leftIndex < left.length && rightIndex < right.length) {
    if (left[leftIndex] < right[rightIndex]) {
      result.push(left[leftIndex]);
      leftIndex++;
    } else {
      result.push(right[rightIndex]);
      rightIndex++;
    }
  }

  return [...result, ...left.slice(leftIndex), ...right.slice(rightIndex)];
}

function mergeSort(arr) {
  if (arr.length === 1) return arr;

  const middle = Math.floor(arr.length / 2);
  const left = arr.slice(0, middle);
  const right = arr.slice(middle);

  return merge(mergeSort(left), mergeSort(right));
}
```

## 계수 정렬

```javascript
function countSort(arr) {
  const hash = {};
  const countArr = [];

  arr.forEach((val) => {
    if (!hash[val]) hash[val] = 1;
    else hash[val] += 1;
  });

  for (key in hash) {
    for (let i = 0; i < hash[key]; i += 1) {
      countArr.push(parseInt(key));
    }
  }

  return countArr;
}
```
