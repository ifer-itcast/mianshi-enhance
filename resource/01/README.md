## 缓存位置
会依次查找 `Service Worker`、`Memory Cache`、`Disk Cache`、`网络请求`

## 强制缓存
**HTTP1.0**

通过响应头设置 `Pragma: no-cache;`，表示每次都会向服务器发送请求

通过响应头设置 `Expires: Thu, 10 Nov 2017 08:45:11 GMT`，可以指定具体时间，表示还未到此时间点则不发起请求，但此时间是相对于服务器时间而言的，如果客户端时间和服务器时间不一致那就没什么用了

**HTTP1.1**

通过响应头设置 `Cache-Control: max-age=3600;` 表示资源缓存的最大时间，该时间内不会发起请求。注意区分 max-age=0、no-cache、no-store 的差异，max-age=0，会检验 ETag/Last-Modified，并通过对应的请求头携带；no-cache 会缓存内容，每次会发起请求，是否使用此内容由后续的对比决定；no-store 不缓存，所有内容不走缓存直接发起请求

**优先级**

`Pragma` > `Cache-Control` > `Expires`

## 对比缓存
对比缓存又叫协商缓存，当强制缓存失效时，就需要使用对比缓存，对比缓存在请求数量上和没有缓存并无差异，但若返回的 304 其实仅仅是一个状态码，并没有实际的内容，在响应体体积上的节省才是它的优化点

**Last-Modified 和 If-Modified-Since**

1、服务器响应 Last-Modified 字段标记最后一次被修改的时间

2、浏览器下一次请求相同资源时，在请求头中将上次的 Last-Modified 的值写入到请求头的 If-Modified-Since 字段

3、服务器会将 If-Modified-Since 的值与 Last-Modified 字段进行对比，相等则响应 304，反之则响应 200 状态码，并返回修改后的数据

缺陷：如果资源更新的速度是秒以下单位，那么该缓存是不能被使用的，因为它的时间单位最低是秒

**Etag 和 If-None-Match**

1、服务器响应文件的特殊标识（摘要）Etag

2、浏览器下一次请求相同资源时，通过请求头 If-None-Match 带上 Etag

3、服务器同样进行比较，命中返回 304, 不命中返回新资源和 200

**优先级**

`Etag` 的优先级高于 `Last-Modified`

## 做个总结
当浏览器要请求资源时，会依次查看 `Service Worker`、`memory cache`、`disk cache`，如果有强制缓存且未失效，则使用强制缓存，这时的状态码是 200；强制缓存失效则使用对比缓存，服务端比较后返回 304 或者 200（并携带资源）