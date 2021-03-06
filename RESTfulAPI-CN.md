# RESTful API for CoinRobots
## 请求交互
- RESTful访问的根URL：`http://open.coinrobots.com/api/v1/`
- 所有请求基于Http协议，请求头信息中contentType需要统一设置为：application/x-www-form-urlencoded
- 系统级参数 ***私有接口必须传系统级请求参数***
> |参数名|参数类型|必填|描述|
> |:-|:-|:-|:-|
> |appId|string|是|CoinRobots给开发者提供的appId|
> |apiKey|string|是|CoinRobots给用户提供的apiKey|
> |timestamp|string|是|时间戳（与服务器时间3分钟之内的秒级时间戳(10位)）|
> |sign|string|是|签名|
- sign签名算法
   - 将除签名sign以外的请求参数进行字典排序
   - 将请求参数值进行字符串拼接
   - 将拼接的字符串使用md5（32大写）进行签名
   - 将第一次签名的密文末尾加上secretKey（CoinRobots给用户提供的secretKey）
   - 对字符串再次进行md5（32大写）加密
- sign签名算法示例
```c
# 请求参数
{
	"appId": "111",
	"apiKey": "333",
	"timestamp": "1510043875",
	"param": "test888",
	......
}

# 排序之后
{
	"apiKey": "333",
	"appId": "111",
	"param": "test888",
	"timestamp": "1510043875"
	......
}

# 拼接字符串
333111test8881510043875

# md5（32大写）签名之后末尾增加secretKey（CoinRobots给用户提供的secretKey）
4C352484CDE66D12C6915A3B8A62D379dgsfgafgdsasfhwerh

# 将上述字符串进行md5（32大写）加密
02d340158aadbfcc1163dbfeb07b0af8
```
- 请求交互说明
   - 请求参数：根据接口请求参数规定进行参数封装。
   - 提交请求参数：将封装好的请求参数通过POST或GET方式提交至服务器。
   - 服务器响应：服务器首先对用户请求数据进行参数安全校验，通过校验后根据业务逻辑将响应数据以JSON格式返回给用户。
   - 数据处理：对服务器响应数据进行处理。

## API参考（公共）

### 1. Ping
> 测试接口服务器是否正常
#### 请求地址
> GET `http://open.coinrobots.com/api/v1/Public/ping`
#### 请求参数（无）

#### 示例
```c
# Request
GET http://open.coinrobots.com/api/v1/Public/ping
# Response
{
	"status": 0,      // 状态码
	"msg": "ok",      // 描述
	"objData": null   // 返回业务参数实体
}
```

### 2. 获取CoinRobots支持平台
> 获取CoinRobots支持的第三方平台
#### 请求地址
> GET `http://open.coinrobots.com/api/v1/Public/getTerrace`
#### 请求参数（无）

#### 示例
```c
# Request
GET http://open.coinrobots.com/api/v1/Public/getTerrace
# Response
{
	"status": 0,      // 状态码
	"msg": "ok",      // 描述
	"objData": [	  // 返回业务参数实体
		{
			"terraceId": 1,		// -  平台id
			"terraceName": "OKEx"	// -  平台名称
		},
		{
			"terraceId": 2,		// -  平台id
			"terraceName": "火币"   // -  平台名称
		},
		......
	]
}
```

### 3. 获取平台支持的市场（计价币种）
> 通过平台id获取所支持的市场（平台可用的计价币种）
#### 请求地址
> GET `http://open.coinrobots.com/api/v1/Public/getMarket`
#### 请求参数
|参数名|参数类型|必填|描述|
|:-|:-|:-|:-|
|terraceId|int|是|平台id|

#### 示例
```c
# Request
GET http://open.coinrobots.com/api/v1/Public/getMarket?terraceId=1
# Response
{
	"status": 0,      // 状态码
	"msg": "ok",      // 描述
	"objData": {	  // 返回业务参数实体
		"market": [   // -  支持的市场（计价币种）
			"USDT",
			"BTC",
			"ETH",
			......
		]
	}
}
```

## API参考（私有）

### 1. 用户登录验证
> 通过手机号或者邮箱及密码验证用户是否有效
#### 请求地址
> POST `http://open.coinrobots.com/api/v1/User/loginVerify`
#### 请求参数
|参数名|参数类型|必填|描述|
|:-|:-|:-|:-|
|userName|string|是|手机号或邮箱|
|userPwd|string|是|密码（md5加密32位小写）|

#### 示例
```c
# Request
POST http://open.coinrobots.com/api/v1/User/loginVerify
# Response
{
	"status": 0,      // 状态码
	"msg": "ok",      // 描述
	"objData": {      // 返回业务参数实体
		"userId": 1234  // -  用户id
	}
}
```

### 2. 获取用户信息
> 通过用户id获取用户的一些信息
#### 请求地址
> POST `http://open.coinrobots.com/api/v1/User/getUserInfo`
#### 请求参数
|参数名|参数类型|必填|描述|
|:-|:-|:-|:-|
|userId|int|是|用户id|

#### 示例
```c
# Request
POST http://open.coinrobots.com/api/v1/User/getUserInfo
# Response
{
	"status": 0,    // 状态码
	"msg": "ok",    // 描述
	"objData": {    // 返回业务参数实体
		"userName": "1234",         // -  用户名
		"phone": "17600000000",     // -  手机号
		"email": "abc@yeah.net",    // -  邮箱
		"robotAddress": "0x3ef...", // -  robot地址
		"robotBalance": "88.6712"   // -  robot余额
	}
}
```

### 3. 提交手续费比例
> 提交平台该市场（计价币种）的手续费比例，传参和加密时apiKey和secretKey默认为空
#### 请求地址
> POST `http://open.coinrobots.com/api/v1/User/submitPoundage`
#### 请求参数
> 例：OKEx平台消耗1BTC扣除0.01个ROBOT `{ "terraceId": 1, "market": "BTC", "poundage": 0.01}`

|参数名|参数类型|必填|描述|
|:-|:-|:-|:-|
|terraceId|int|是|平台id|
|market|string|是|市场（计价币种）|
|poundage|decimal(18,8)|是|手续费比例（每消耗1个计价币种需扣除的ROBOT数量）|

#### 示例
```c
# Request
POST http://open.coinrobots.com/api/v1/User/submitPoundage
# Response
{
	"status": 0,    // 状态码
	"msg": "ok",    // 描述
	"objData": null // 返回业务参数实体
}
```

### 4. 扣除用户手续费
> 用户策略成功后，根据策略的计价币种进行扣费
#### 请求地址
> POST `http://open.coinrobots.com/api/v1/User/deductionPoundage`
#### 请求参数
|参数名|参数类型|必填|描述|
|:-|:-|:-|:-|
|userId|int|是|用户id|
|terraceId|int|是|平台|
|market|string|是|市场（计价币种）|
|amount|decimal(18,8)|是|成交数量|
|price|decimal(18,8)|是|成交均价|

#### 示例
```c
# Request
POST http://open.coinrobots.com/api/v1/User/deductionPoundage
# Response
{
	"status": 0,    // 状态码
	"msg": "ok",    // 描述
	"objData": {	// 返回业务参数实体
		"robotBalance": "66.98428321" // -  robot余额
	} 
}
```
