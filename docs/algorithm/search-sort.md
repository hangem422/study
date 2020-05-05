# 검색

## 선형 검색

```javascript
function linearSearch(arr, n) {
  return arr.some((val) => val === n);
}
```

## 이진 검색

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

function quickSelectInPlace(arr, left, right, k) {
  const index = partition(arr, left, right);
  if (index == k - 1) return arr[index];
  else if (index > k - 1) return quickSelectInPlace(arr, left, index - 1, k);
  else return quickSelectInPlace(arr, index + 1, right, k);
}

function quickSelect(arr, k) {
  return quickSelectInPlace(arr, 0, arr.length - 1, k);
}
```

## 병합 정렬

```javascript
function merge(arr, left, right, mid) {
  const tempSize = right - left + 1;
  const temp = new Array(tempSize);
  let tempIndex = 0;
  let leftIndex = left;
  let rightIndex = mid + 1;

  while (leftIndex <= mid && rightIndex <= right) {
    if (arr[leftIndex] < arr[rightIndex]) {
      temp[tempIndex] = arr[leftIndex];
      leftIndex += 1;
    } else {
      temp[tempIndex] = arr[rightIndex];
      rightIndex += 1;
    }
    tempIndex += 1;
  }

  let remain = leftIndex > mid ? rightIndex : leftIndex;
  while (tempIndex < tempSize) {
    temp[tempIndex] = arr[remain];
    tempIndex += 1;
    remain += 1;
  }

  temp.forEach((val, index) => {
    arr[left + index] = val;
  });
}

function mergeSort(arr, left, right) {
  if (left < right) {
    const mid = Math.floor((left + right) / 2);
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, right, mid);
  }
}

function sort(arr) {
  mergeSort(arr, 0, arr.length - 1);
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
