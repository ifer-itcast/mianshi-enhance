```javascript
const arr = [1, 2, 3, [1, 2, 3, 4, [2, 3, 4]]];

function fn(arr) {
    return arr.reduce((calc, cur) => {
        return calc.concat( Array.isArray(cur) ? fn(cur) : cur );
    }, []);
}

console.log(fn(arr));
```
