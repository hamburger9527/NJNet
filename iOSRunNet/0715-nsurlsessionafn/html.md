# html

```html
<html>
    <meta charset="UTF-8">

    <body>
        电话号码：13243244535
        网站：http://www.baidu.com
    </body>
</html>

```

---


```html
<html>
    <!-- 网页的描述信息 -->
    <head>
        <meta charset="UTF-8">
        <title>第一个网页</title>

        <script>
            function login()
            {
                // 让webView跳转到百度页面（加载百度页面）
                location.href = 'xmg://sendMessage_number2_number3_?100&100&99';
            }
        </script>
    </head>

    <!-- 网页的具体内容 -->
    <body>
        电话：10086
        <button style="background: red; width:100px; height:30px;" onclick="login();">Call</button>

        <br>
        <a href="http://www.baidu.com">百度</a>
    </body>
</html>

```
