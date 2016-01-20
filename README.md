# 周末做啥Open Api

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
* 接口采用http服务，返回状态码借助http code，返回包体格式utf8,json格式
* 如果返回结果均使用Jsend格式的json
* error code list待完善...

### 目前接口
* 测试环境域名:  `https://open-test.xmonster.cn`
* 正式环境域名:  `https://open.xmonster.cn`

* 通知订单消费
	* 接口 `POST /api/v1/order/consume`
	* 类型 `Content-Type: application/json`
	* 参数
		* `order_id` 周末做啥的订单号
	* 结果 
	
	 ```
	 {
	     "status" : "success",
	     "data": {
	         "order_id": "1373260497454",
	         "order_state": 5
	     }
	 }
	```
	* 订单状态说明：
		   * 0 未支付
    		* 1 出票中
    		* 2 购票失败,请联系客服
    		* 4 购票成功
    		* 5 已消费


* 获取用户信息
	* 接口 `GET /api/v1/user`
	* 参数
		* `openid` 周末做啥给第三方的openid
		* `extra`  周末做啥给第三方的extra字符串，该信息需要在其他接口中回传给周末做啥
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
	* 接口 `PUT /api/v1/order`
	* 参数：
		* `order_id` 第三方订单编号
		* `openid` 周末做啥`获取用户信息`接口中的openid
		* `product_id` 第三方产品id
		* `price` 第三方产品价格
		* `status` 订单状态
		* `extra` 周末做啥`获取用户信息`接口中的extra信息
		* `ticket_type` 取票方式,电子票为0，快递票为1
		* `check_pass` 如果是电子票必填,验证码
		* `address` 如果是快递必填，快递地址
		* 订单状态说明：
		    * 0 未支付
    		* 1 出票中
    		* 2 购票失败,请联系客服
    		* 4 购票成功
	
	* 结果 
	
	 ```
	 {
	    "status": "success",
	    "data": {
	        "order_state": 1,
	        "details": "出票中"
	    }
	 }
	 ```