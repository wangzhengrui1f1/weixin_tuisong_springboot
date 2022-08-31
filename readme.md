* 安装jdk环境
[百度的挺详细的，应该没问题](https://blog.csdn.net/weixin_43583223/article/details/123296163)

* [登陆微信的测试公众平台](http://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)
    1. 拿到appID和appsecret
    2. 关注测试公众号，拿到用户微信账户（微信用户openId）
    3. 新增测试模板 
       1. 模板标题：懒猪猪的每天提醒（可以随便输）
       2. 早上模板内容：
       ```
       {{title.DATA}}
       今天是我们认识的第{{day.DATA}}天
       我希望今天的你心情是 {{state.DATA}}
       你的城市：{{city.DATA}}
       今天天气：{{dayWeather.DATA}}
       今天温度：{{daytemp.DATA}}
       你的幸运颜色：{{color.DATA}}
       {{constellationName.DATA}}的你今天的爱情指数是 {{love.DATA}}
       速配星座：{{friend.DATA}}
       今天的你:{{summary.DATA}}
       {{chp.DATA}}
       {{du.DATA}}
       ```
       3. 添加就能获取到模板ID
       4. 添加分页模板，（此模板是因为推送消息有字数限制，导致后面显示不出来，如果添加了此模板，会分页发送）
       
       ```
       {{data1.DATA}}
       {{data2.DATA}}
       {{data3.DATA}}
       {{data4.DATA}}
       {{data5.DATA}}
       ```
       **拿到模板ID，添加到application.yaml文件的singleTemplateId字段**
       5. 新增颜文字模板（如添加了会自动随机发一个颜文字，目前就30个，后面可能会添加），可以和分页模板用同一个，把模板ID，添加到application.yaml文件的emoticonsTemplate：templateId处
       ```
       {{data1.DATA}}
       ```
       6. 新增智障自动对话功能（需要部署在云服务或者使用花生壳之类的内网穿透工具（如花生壳主机填：127.0.0.1，内网端口：8081））没有可以忽略这个功能啊（呜呜呜
          
          ​	如果是部署在服务器还需要配置nginx转发，配置如下
          
          ```
          server {
                     listen       80;
                 server_name  你的ip或域名;
                     location  /wechat/demo {
                     proxy_pass  http://127.0.0.1:8081/wechat/demo;
                     proxy_redirect     off;
                     proxy_set_header   Host             $host;
                     proxy_set_header   X-Real-IP        $remote_addr;
                     proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
                      }
                }
          ```
          1. 需要在微信测试后台接口配置信息，添加url和token
             url填写：http://你的ip或域名/wechat/demo/wechat/msg
             token填写随意，然后把token复制到配置文件的token处，启动服务按提交
          2. 直接发送消息到订阅号
    
* **开始填写配置文件，打开了这个文件：src/main/resources/application.yaml**
    
    1. 把从微信后台的appId、appSecret、templateId、用户微信号（openId）替换掉（多个用户可以继续添加用户openId）
    2. title（标题）、meetDate（认识日期）、city（城市）、cityCode（城市编号）、constellation（星座）填写该用户的信息
    3. 注意:前要有个空格，不然格式错误
    4. lbs:
        key:为高德地图接口天气api的私钥（去这里申请填进去https://lbs.amap.com/api/webservice/guide/api/weatherinfo）
    5. constellation: 
    key:为聚合星座接口api的私钥（不过这个免费只能请求30次/天，还要实名认真，有点坑。https://www.juhe.cn/docs/api/id/58）
    
* 所有东西完成后，需要打包项目，运行等待启动完成后，网页上调用 http://localhost:8081/wechat/demo/wechat/sendMessage ，  就能收到推送消息了，每天8点会定时发送，但服务要一直开着，不差钱的，可以买个云服务器放在服务器上。新人优惠第一年一般都很便宜，可以嫖一嫖
