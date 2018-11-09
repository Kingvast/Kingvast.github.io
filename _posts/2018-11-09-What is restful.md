---
layout:     post                      # 使用的布局（不需要改）
title:      RESTful架构个人理解        # 标题
subtitle:   分享一些个人的看法          # 副标题
date:       2018-11-09                 # 时间
author:     甚了期                     # 作者
header-img: img/post-bg-181109.jpg    # 这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               # 标签
    - RESTful
---
## 1. 什么是REST
简单的说：RESTful是一种架构的规范与约束、原则，符合这种规范的架构就是RESTful架构。
REST全称是Representation State Transfer，中文意思是表述性状态转移，其实就是对资源的表述性状态转移。
资源的地址在web中就是URL（统一资源标识符）。
资源是REST系统的核心概念，所有的设计都是以资源为中心。

结合项目怎么识别资源：
1. 商品加入购物车   购物车
2. 提交订单        订单
3. 创建用户        用户

## 2. 理解RESTful
### 2.1 统一资源接口
分为四个子约束：
1. 每个资源都拥有一个资源标识，每个资源的资源标识可以用来唯一地标明该资源
2. 消息的自描述性
3. 资源的自描述性。
4. HATEOAS Hypermedia As The Engine Of Application State(超媒体作为应用状态引擎)

    即客户只可以通过服务端所返回各结果中所包含的信息来得到下一步操作所需要的信息，如到底是向哪个URL发送请求等。也就是说，一个典型的REST服务不需要额外的文档标示通过哪些URL访问特定类型的资源，而是通过服务端返回的响应来标示到底能在该资源上执行什么样的操作

    目的：实现客户端无需借助任何文档即能调用到所有的服务器资源
![](https://images2018.cnblogs.com/blog/1327924/201806/1327924-20180628115016658-1660690981.png)

RESTful架构应该遵循统一接口原则，统一接口包含了一组受限的预定义的操作，不论什么样的资源，都是通过使用相同的接口进行资源的访问。接口应该使用标准的HTTP方法如GET，PUT和POST，并遵循这些方法的语义。

如果按照HTTP方法的语义来暴露资源，那么接口将会拥有安全性和幂等性的特性，例如GET和HEAD请求都是安全的， 无论请求多少次，都不会改变服务器状态。而GET、HEAD、PUT和DELETE请求都是幂等的，无论对资源操作多少次， 结果总是一样的，后面的请求并不会产生比第一次更多的影响。

下面列出了GET，DELETE，PUT和POST的典型用法:
#### GET
- 安全且幂等
- 获取表示
- 变更时获取表示（缓存）
- 200（OK） - 表示已在响应中发出
- 204（无内容） - 资源有空表示
- 301（Moved Permanently） - 资源的URI已被更新
- 303（See Other） - 其他（如，负载均衡）
- 304（not modified）- 资源未更改（缓存）
- 400 （bad request）- 指代坏请求（如，参数错误）
- 404 （not found）- 资源不存在
- 406 （not acceptable）- 服务端不支持所需表示
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务端当前无法处理请求
#### POST
- 不安全且不幂等
- 使用服务端管理的（自动产生）的实例号创建资源
- 创建子资源
- 部分更新资源
- 如果没有被修改，则不过更新资源（乐观锁）
- 200（OK）- 如果现有资源已被更改
- 201（created）- 如果新资源被创建
- 202（accepted）- 已接受处理请求但尚未完成（异步处理）
- 301（Moved Permanently）- 资源的URI被更新
- 303（See Other）- 其他（如，负载均衡）
- 400（bad request）- 指代坏请求
- 404 （not found）- 资源不存在
- 406 （not acceptable）- 服务端不支持所需表示
- 409 （conflict）- 通用冲突
- 412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
- 415 （unsupported media type）- 接受到的表示不受支持
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务当前无法处理请求
#### PUT
- 不安全但幂等
- 用客户端管理的实例号创建一个资源
- 通过替换的方式更新资源
- 如果未被修改，则更新资源（乐观锁）
- 200 （OK）- 如果已存在资源被更改
- 201 （created）- 如果新资源被创建
- 301（Moved Permanently）- 资源的URI已更改
- 303 （See Other）- 其他（如，负载均衡）
- 400 （bad request）- 指代坏请求
- 404 （not found）- 资源不存在
- 406 （not acceptable）- 服务端不支持所需表示
- 409 （conflict）- 通用冲突
- 412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）
- 415 （unsupported media type）- 接受到的表示不受支持
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务当前无法处理请求
#### DELETE
- 不安全但幂等
- 删除资源
- 200 （OK）- 资源已被删除
- 301 （Moved Permanently）- 资源的URI已更改
- 303 （See Other）- 其他，如负载均衡
- 400 （bad request）- 指代坏请求
- 404 （not found）- 资源不存在
- 409 （conflict）- 通用冲突
- 500 （internal server error）- 通用错误响应
- 503 （Service Unavailable）- 服务端当前无法处理请求

### 2.2 资源的URL设计
1. 通过URL来表示资源
    资源分为主资源与子资源

    因为主资源是一类独立的资源 所以主资源应直接放在相对路径下：例如

    若要表示主资源的实例：如果实例的ID=1，则这样表示： ```/goods/1```

    子资源：

    一个实例的子资源可能是一个集合也可能是一个单一的子资源

    子资源为图片集合：```/goods/1/pictures```

    子资源为商品折扣的单子子资源：```/goods/1/discount```
2. 单数 vs 复数

    获取用户1的信息，哪种方式更符合RESTful?
    ```
    /api/users/1
    ```
    ```
    /api/user/1
    ```
3. 相对路径 vs 绝对路径
    
    极光的RESTful API:

    获取用户信息 ```GET /v1/users/{username}``` 参数放在路径中

    VS

    获取用户信息``` GET /v1/users?username=xxxxx```` 拼接的方式
 
    获取应用管理员列表 ```GET /v1/admins?start={start}&count={count} ？后拼接参数```的方式：这种方式一般作为过滤资源

4. 使用合适的动词get delete put post
    
    选择请求接口的方式： get delete。

    PUT 在服务器更新资源（客户端提供改变后的完整资源）。

    POST 在服务器新建一个资源。

5. 使用上述中标准的状态码

6. 选择适当的表示结构
    - json
    
    ![](http://www.runoob.com/wp-content/uploads/2015/07/291731048886033.jpg)
    
    - xml
    
    ![](http://www.runoob.com/wp-content/uploads/2015/07/291731045756062.jpg)
    
7. 版本控制
    ```
    http://api.example.com/1.0/foo
    http://api.example.com/1.2/foo
    http://api.example.com/2.0/foo
    ```
