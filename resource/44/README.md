## 是什么

重绘：影响可见性不影响布局，就会发生重绘，例如 opacity、background-color、visibility、outline 等

回流：几何属性发生变化时会进行回流（例如元素的尺寸和位置）

## 减少重绘和回流

1、批量更新元素

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: red;
        }
    </style>
</head>

<body>
    <div id="oDiv"></div>
    <script>
        /* oDiv.style.width = '100px';
        oDiv.style.height = '100px';
        oDiv.style.backgroundColor = 'red'; */

        oDiv.className = 'box';
    </script>
</body>

</html>
```

2、文档碎片

