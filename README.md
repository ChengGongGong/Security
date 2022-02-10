# 1. CSRF

## 1. 定义
    CSRF（Cross-site request forgery）跨站请求伪造：
      攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求，
      利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。
## 2. 基本流程

    1. 受害者登录a.com，并保留了登录凭证（Cookie）；
    2. b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会默认携带a.com的Cookie。
    3. a.com接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。
    4. a.com以受害者的名义执行了act=xx。
    5. 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让a.com执行了自己定义的操作。
## 3.常见的攻击类型

    1. get类型：只需一个HTTP请求
    2. post类型：使用一个自动提交的表单
    3. 链接类型：用户点击链接才会触发
## 4. 特点
    
    1. 攻击一般发起在第三方网站，而不是被攻击的网站。被攻击的网站无法防止攻击发生；
    2. 攻击者并不能获取到受害者的登录凭证，仅仅是“冒用”，冒充受害者提交操作；而不是直接窃取数据。
    3. 跨站请求可以用各种方式：图片URL、超链接、CORS、Form提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪。    
## 5.防护策略

    1. 阻止不明外域的访问
      a. 同源检测：同源验证是一个相对简单的防范方法，能够防范绝大多数的CSRF攻击。对于安全性要求较高，或者有较多用户输入内容的网站，我们就要对关键的接口做额外的防护措施。
        在HTTP协议中，每一个异步请求都会携带两个Header，用于标记来源域名：Origin Header和Referer Header。
        这两个Header在浏览器发起请求时，大多数情况会自动带上，并且不能由前端自定义内容。 服务器可以通过解析这两个Header中的域名，确定请求的来源域。
        Origin Header不存在的情况：
          1. IE11同源策略：IE 11 不会在跨站CORS请求上添加Origin标头；
          2. 302重定向：在302重定向之后Origin不包含在重定向的请求中，因为Origin可能会被认为是其他来源的敏感信息
        Referer Header：
          1. 对于Ajax请求，图片和script等资源请求，Referer为发起请求的页面地址。对于页面跳转，Referer为打开页面历史记录的前一个页面地址。
          2. 设置Referrer Policy的方法：
            在CSP设置；页面头部增加meta标签；a标签增加referrerpolicy属性
          3. 攻击者可以在自己的请求中隐藏Referer
          4. Referer没有或者不可信的情形：
              1. IE6、7下使用window.location.href=url进行界面的跳转，会丢失Referer。
              2. IE6、7下使用window.open，也会缺失Referer。
              3. HTTPS页面跳转到HTTP页面，所有浏览器Referer都丢失。
              4. 点击Flash上到达另外一个网站的时候，Referer的情况就比较杂乱，不太可信。
        b. Samesite Cookie：为Set-Cookie响应头新增Samesite属性，它用来标明这个 Cookie是个“同站 Cookie”，同站Cookie只能作为第一方Cookie，不能作为第三方Cookie    
          1. 严格模式，Samesite=Strict，表明这个 Cookie 在任何情况下都不可能作为第三方 Cookie。
                该模式下，CSRF攻击基本没有机会，但是跳转子域名或者是新标签重新打开刚登陆的网站，之前的Cookie都不会存在，需要重新登录。
          2. 宽松模式，Samesite=Lax，假如这个请求是改变了当前页面或者打开了新页面且同时是个GET请求，则这个Cookie可以作为第三方Cookie
          3. 存在的问题：
                1. 兼容性不是很好，
                2. 不支持子域，即不能使用SamesiteCookie在主域名存储用户登录信息。每个子域名都需要用户重新登录一次。
          
          
          
          
          
          
          
