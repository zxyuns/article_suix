# restful api设计之swagger

## 摘要
Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。

## 快速入门

```dash
# swagger版本号
swagger: "2.0"

# 接口文档的基本描述
info:
  # 文档的标题
  title: 品牌到家联盟-周期购API

  # 文档的基本描述
  description: 该接口文档用于周期购前后端开发数据约定，无特别说明，都是用该文档标准.

  # 文档的版本
  version: 1.0.0

# API根
host: www.baidu.com

# 设置传输协议
schemes:
  - HTTP
  - HTTPS

# 设置HTTP的MIME类型
produces:
  - application/json

# 所有API从这里开始配置
paths:
  
  # API的路径
  /merchants/coupons/6:

    # 请求方式
    get:

      # 简要描述，在swagger-ui 这种工具中显示在折叠菜单的标题栏上
      summary: 该接口用于获取商户发放的优惠券

      # 当前接口更详细的描述
      description: 该接口是以前存在的现有的接口，在周期购功能中，用于修正接口数据规范，name字段长度不超过8个字符。

      # 要发送的参数列表
      parameters:

        # 参数的格式以 - 开始
          # 参数名
        - name: user_id 
          
          # 必须的参数，用来说明在这个接口中用来干嘛的，常用的取值有： "query", "header", "path", "formData", "body"
          in: query 
          
          # 字段描述
          description: 用户ID 
          
          # 数据类型 常用的有 "string", "number", "integer", "boolean", "array" or "file"
          type: number 
          
          # 是否必须
          required: true 
          
          # 在swagger-ui 这种工具中用来校验类型
          format: number 
          
          # 默认值
          default: 212501 

        - name: count
          in: query
          description: 每次请求多少条信息
          type: number
          required: false
          format: number
          default: 2
      # 打一个标记
      tags:
        - 老接口

      # 响应内容
      responses:
        
        # HTTP状态码
        "200":

          # 响应描述
          description: 

          # 响应例子
          examples:
            {
              "code":200,
              "result":[{
                "category": "buy_full",
                "cou_status": 1,
                "desc": "描述信息",
                "discount_amount": 1000,
                "end_time": "2015-12-24",
                "id": 102,
                "m_id": 6,
                "name": "不超过8个字符",
                "start_time": "2015-12-07"
              },{
                "category": "buy_full",
                "cou_status": 1,
                "desc": "描述信息",
                "discount_amount": 1000,
                "end_time": "2015-12-24",
                "id": 103,
                "m_id": 6,
                "name": "不超过8个字符",
                "start_time": "2015-12-07"
              }]
            }
        # 默认响应
        default:
          description: "结果值"

  /cycles/user_cycles_list:
    get:
      summary: 用户的周期购的电子券列表信息
      description: 该接口用于获取用户的周期购的电子券列表信息，包括电子券的状态信息：未设置配送周期、无剩余数量、和设置过配送周期等信息。
      parameters:
        - name: user_id
          in: query
          description: 用户ID
          type: number
          required: true
          format: number
          default: 212501

        - name: id
          in: query
          description: 周期购ID
          type: number
          required: false
          format: number

        - name: page
          in: query
          description: 页码
          type: number
          required: false
          format: number

        - name: limit
          in: query
          description: 取多少条
          type: number
          required: false
          format: number
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "message":"说明电子券的使用规则，规则请见需求说明",
              "result":[{
                  # 未设置配送周期
                  "id":1,
                  "url":"http://www.baidu.com/aaa.jpg",
                  "name":"商品的名字",
                  "count":154,
                  "type":1,
                  "order_time":"10:00-23:00",   #配送时间
                  "start_date":"2016-01-06",    #起配时间
                  "order_count":1,              #配送个数
                  "set_time":""                 #配送时间设置(默认每天配送)
                },
                {
                  # 剩余张数为0
                  "id":2,
                  "url":"http://www.baidu.com/aaa.jpg",
                  "name":"商品的名字",
                  "count":0,
                  "type":2,
                  "order_time":"10:00-23:00",
                  "start_date":"2016-01-06",
                  "order_count":1,
                  "set_time":""
                },
                {
                  # 已经设置了配送周期
                  "id":3,
                  "url":"http://www.baidu.com/aaa.jpg",
                  "name":"商品的名字",
                  "count":24,
                  "type":3,
                  "order_time":"10:00-23:00",
                  "start_date":"2016-01-06",
                  "order_count":1,
                  "set_time":""
                }]
            }
```

## 特别说明
在前后端分离模式下开发的时候，接口文档对开发人员非常重要！！！说小点就是关系到项目进度的事情，说大了就是关系到出问题时候扯皮的事情，所以业内很多同行都强烈建议保证接口文档的完整性。所以在日常开发中，在定义接口文档的时候，前端，测试，开发，三者都应该非常了解接口文档。

## 快速生成接口文档
首先把编写好的接口文档命名为**xxx.yaml**然后放到自己的项目中,我是放到项目根目录下的api文件夹中，如果您使用的IDE可以检测YAML基本语法当然更好，不过只能作为参考，具体校验方法还的用官方的(ps:被其他校验工具坑哭过)。官方的校验工具最直观的是这个[官方在线编辑器](http://editor.swagger.io/ "点这里")可以把刚刚编写的接口文档粘贴到左边区域，然后就能实时校验了，编写完毕之后，点击**Generate Server**或者**Generate Client**来下载相应语言或者相应版本的代码。当然也可以点击File>Download JSON把编辑的文档下载下来，然后使用github上[官方的UI](https://github.com/swagger-api/swagger-ui "点这里")在自己测试服务器上部署，部署成功之后上传刚刚下载的swagger.json文件到项目的dist下面替换掉swagger.json文件，然后修改dist目录下index.html中38行的请求文件路径为自己刚刚上传的swagger.json文件即可，为了访问的方便，最好给文档配置一个域名，把路径指向到dist下面就可以了。