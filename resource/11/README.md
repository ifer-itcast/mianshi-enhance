```javascript
const arr = ['apple', 'apple', 'orange', 'orange', 'apple', 'orange', 'apple'];

for (let i = 0; i < arr.length; i++) {
    // let j = i + 1，不要和自己比较
    for (let j = i + 1; j < arr.length; j++) {
        if (arr[i] === arr[j]) {
            arr.splice(j, 1);
            j--;
        }
    }
}
console.log(arr);
```
