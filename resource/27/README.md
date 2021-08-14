##  0. 冒泡

```javascript
const arr = [5, 4, 3, 1, 2];

for (let i = 0; i < arr.length - 1; i++) {
    let flag = true;
    for (let j = 0; j < arr.length - 1 - i; j++) {
        if (arr[j] > arr[j + 1]) {
            flag = false;

            [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        }
    }
    if (flag) break; // 代表未曾进去过，说明已经排好了
    console.log('次数测试');
}

console.log(arr);
```

## 2. 选择
每次找到最大或最小的与当前外层索引交换位置！与冒泡不同的是它不是每比较就交换，而是一轮比较完毕再交换！

```javascript
const arr = [3, 5, 4, 1, 2];

for (let i = 0; i < arr.length; i++) {
    let min = i; // 假设最小索引

    // 检查是否还有更小的索引
    for (j = i + 1; j < arr.length; j++) {
        if (arr[j] < arr[min]) {
            min = j;
        }
    }
    // 假设失败，则交换位置
    if (i !== min) {
        [arr[i], arr[min]] = [arr[min], arr[i]];
    }
}

console.log(arr);
```

## 3. 快排

```javascript
const arr = [5, 3, 4, 1, 2];

function fn(arr) {
    // #1 条件
    if (arr.length <= 1) return arr;

    // #2 中数
    let cenValue = arr.splice(Math.floor(arr.length / 2), 1)[0];

    // #3 和中数比较
    let leftArr = [];
    let rightArr = [];
    for (let i = 0; i < arr.length; i++) {
        if (arr[i] < cenValue) {
            leftArr.push(arr[i]);
        } else {
            rightArr.push(arr[i]);
        }
    }

    // #4 返回
    return fn(leftArr).concat(cenValue, fn(rightArr));
}

console.log(fn(arr));
```

## 4. 归并
