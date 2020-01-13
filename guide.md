## 接口描述
小程序登录接口，小程序登录使用该接口（传递数据包含加密数据和sessionkey）， 
说明：小程序的登录流程改为悠享流程，先授权后绑定手机号和邀请码（需要保留App继续维持不变）， 此接口小程序单独使用

## 接口地址

	* 测试 http://testapi.one1tree.com.cn/testapp/v1.1/user/optimize/wechat/login
	* 生产 https://api.one1tree.com.cn/app/v1.1/user/optimize/wechat/login

## 请求方式
POST

## 请求头
名称 | 类型 | 描述 | 是否必须
---|---|---|---
App-Token | string | 当前登录用户 token，如果未登录传空 | 否
App-Type | int | 客户端类型（小程序传 3 ） | 是
App-Space | string | 域字符串 | 是

## 请求参数
名称 | 类型 | 描述 | 是否必须
---|---|---|---
encryptedData | string | 微信登录成功后返回的数据（加密）| 是
iv | string | 微信加密数据所使用的向量 | 是
shareid | int | 分享人 ID | 是
sessionKey|string|当前获取的sessionKey|是
## 请求的参数示例
``` json
{
  "encryptedData":"0XABtiBlfQd8auFZBaRvpVYIX0p47n+9khbaHIYMVUwCg1jghd5jRspmnvKqY05nBinNIPg5WBafaNnAGzp86wVMtyfHkLERRjEY3eGB19xYesnJPW+gvc4E/ZZbQVHsOcHsYEKv6DrO4ym5hXXgalGAA3VHnAwtBKWUuJdK6MUy12h07liurFj1PckXPnCNEIEb2WDufzbc4zxChqdoE1+QcjuuiVsABsChYkSBqXiCY7Avx4oQVlbgL/KMORgYp4A433jUqDv/yawiWug2hIG3QLXMMn7W2bNWNJUp4z4ReVqHP0GpE2dCEyrOYeW2YjTGW9kEqq1mU0uKu1cPQz7408H0ZJ781pirZLArcqZlZKEQ1uEb6tAs5VK9AZq41lD4MNpcaAjaL6eQBAyMJlmOYYntpsexPrKjaDtKWj7Uye0GxckHvtuthm0ytipgKbZCa0qqJi1pRBAp4mEZB9kAPtLmDqgZJAc99cHrG8k1pNK8aUp6hs4Jjg3bDMW3q66BmHFpayG2VHetgXQJTjxVx/ib8NF5jK0ZWR1We3g=",
	"shareid":0,
	"iv":"JR6qCRqYlfecgm9XOjH6fQ==",
	"sessionKey":"Mk2QlBRyFt39DfTNzg6hJQ=="
}
```
## 成功时返回值
```json
{
  "data": {
    "userid": 115869,
    "userId": 115869,
    "status": 1,
    "token": "",
		"createTime":"2019-01-01 12:00:00"
  },
  "code": 200,
  "msg": "登录成功！",
  "success": false
}
```
## 返回值字段说明
名称 | 类型 | 是否必须 | 描述
---|---|---|---
userid|int|是|用户ID（注意，该字段不符合命名规范，将来会被迭代掉，请使用userId）
userId|int|是|用户ID
status|int|是|用户是否已经绑定手机号和推荐人；0：未绑定，需要引导用户去进入绑定页面；1：已绑定，直接登陆成功，进入业务页面
token|string|是|令牌，登录成功之后，所有的接口访问，都应该增加该Header（App-Token）进行访问
createTime|datetime|是|当前用户的注册时间