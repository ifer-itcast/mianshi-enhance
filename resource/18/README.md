## 证书验证阶段

1\. 客户端发起请求

2\. 服务端返回证书加密后公钥，即**用 CA 的私钥加密后的公钥**

3\. 客户端判断证书是否合法，用内置的 CA 公钥解密，获得服务端的公钥

## 数据传输阶段

4\. 生成一个随机数，用服务端的公钥加密随机数，并发给服务器

5\. 服务端用私钥解密得到随机数

6\. 用这个随机数对称加密数据并返回给客户端