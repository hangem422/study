# 동적 프로그래밍

## 걸음 수를 채우는 방법

n걸음 거리가 주어졌을 떄 한 걸음, 두 걸음, 세 걸음을 가지고 n걸을음 채우는 여러 방법이 존재한다. 몇 가지 조합니 가능한지 구해라.

### 일반적인 방법

```javascript
function waysToCoverSteps(step) {
  if (step < 0) return 0;
  if (step === 0) return 1;

  return (
    waysToCoverSteps(step - 1) +
    waysToCoverSteps(step - 2) +
    waysToCoverSteps(step - 3)
  );
}
```

### 동적 프로그래밍

```javascript
function wayToCoverStepsDP(step) {
  const cache = {};
  if (step < 0) return 0;
  if (step == 0) return 1;

  if (cache[step]) {
    return cache[step];
  } else {
    cache[step] =
      wayToCoverStepsDP(step - 1) +
      wayToCoverStepsDP(step - 2) +
      wayToCoverStepsDP(step - 3);
    return cache[step];
  }
}
```

```javascript
function wayToCoverStepsDP(stepArr, stepType, stepValue) {
  const dpMatrix = new Array(stepValue + 1)
    .fill(null)
    .map(() => new Array(stepType).fill(undefined));
  dpMatrix[0].fill(1);

  for (let i = 1; i <= stepValue; i += 1) {
    for (let j = 0; j <= stepType; j += 1) {
      const temp1 = i - stepArr[j] >= 0 ? dpMatrix[i - stepArr[j]][j] : 0;
      const temp2 = j >= 1 ? dpMatrix[i][j - 1] : 0;
      dpMatrix[i][j] = temp1 + temp2;
    }
  }
  return dpMatrix[stepValue][stepType - 1];
}

function wayToCoverStepsDPWrapper(stepArr, stepValue) {
  return wayToCoverStepsDP(stepArr, stepArr.length, stepValue);
}
```

## 배낭 문제 알고리즘

무게와 가치를 지니는 n개의 항목이 주어졌을 때 최대 w의 무개를 담을 수 잇는 배낭에 해당 항목들을 집어넣어 배낭에 담긴 함목들의 가치의 합이 최대가 되도록 한다,

### 일반적인 방법

```javascript
function knapsackNavie(index, weight, values, target) {
  let result = 0;

  if (index <= -1 || target <= 0) {
    result = 0;
  } else if (weight[index] > target) {
    result = knapsackNavie(index - 1, weight, values, target);
  } else {
    const cur = knapsackNavie(index - 1, weight, values, target);
    const other =
      values[index] +
      knapsackNavie(index - 1, weight, values, target - weight[index]);
    result = Math.max(cur, other);
  }
  return result;
}
```

### 동적 알고리즘

```javascript
function knapsackKDP(index, weight, values, target, matrixDP) {
  let result = 0;

  if (matrixDP[index + "-" + target]) {
    return matrixDP[index + "-" + target];
  }

  if (index <= -1 || target <= 0) {
    result = 0;
  } else if (weight[index] < target) {
    result = knapsackKDP(index - 1, weight, values, target, matrixDP);
  } else {
    const cur = knapsackKDP(index - 1, weight, values, target);
    const other =
      values[index] +
      knapsackKDP(index - 1, weight, values, target - weight[index]);
    result = Math.max(cur, other);
  }
  matrixDP[index + "-" + target] = result;
  return result;
}
```

```javascript
function wayToCoverStepsDP(weight, values, num, target) {
  const matrixDP = new Array(target + 1)
    .fill(null)
    .map(() => new Array(num).fill(undefined));
  matrixDP[0].fill(0);

  for (let i = 1; i <= target; i += 1) {
    for (let j = 0; j < num; j += 1) {
      let temp1 = i - weight[j] >= 0 ? values[j] : 0;
      let temp2 = j >= 1 ? matrixDP[i][j - 1] : 0;

      if (j >= 1 && i - weight[j] >= 0) temp1 += matrixDP[i - weight[j]][j - 1];

      matrixDP[i][j] = Math.max(temp1, temp2);
      console.log(matrixDP);
    }
  }

  return matrixDP[target][num - 1];
}

function wayToCoverStepsDPWrapper(weight, values, target) {
  return wayToCoverStepsDP(weight, values, values.length, target);
}
```

## 최장 공통 부분 수열 알고리즘

두 개의 수열이 잇을 때 두 수열의 가장 긴 공통 부분 수열의 길이를 찾습니다. 이때 부분 수열 내 항목들이 연속일 필요는 없고 순서만 맞으면 됩니다.

### 일반적인 방법

