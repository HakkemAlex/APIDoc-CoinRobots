# RESTful API for CoinRobots
## 请求交互
* RESTful访问的根URL：`https://open.coinrobots.com/api/`<br/>
* 所有请求基于Https协议，请求头信息中contentType需要统一设置为：application/x-www-form-urlencoded<br/>
* 系统级参数 ***所有接口必须传系统级请求参数***
>|参数名|参数类型|必填|描述|
>|:-|:-|:-|:-|
>|appId|string|是|CoinRobots给开发者提供的appId|
>|apiKey|string|是|CoinRobots给用户提供的apiKey|
>|timestamp|string|是|时间戳|
>|sign|string|是|签名|
* sign签名算法
   * 将除签名sign以外的请求参数进行字典排序
   * 将请求参数值进行字符串拼接
   * 将拼接的字符串使用md5(32大写)进行签名
   * 将第一次签名的密文末尾加上secretKey(CoinRobots给用户提供的secretKey)
   * 对字符串再次进行md5(32小写)加密
* sign签名算法示例
```
请求参数
{
  "appId": "111",
  "apiKey": "333",
  "timestamp": "1510043875",
  "param": "test888",
  ......
}

排序之后
{
  "apiKey": "333",
  "appId": "111",
  "param": "test888",
  "timestamp": "1510043875"
  ......
}

拼接字符串
333111test8881510043875

md5(32大写)签名之后末尾增加secretKey(CoinRobots给用户提供的secretKey)
4C352484CDE66D12C6915A3B8A62D379dgsfgafgdsasfhwerh

将上述字符串进行md5(32小写)加密
02d340158aadbfcc1163dbfeb07b0af8
```
* 请求交互说明
   * 请求参数：根据接口请求参数规定进行参数封装。
   * 提交请求参数：将封装好的请求参数通过POST或GET方式提交至服务器。
   * 服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。
   * 数据处理：对服务器响应数据进行处理。
## API参考
### Ping
>测试接口服务器是否正常
#### 请求地址
>GET `https://open.coinrobots.com/api/Public/ping`
#### 请求参数（无）
#### 示例
```
# Request
GET https://open.coinrobots.com/api/Public/ping
# Response
{
  "status": "0",    // 状态码
  "msg": "ok",      // 描述
  "objData": null   // 返回业务参数实体
}
```
### 用户登录验证
>通过手机号或者邮箱及密码验证用户是否有效
#### 请求地址
>GET `https://open.coinrobots.com/api/User/loginVerify`
#### 请求参数
|参数名|参数类型|必填|描述|
|:-|:-|:-|:-|
|userName|string|是|手机号或邮箱|
|userPwd|string|是|密码(md5加密32位小写)|
#### 示例
```
# Request
GET https://open.coinrobots.com/api/User/loginVerify?appId=1&apiKey=2&userName=3&userPwd=4&timestamp=5&sign=6
# Response
{
  "status": "0",    // 状态码
  "msg": "ok",      // 描述
  "objData": {      // 返回业务参数实体
    "userId": 1234  // -  用户id
  }
}
```
