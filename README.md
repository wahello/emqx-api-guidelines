# emqx-api-guidelines
EMQ X RESTful API Guidelines


## Error

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


## GET

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



	
	
	