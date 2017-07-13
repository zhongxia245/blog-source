---
title: 自制简单的Markdown编辑器
date: 2016-05-05 17:26:27
tags: JavaScript
categories: JavaScript
---
现在 markdown 来编写 笔记,是非常流行的一种语言. 学习 markdown 的难度很低, 只要学习了如何使用 markdown 就可以很快速的编写笔记. 
这里来介绍一款,把 markdown 转换成 HTML的 工具类
<!--more-->
---
## marked.js
地址: https://github.com/chjj/marked/blob/master/lib/marked.js

### 1. 如何使用呢?

就一句代码的问题,这样就转换完成了
```
var resultHtml = marked(e.target.value);
```

### 2. 根据该工具类,自制一个简单的Markdown编辑器
效果图:

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2004793-334d7adb49567baf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<!doctype html>
<html>

<head>
  <meta charset="utf-8" />
  <title>Marked in the browser</title>
  <script src="js/lib/marked.js"></script>
  <link rel="stylesheet" media="all" href="http://cdn-qn0.jianshu.io/assets/base-f935477e8faba16f29c6f9e4f0e1d032.css">
  <link rel="stylesheet" media="all" href="http://cdn-qn0.jianshu.io/assets/writer-0115c67175d681b0ce9facf3c65caf11.css">
  <link rel="stylesheet" media="all" href="http://cdn-qn0.jianshu.io/assets/base-read-mode-64acccf6966299cfa3d580140a2582fe.css">
  <style type="text/css">
  .container {
    width: 100%;
    display: -webkit-flex;
    display: flex;
  }
  
  .left,
  .right {
    flex: 1;
    margin: 10px;
    height: 500px;
    border-radius: 5px;
    overflow: auto;
  }
  
  textarea {
    padding: 10px;
    border-radius: 5px;
  }
  
  .right {
    padding: 10px;
  }
  </style>
</head>

<body>
  <div class="container">
    <div class="left">
      <textarea id="txt_content" style="width:100%; height:100%;"></textarea>
    </div>
    <div class="right" id="result">
    </div>
  </div>
  <script>
  window.onload = function() {
    var txt = document.getElementById('txt_content');
    var result = document.getElementById('result');

    txt.onkeydown = function(e) {
      result.innerHTML = marked(e.target.value);
    }
  }
  </script>
</body>

</html>
```