## 周末做啥Open Api

### 前置条件
*	周末做啥分配给第三方appid和appkey

### 访问方法
* 必带参数
	* appid
	* timestamp(当前时间戳)
	* nouce(随机串)
	* signature(签名，签名方法见后)
	* 其他业务参数
	
* 签名方法
	* 将所有请求参数（不包括signature）加上appkey按照参数名称字典升序排列，用&符号连接，得到如appid=xxx&appkey=xxx&key1=val1的连接字符串
	* 对上述字符串进行sha1加密得到signature
	* utf8编码
	
###结果返回

所有返回均为Jsend格式，[http://labs.omniti.com/labs/jsend](http://labs.omniti.com/labs/jsend)

类型      | 描述                             | 必须的key        | 可选的key
---------| ---------------------------------| ----------------|------------
success  | 成功                              | status, data    |
fail     | 输入参数错误或不满足API调用的预设条件  | status, data    |
error    | 后台发生错误                       | status, message | code ,data

####success
```
{
    status : "success",
    data : {
        "post" : { "id" : 1, "title" : "A blog post", "body" : "Some useful content" }
     }
}
```

####fail
```
{
    "status" : "fail",
    "data" : { "title" : "A title is required" }
}
```
 Required keys:

status: 返回'fail'
data: 用来返回输入错误的具体原因，我们使用参数名作为key,原因为value的KV来作为结果返回给调用者。
    
####error
```
{
    "status" : "error",
    "message" : "Unable to communicate with database",
    "code": "5000"
}
```


### 统一错误码
* 接口采用http服务，返回状态码200，utf8,json格式
* 如果返回结果均使用Jsend格式的json
* error code list待完善...

### 目前接口
* 测试环境域名: 待定
* 正式环境域名: 待定

* 获取用户信息
	* 接口 `GET /openapi/userinfo`
	* 参数 `user_id` 周末做啥给第三方的user_id
	* 结果 
	
	 ```
	 {
	     "status" : "success",
	     "data": {
	         "id": "12",
	         "mobile": "138xxxxxxx"
	     }
	 }
	```
	
* 更新订单信息
	* 接口 `POST /openapi/orders`
	* 参数：
		* `order_id` 第三方订单编号
		* `user_id` 第三方在获取用户信息中得到的`id`
		* `product_id` 第三方产品id
		* `price` 第三方产品价格
		* `status` 第三方订单状态
		* 状态说明：
			* 0 订单提交，等待付款
			* 1 已付款，未确认
			* 2 已确认
			* 3 已完成（已消费）
			* 4 用户取消
			* 5 商户取消（订单已满之类）
	
	* 结果 
	```
	{
	    "status": "success",
	    "data": {
	        "order_stat": 0,
	        "details": "订单提交，等待付款"
	    }
	}
	```