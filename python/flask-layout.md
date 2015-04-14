## jinja2模板的使用

通常网站的结构

```
.---
    - static
    - templates
    - tmp
```

配合layout使用

**layout.html**

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <!-- The above 3 meta tags *must* come first in the head; any other head content must come *after* these tags -->
    <script src="http://libs.baidu.com/jquery/2.0.0/jquery.min.js"></script>
    <script src="http://libs.baidu.com/bootstrap/3.0.3/js/bootstrap.min.js"></script>
    <link href="http://libs.baidu.com/bootstrap/3.0.3/css/bootstrap.min.css" rel="stylesheet">
    <link href="/static/google-plus.css" rel="stylesheet">
    <link rel="shortcut icon" href="/static/favicon.png">
    <title>网页标题</title>
  </head>
  <body>
    {% block nav %}
      <nav class="navbar navbar-default" role="navigation">
        <div class="container">
          <!-- Brand and toggle get grouped for better mobile display -->
          <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target="#bs-navbar-collapse">
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="/">大标题</a>
          </div>
          <!-- Collect the nav links, forms, and other content for toggling -->
          <div class="collapse navbar-collapse" id="bs-navbar-collapse">
            <ul class="nav navbar-nav">
              <li><a href="/">主页</a></li>
              <li><a href="/upload">上传</a></li>
              <li><a href="/doc">相关文档</a></li>
            </ul>
            <ul class="nav navbar-nav navbar-right">
              <li><a href="http://git-qa.gz.netease.com/lagissue/videoframe-compare">
                See in Git
              </a></li>
            </ul>
          </div>
        </div>
        <script>
          /* highlight nav */
          $("ul.nav").find("a").each(function(e){
            var href = $(this).attr("href");
            if (href == window.location.pathname){
              $(this).parent("li").addClass("active");
            }
          });
        </script>
      </nav>
    {% endblock %}
    <noscript>Please enable JavaScript in your browser!</noscript>
    <div class="container">
      <div id="content">
        {% block body %}{% endblock %}
      </div>
      <div id="footer">
        <span class="pull-right">网站作者: 孙圣翔</span>
      </div>
    </div>
  </body>
</html>
```