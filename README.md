# emqx-api-guidelines

EMQ X RESTful API Guidelines

## EMQX Broker

### Response Format

Response Status Code: 200 / 400 / 401

Response Body: JSON Object

Response Body 目前支持以下字段：

- code, integer()

  具体业务返回的错误码，非0值表示错误，此字段不可省略

- message, string()

  错误原因的详细描述，code为0时此字段可以省略
  
- items, [JSON Object()]

  用于返回详细数据，在可能存在多条数据时使用
  
- item, JSON Object()

  用于返回详细数据，在只可能返回单条数据时使用
  
- meta, JSON Object()

  用于返回分页的信息，例如：
  
  ```
  	"meta" : {
  		"count" : 1,
  		"limit" : 1000,
  		"page" : 1
  	}
  ```

### Examples

#### 获取节点监控数据

返回结果只可能存在一条数据，因此使用 item 字段，并且省略 message 字段

```
{
	"code" : 0,
	"item": {
		"connections": 2,
		"load1": "2.75",
		"load15": "2.87",
		"load5": "2.57",
		"max_fds": 7168,
		"memory_total": 80162816,
		"memory_used": 62254160,
		"name": "emqx@127.0.0.1",
		"node_status": "Running",
		"otp_release": "R21/10.0.5",
		"process_available": 262144,
		"process_used": 331,
		"uptime": "1 days,18 hours, 45 minutes, 1 seconds",
		"version": "3.0"
	}
}
```

#### 获取集群订阅信息

返回结果中可能存在多条数据，且此请求支持分页，因此使用 items 和 meta 字段

```
{
	"code" : 0,
	"items" : [
		{
			"client_id": "emqx-api-test:v1",
			"node": "emqx@127.0.0.1",
			"qos": 0,
			"topic": "/test"
		},
		{
			"client_id": "mqttjs_406e3f9a",
			"node": "emqx@127.0.0.1",
			"qos": 0,
			"topic": "/test"
		}
	],
	"meta": {
		"count": 2,
		"limit": 10000,
		"page": 1
	}
}
```
### Error Codes

| Error Code | Description                     |
| :--------- | :------------------------------ |
| 0          | Success                         |
| 101        | Bad RPC                         |
| 102        | Unknown Error                   |
| 103        | Username or password error      |
| 104        | Empty username or password      |
| 105        | User does not exist             |
| 106        | Admin can not be deleted        |
| 107        | Missing request parameter       |
| 108        | Request parameter type error    |
| 109        | Request parameter is not a json |
| 110        | Plugin has been loaded          |
| 111        | Plugin has been unloaded        |
| 112        | Client not online               |
| 113        | User already exist              |
| 114        | Old password error              |
| 115        | Bad topic                       |

## ActorCloud

### Error

- 普通错误

```
{
	"message": "Permission Denied"
}
```

- 字段验证错误

```
{
	"message": "Validation Failed"
	"errors": [
		{
			"field": "title",
			"message": "missing field"
		}
	]
}
```


### GET

#### 单个资源的详情直接返回对象

`/products/1`

```
{
    "id": 1,
    "productName": "product_1",
    "productID": "id7126289352",
    "productIndustry": "电力",
    "productCat": "门锁",
    "productFactory": "西门子",
    "productType": "MG-23530",
    "connetType": [
        "4G"
    ],
    "createAt": "2017-01-15 07:03:44",
    "updateAt": "2017-01-22 08:15:59"
}
```

### 分页

#### 1. 支持分页的 API 都需要返回分页 meta，不支持分页的 API 如果可能有多条数据就返回列表（列表外不需要嵌套 item），否则直接返回对象。

#### 2. 分页 URL : `/products?_page=7&_limit=20`，返回样例如下：

```
{
  "meta": {
    "limit": 10, 
    "page": 1, 
    "count": 7
  }, 
  "items": [
    {
      "cloudProtocol": [], 
      "connetType": [], 
      "createAt": "2017-10-09 13:22:17", 
      "developState": null, 
      "id": 17, 
      "osName": null, 
      "osType": null, 
      "privateProtocol": null, 
      "productCat": null, 
      "productDes": null, 
      "productFactory": null, 
      "productIndustry": 3, 
      "productKey": "d12e68a8acb111e7a872d0a637ed3757", 
      "productLocal": null, 
      "productName": "111", 
      "productPhoto": [], 
      "productStatus": 3, 
      "productType": null, 
      "productUUID": "d12e1f9eacb111e79560d0a637ed3757", 
      "registerCode": "d12e69b8acb111e79b7fd0a637ed3757", 
      "updateAt": "2017-10-09 13:27:14"
    }, 
  ]
}
```

### V2 版本 API 更改讨论结果

1. `/api/v2/management/nodes` 与 `/api/v2/monitoring/nodes` 合并为： `/api/v2/nodes`

2. `/api/v2/management/nodes/{node_name}` 改为 `/api/v2/nodes/{node_name}`

3. `DELETE /api/v2/clients/{clientID}/clean_acl_cache/` 改为 `DELETE /api/v2/clients/{clientID}/acl/{topic}`


### 遗留问题接口

1. POST /api/v2/mqtt/publish

2. POST /api/v2/mqtt/subscribe

3. POST /api/v2/mqtt/unsubscribe

4. 全局的 clients 查询与节点的 clients URL 如何命名？

- 全局：`/api/v2/clients/{clientID}`，节点：`/api/v2/nodes/{nodeName}clients/{clientID}`

- 全局：`/api/v2/clients?clientID={clientID}`，节点：`/api/v2/clients?node={nodeName}&clientID={clientID}`



	
	
	