# restful api设计之swagger

swagger: "2.0"
info:
  title: 品牌到家联盟-周期购API
  description: 该接口文档用于周期购前后端开发数据约定，无特别说明，都是用该文档标准.
  version: 1.0.0
host: www.baidu.com
schemes:
  - HTTP
  - HTTPS
produces:
  - application/json
paths:
  /merchants/coupons/6:
    get:
      summary: 该接口用于获取商户发放的优惠券
      description: 该接口是以前存在的现有的接口，在周期购功能中，用于修正接口数据规范，name字段长度不超过8个字符。
      parameters:
        - name: user_id
          in: query
          description: 用户ID
          type: number
          required: true
          format: number
          default: 212501

        - name: count
          in: query
          description: 每次请求多少条信息
          type: number
          required: false
          format: number
          default: 2
      tags:
        - 老接口
      responses:
        "200":
          description: 请求成功
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
                  "url":"http://wxtest.kuaijiankang.cn/aaa.jpg",
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
                  "url":"http://wxtest.kuaijiankang.cn/aaa.jpg",
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
                  "url":"http://wxtest.kuaijiankang.cn/aaa.jpg",
                  "name":"商品的名字",
                  "count":24,
                  "type":3,
                  "order_time":"10:00-23:00",
                  "start_date":"2016-01-06",
                  "order_count":1,
                  "set_time":""
                }]
            }
  /goods:
    get:
      summary: 获取用户购买的周期够的单品
      description: 该接口用于获取用户购买的周期够的单品，在周期购功能中，增加商品类型用来判断商品是否为周期购商品。
      parameters:
        - name: user_id
          in: query
          description: 用户ID
          type: number
          required: true
          format: number
          default: 212501

        - name: goods_id
          in: query
          description: 商品ID
          type: number
          required: true
          format: number
          default: 127
      tags:
        - 老接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "result":{
                  "Goods":{
                      "goods_type":"cycle"
                  }
              }
            }

  /cycles/total_status:
    get:
      summary: 获取用户我的电子券信息
      description: 周期购需求中，需要在该接口中返回我的电子券信息
      parameters:
        - name: user_id
          in: query
          description: 用户ID
          type: number
          required: true
          format: number
          default: 212501
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "result":{
                "count":0,              # 电子券数量
                "cycle_delivery":0      # 周期购的今日配送
              },
              "code":200
            }

  /cycles/goods_list:
    get:
      summary: 该接口用于获取周期购商品列表信息
      description: 该接口用于获取周期购商品列表信息
      parameters:
        - name: home_show
          in: query
          description: 是否在首页展示，0不展示，1展示
          type: number
          default: 0
          required: true
          format: number

        - name: page
          in: query
          description: 页码
          type: number
          default: 1
          required: false
          format: number

        - name: count
          in: query
          description: "每次请求多少条信息"
          type: number
          required: false
          default: 50
          format: number
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "result":[{
                "id":126,
                "src": "http://stage.ams.kuaijiankang.cn/data/merchants/02800011/products/10001/p1.jpg",
                "full_name": "【一元抢相因】真酸奶 200g*1",
                "market_price": 500,
                "shop_price": 0,
                "shop_type": 1,
                "spec": "盒",
                "status": 1,
                "tags":"单个商品的tag描述"
              },{
                "id":126,
                "src": "http://stage.ams.kuaijiankang.cn/data/merchants/02800011/products/10001/p1.jpg",
                "full_name": "【一元抢相因】真酸奶 200g*1",
                "market_price": 500,
                "shop_price": 0,
                "shop_type": 1,
                "spec": "盒",
                "status": 1,
                "tags":"单个商品的tag描述"
              }],
              "message":"这个字段必须返回，用来在周期购页面顶部显示特别说明。"
            }

  /cycles/orders:
    post:
      summary: 该接口用于用户直接购买周期购商品
      description: 周期购中，前端需要单独传递类型给后端，后端在生成订单的时候需要标识订单类型，以方便前端在订单成功页面判断订单类型。
      parameters:
        - name: Auth-Token
          in: query
          description: 用户Token信息
          type: string
          required: true
          format: string

        - name: address_id
          in: query
          description: 用户地址ID
          type: number
          required: true
          format: string

        - name: goodsinfo
          in: query
          description: 购买的商品信息
          type: string
          required: true
          format: string
          enum:
            - 126|1,127|2

        - name: m_id
          in: query
          description: 商户的ID
          type: number
          required: true
          format: string
          enum:
            - 26

        - name: pay_type
          in: query
          description: 支付类型
          type: number
          required: true
          format: number
          enum:
            - 8

        - name: user_id
          in: query
          description: 用户ID
          type: number
          required: true
          format: number
          default: 212501

        - name: order_type
          in: query
          description: 订单类型，这里标记这个订单为周期购的订单
          type: string
          required: true
          format: string
          default: cycle
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "result":{
                  "amount":10000,
                  "sn":342423423432,
                  "id": 28, # 商户ID
                  "goodsinfo":"126|1"   # 需要单独确认一下
              },
              "message":"成功"
            }

  /cycles/setting:
    post:
      summary: 该接口用于设置用户设定的周期购的配送周期
      description: 该接口用于设置用户设定的周期购的配送周期.
      parameters:
        - name: Auth-Token
          in: query
          description: 用户Token信息
          type: string
          required: true
          format: string
          enum:
            - "b7162a3c3258616d"

        - name: user_id
          in: query
          description: 用户身份ID
          type: number
          required: true
          format: number
          default: 212501

        - name: type
          in: query
          description: 配送时间类型
          type: string
          required: true
          default: everyday
          enum:
              everyday -> 每天配送
              interval -> 隔天配送
              week -> 按礼拜选择
        - name: custom
          in: query
          description: 配送时间结果值
          type: string
          required: true
          enum:
              everyday -> ""
              interval -> "2" 隔2天配送
              week -> "1,3,5" 每周1，3，5配送
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "message":"设置配送周期成功！"
            }
    get:
      summary: 该接口用于获取用户设定的周期购的配送周期
      description: 该接口用于获取用户设定的周期购的配送周期.
      parameters:
        - name: user_id
          in: query
          description: 用户身份ID
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
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "result":{
                  "type":"week",      # 配送时间类型
                  "custom":"1,3,5"    # 配送时间结果值
              }
            }

  /cycles/calendar:
    get:
      summary: 该接口用于获取周期购中日历页面的相关信息
      description: 该接口用于获取周期购中日历页面的相关信息.
      parameters:
        - name: date
          in: query
          description: 传递[年份+月份]给后端,方便后端根据值来定位月份中的周期
          type: date
          required: true
          enum:
              - 2015-12
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "result":[
                {
                  "status":0,   # 0表示已经配送过了的单子
                  "day":3       # 返回的月份中，定位到3号这一天
                },
                {
                  "status":0,
                  "day":10
                },
                {
                  "status":1,   # 1表示还没配送过的单子
                  "day":14      # 返回的月份中，定位到14号这一天
                },
                {
                  "status":1,
                  "day":18
                }
              ]
            }

  /cycles/take_goods:
    get:
      summary: 该接口用于获取周期购中日历页面指定某一天的配送商品
      description: 该接口用于获取周期购中日历页面指定某一天的配送商品.
      parameters:
        - name: user_id
          in: query
          description: 用户身份ID
          type: number
          required: true
          format: number
          default: 212501

        - name: date
          in: query
          description: 传递年份给后端方便后端根据年份来定位月份中的周期
          type: date
          required: true
          format: date
          enum:
            - 2015-12-18
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "result":{
                  "best_time":"19:00 ~ 21:00",
                  ”items”:[{
                    "id":127,
                    "full_name":"真酸奶 200g*8盒/箱",
                    "market_price": 4000,
                    "shop_price":1000,
                    "number":1,
                    "cover": "http://stage.ams.kuaijiankang.cn/data/merchants/02800011/products/10002/p2.jpg",
                    "category_name":"鲜奶到家",
                    "amount":1000
                  }]
              }
            }

  /cycles/take_goods_del:
    post:
      summary: 该接口用于删除周期购中日历页面指定某一天的配送商品
      description: 该接口用于删除周期购中日历页面指定某一天的配送商品.
      parameters:
        - name: Auth-Token
          in: query
          description: 用户Token信息
          type: string
          required: true
          format: string
          enum:
            - "b7162a3c3258616d"

        - name: user_id
          in: query
          description: 用户身份ID
          type: number
          required: true
          format: number
          default: 212501

        - name: date
          in: query
          description: 传递年份给后端方便后端根据年份来定位月份中的周期。注意，如果取消周期购，那这个字段将不传
          type: date
          required: false
          format: date
          enum:
              - 2015-12-18

        - name: cycle_id
          in: query
          description: 商品ID
          type: number
          required: true
          format: number
          enum:
            - 2
        - name: m_id
          in: query
          description: 商户ID
          type: number
          required: true
          format: number
          enum:
            - 26
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "message":"删除成功"
            }

  /cycles/take_goods_update:
    post:
      summary: 该接口用于调整周期购中日历页面指定某一天的配送商品的数量
      description: 该接口用于调整周期购中日历页面指定某一天的配送商品的数量.
      parameters:
        - name: Auth-Token
          in: query
          description: 用户Token信息
          type: string
          required: true
          format: string
          enum:
            - "b7162a3c3258616d"

        - name: user_id
          in: query
          description: 用户身份ID
          type: number
          required: true
          format: number
          default: 212501

        - name: date
          in: query
          description: 传递年份给后端方便后端根据年份来定位月份中的周期
          type: date
          required: true
          format: date
          enum:
              - 2015-12-18

        - name: cycle_id
          description: 商品信息列表
          type: array
          required: true
          format: array
          enum:
            [{
              "id":187,
              "number":2
            },
            {
              "id":126,
              "number":4
            }]
      tags:
        - 新接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              "code":200,
              "message":"修改商品数量成功"
            }

  /merchants:
    get:
      summary: 该接口用于获取是否开启周期购功能
      description: 该接口是以前存在的现有的接口，周期购需求中，需要在该接口中增加一个状态字段表示周期购是否开启
      parameters:
        - name: user_id
          in: query
          description: 用户身份ID
          type: number
          required: true
          format: number
          default: 212501

        - name: short_url
          description: 商户名
          type: string
          required: true
          default: jule
          enum:
              - jule
      tags:
        - 老接口
      responses:
        "200":
          description: 请求成功
          examples:
            {
              code:200,
              result:{
                items:[{
                  is_allow_cycles:0 # 0表示没开启；1表示开启周期购功能
                }]
              }
            }
/orders:
  get:
    summary: 在订单列表页面，后端需要返回周期购订单状态
    description: 该接口是以前存在的接口，在订单列表页面，后端需要返回周期购订单状态
    parameters:
      - name: user_id
        in: query
        description: 用户身份ID
        type: number
        required: true
        format: number
        default: 212501

      - name: limit
        in: query
        description: 获取多少条
        type: number
        required: false
        format: number
        enum:
          - 12
    tags:
      - 老接口
    responses:
      "200":
        description: 请求成功
        examples:
          {
            "code":200,
            "result":{
              "items":[{
                order_type:cycle
              }],
              "total":1
            }
          }