```javascript
function LCSNaive(str1, str2, str1Length, str2Length) {
  if (str1Length === 0 || str2Length === 0) return 0;

  if (str1[str1Length - 1] === str2[str2Length - 1]) {
    return 1 + LCSNaive(str1, str2, str1Length - 1, str2Length - 1);
  } else {
    return Math.max(
      LCSNaive(str1, str2, str1Length, str2Length - 1),
      LCSNaive(str1, str2, str1Length - 1, str2Length)
    );
  }
}

function LCSNaiveWrapper(str1, str2) {
  return LCSNaive(str1, str2, str1.length, str2.length);
}
```

### 동적 알고리즘

```javascript
function logestCommonSequesnceLength(str1, str2) {
  const rowLength = str1.length + 1;
  const colLength = str2.length + 1;
  const matrix = new Array(rowLength)
    .fill(null)
    .map(() => new Array(colLength).fill(0));

  for (let i = 1; i < rowLength; i += 1) {
    for (let j = 1; j < colLength; j += 1) {
      const str1Char = str1.charAt(i - 1);
      const str2Char = str2.charAt(j - 1);

      if (str1Char === str2Char) {
        matrix[i][j] = matrix[i - 1][j - 1] + 1;
      } else {
        matrix[i][j] = Math.max(matrix[i - 1][j], matrix[i][j - 1]);
      }
    }
  }
  return matrix[rowLength][colLength];
}
```

## 동전 교환 알고리즘

동전의 금액 종류가 S={S1, S2, ...Sm}으로 M개이고 각 금액의 동전이 무한 개로 제공될 수 있다고 할 때, 금액 n을 동전으로 교환하기 위한 동전의 조합은 몇 개나 될까? 이 떄 동전의 순서는 무시한다.

### 일반적인 방법

```javascript
function countCoinWays(coinArr, numCoins, coinValue) {
  if (coinValue === 0) return 1;
  if (coinValue < 0 || numCoins <= 0) return 0;

  return (
    countCoinWays(coinArr, numCoins - 1, coinValue) +
    countCoinWays(coinArr, numCoins, coinValue - coinArr[numCoins - 1])
  );
}

function countCoinWaysWrapper(coinArr, coinValue) {
  return countCoinWays(coinArr, coinArr.length, coinValue);
}
```

### 동적 알고리즘

```javascript
function countCoinWaysDP(coinArr, numCoins, coinValue) {
  const dpMatrix = new Array(coinValue + 1)
    .fill(null)
    .map(() => new Array(numCoins).fill(undefined));
  dpMatrix[0].fill(1);

  for (let i = 1; i <= coinValue; i += 1) {
    for (let j = 0; j < numCoins; j += 1) {
      const temp1 = i - coinArr[j] >= 0 ? dpMatrix[i - coinArr[j]][j] : 0;
      const temp2 = j >= 1 ? dpMatrix[i][j - 1] : 0;
      dpMatrix[i][j] = temp1 + temp2;
    }
  }
  return dpMatrix[coinValue][numCoins - 1];
}

function countCoinWaysDPWrapper(coinArr, coinValue) {
  return countCoinWaysDP(coinArr, coinArr.length, coinValue);
}
```

## 편집 거리 알고림즘

길이 m인 문자열 str1과 길이 n인 문자열 str2가 주어졌을 때 str1을 str2로 변환하기 위한 최소 편집 횟수는 무엇인가?

## 일반적인 방법

```javascript
function editDistanceRecursive(str1, str2, length1, length2) {
  if (length1 === 0) return length2;
  if (length2 === 0) return length1;
  if (str1[length1 - 1] === str2[length2 - 1]) {
    return editDistanceRecursive(str1, str2, length1 - 1, length2 - 1);
  }
  return (
    1 +
    Math.min(
      editDistanceRecursive(str1, str2, length1, length2 - 1),
      editDistanceRecursive(str1, str2, length1 - 1, length2),
      editDistanceRecursive(str1, str2, length1 - 1, length2 - 1)
    )
  );
}

function editDistanceRecursiveWrapper(str1, str2) {
  return editDistanceRecursive(str1, str2, str1.length, str2.length);
}
```

### 동적 프로그래밍

```javascript
function editDistanceDP(str1, str2, length1, length2) {
  const dpMatrix = new Array(length1 + 1)
    .fill(undefined)
    .map(() => new Array(length2 + 1).fill(undefined));

  for (let i = 0; i <= length1; i += 1) {
    for (let j = 0; j <= length2; j += 1) {
      if (i === 0) {
        dpMatrix[i][j] = j;
      } else if (j === 0) {
        dpMatrix[i][j] = i;
      } else if (str1[i - 1] === str2[j - 1]) {
        dpMatrix[i][j] = dpMatrix[i - 1][j - 1];
      } else {
        dpMatrix[i][j] =
          1 +
          Math.min(
            dpMatrix[i][j - 1],
            dpMatrix[i - 1][j],
            dpMatrix[i - 1][j - 1]
          );
      }
    }
  }
  console.log(dpMatrix);
  return dpMatrix[length1][length2];
}

function editDistanceDPWrapper(str1, str2) {
  return editDistanceDP(str1, str2, str1.length, str2.length);
}
```
