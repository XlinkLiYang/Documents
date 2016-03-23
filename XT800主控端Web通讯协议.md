# XT800远控组件Web通讯协议

## 说明


## 接口设计

### 实现方式

* Web通过Ajax的get请求和本地进行通讯
* 对于Web的跨域问题，使用jsonp的callback方式实现
	* jsonp的callback名称统一用："**callback**"
* XTService.exe监听本地的固定端口，该端口作为web服务通讯端口，固定未22899（21899原有的XT800程序在使用）

### 接口定义

* 接口概览
	1. 获取应用程序版本
	2. 启动XTService程序
	3. 远程控制 

### 1. 获取应用程序版本

#### 说明

* 用于获取当前安装的应用程序版本；
* 同时检测XT程序有没有在本机安装；

#### *Request*

URL

```
GET /v1/xts?opt=getVersion
```

Header

```
Content-Type:"application/json"
```

#### *Response*

Header

```
HTTP/1.1 200 OK
```

Content

```
{
	"app" : "XTService_Viewer",
	"version" : "",
	"uid" : "UID",
	"pwd" : "PASSWORD",
	"apprun" : false,
	"logined" : false
}
```

| 字段 | 是否必须 | 描述 |
| --- | --- | --- |
| app  | 是 | 程序名称，暂时统一XTService，以后不同的类型待定。 |
| version | 是 | 程序版本，1.0.0.1。 |
| uid | 否 | 登录到平台的UID，如果用户未注册，则没有该值。 |
| pwd | 否 | 用户登录到平台的PWD，如果用户未注册，则没有该值。 |
| apprun | 是 | 应用程序是否已经启动 |
| logined | 是 | 应用程序是否已经登录 |

### 2. 启动XTService程序

#### 说明

* Web调用这个接口用来启动XTService程序，并且**使用指定的帐号密码登录**;

#### *Request*

URL

```
GET /v1/xts?opt=appStartup&minversion=1.0.0.1&act=login&uid=10001&chk=password
```

Header

```
Content-Type:"application/json"
```

| 字段 | 是否必须 | 描述 |
| --- | --- | --- |
| minversion | 是 | 要求程序运行的最小版本，若版本太低，XTService需要返回对应错误，Web会提醒用户进行升级动作。 |
| act | 否 | APP启动以后的动作，login表示程序登录，其他指令待定。若act字段没有，表示程序直接拿缓存的用户ID和密码自动登录。 |
| uid | 否 | 当act为login时，uid会指定需要登录的用户ID。 |
| chk | 否 | 当act为login时，chk会指定需要登录的用户的password。 |

#### *Response*

Header

```
HTTP/1.1 200 OK
```

Content

```
{
	"ret" : 0,
	"msg" : "ok",
	"version" : "current app version",
	"app" : "XTService",
	"uid" : "登录用户ID",
	"name" : "用户昵称",
	"token" : "用户Web调用秘钥",
	"logined" : true
}
```
| 字段 | 是否必须 | 描述 |
| --- | --- | --- |
| ret | 是 | 调用结果，0为成功，其他失败，见附录。|
| msg | 是 | 调用结果消息。 |
| version | 是 | 当前APP的版本。 |
| app | 是 | APP类型，固定XTService。 |
| uid | 是 | 登录用户ID。 |
| name | 是 | 用户昵称。|
| token | 是 | APP返回的给Web进行其他操作的调用秘钥。|
| logined | 是 | APP是否已经登录。 |


### 3. 远程控制

#### *Request*

URL

```
GET /v1/xts?opt=remoteControl&from=FromID&to=ToID&authcode=AUTHCODE&viewonly=false&sign=Signature
```

| 字段 | 是否必须 | 描述 |
| --- | --- | --- |
| from | 是 | 发起者ID，也即是当前登录的用户ID，这个字段主要是为了保证Web请求的发起者和本地登录的用户，是同一个用户。 |
| to | 是 | 目标用户ID。 |
| 

Header

```
Content-Type:"application/json"
```

#### *Response*

Header

```
HTTP/1.1 200 OK
```

Content

```
{
	"ret" : 0,
	"msg" : "ok",
}
```

### 附录

#### 通用错误码定义

