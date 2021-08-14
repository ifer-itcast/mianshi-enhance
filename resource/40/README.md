## 微信支付

[微信小程序账号](https://mp.weixin.qq.com)

要企业认证、获取 AppID、生成 AppSecret、开通微信支付、关联商户号（微信支付-商户号管理-关联更多商户号）

[微信商户平台账号](https://pay.weixin.qq.com)

要企业认证、获取商户号（账户中心-个人信息-登录账号）、设置 API 秘钥（账户中心-API 安全-设置 API 秘钥）、APPID 授权（产品中心-APPID 授权管理-关联更多AppID）、配置支付接口（产品中心-开发配置-支付配置-JSAPI 支付）

[小程序支付](https://pay.weixin.qq.com/wiki/doc/apiv3/open/pay/chapter2_8_2.shtml)

## 前端流程

**a,** 创建订单

发送价格、地址、已选购物车列表，返回<font color="red">订单编号</font>

**b,** 订单预支付

发送<font color="red">订单编号</font>，返回<font color="red">订单预支付对象</font>

**c,** 发起微信支付

调用 <font color="red">uni.requestPayment()</font>，发送<font color="red">订单预支付对象</font>

**d,** 检查支付状态

微信支付完成后，根据<font color="red">订单编号</font>调用服务端接口查询订单支付状态并给出提示



