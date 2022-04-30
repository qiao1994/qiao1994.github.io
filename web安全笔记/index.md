# Web安全备忘


## SQL注入

- 永远不要使用超级用户或所有者帐号去连接数据库，要用权限被严格限制的帐号
- 检查输入的数据是否具有所期望的数据格式
  - is_numeric() 、 ctype_digit()等
- 使用数据库特定的敏感字符转义函数
  - mysql_escape_string() 、 sql_escape_string()、 addslashes() 、 str_replace()
- 避免显示出任何有关数据库的信息
- 白名单列出输入字段

## XSS 跨站脚本攻击

### 非持久型(url)

-  Web 页面渲染的所有内容或者渲染的数据都必须来自于服务端
- 尽量不要从 URL，document.referrer，document.forms 等这种 DOM API 中获取数据直接渲染
- 尽量不要使用 eval, new Function()，document.write()，document.writeln()，window.setInterval()，window.setTimeout()，innerHTML，document.creteElement() 等可执行字符串的方法
- 如果做不到以上几点，也必须对涉及 DOM 渲染的方法传入的字符串参数做 escape 转义
- 前端渲染的时候对任何的字段都需要做 escape 转义编码，escape 转义的目的是将一些构成 HTML 标签的元素转义，比如 <，>，空格 等，转义成 <，>，  等显示转义字符

### 持久型(db)

-  后端在入库前应该选择不相信任何前端数据，将所有的字段统一进行转义处理
- 后端在输出给前端数据统一进行转义处理
- 前端在渲染页面 DOM 的时候应该选择不相信任何后端数据，任何字段都需要做转义处理



## CSRF 跨站请求伪造攻击

- 正确使用 GET，POST 请求和 cookie；在非 GET 请求中增加 token
- 为每个用户生成一个唯一的 cookie token，所有表单都包含同一个伪随机值，这种方案最简单，因为攻击者不能获得第三方的 cookie(理论上)，所以表单中的数据也就构造失败，但是由于用户的 cookie 很容易由于网站的 XSS 漏洞而被盗取，所以这个方案必须要在没有 XSS 的情况下才安全。
- 每个 POST 请求使用验证码，这个方案算是比较完美的，但是需要用户多次输入验证码，用户体验比较差，所以不适合在业务中大量运用。
- 渲染表单的时候，为每一个表单包含一个 csrfToken，提交表单的时候，带上 csrfToken，然后在后端做 csrfToken 验证。

## 参考资料

- https://baijiahao.baidu.com/s?id=1608462773715109234&wfr=spider&for=pc
- https://www.php.net/manual/zh/security.database.sql-injection.php


