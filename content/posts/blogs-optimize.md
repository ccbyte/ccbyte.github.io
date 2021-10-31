---
date: 2021-10-31
title: "Hugo博客/Leaveit主题优化"
description: "增加了评论模块，markdown目录，字数统计"
tags: ["hugo"]
categories: ["front end"]
---
# 主题优化
我能说我是因为这个主题才用的hugo吗？
## 评论模块
该主题不自带评论模块，需要我们手动添加，挑了一下选了Utterances。  
Utterances使用Github登录评论，Valine可匿名评论但要备案才能用，两个都加再页面上了，根据配置来，配了一个注释掉另一个配置就行。    

### 新增评论模块页
📝 content/themes/LeaveIt/layouts/partials/comments.html    
``` html
<!-- valine -->
{{- if .Site.Params.valine.enable -}}
  <!-- id 将作为查询条件 -->
  <span id="{{ .URL | relURL }}" class="leancloud_visitors" data-flag-title="{{ .Title }}">
    <span class="post-meta-item-text">阅读量 </span>
    <span class="leancloud-visitors-count"></span>
    <p></p>
  </span>
  <div id="vcomments"></div>
  <script src="//cdn1.lncld.net/static/js/3.0.4/av-min.js"></script>
  <script src='//unpkg.com/valine/dist/Valine.min.js'></script>
  <script type="text/javascript">
    new Valine({
        el: '#vcomments' ,
        appId: '{{ .Site.Params.valine.appId }}',
        appKey: '{{ .Site.Params.valine.appKey }}',
        notify: {{ .Site.Params.valine.notify }}, 
        verify: {{ .Site.Params.valine.verify }}, 
        avatar:'{{ .Site.Params.valine.avatar }}', 
        placeholder: '{{ .Site.Params.valine.placeholder }}',
        visitor: '{{ .Site.Params.valine.visitor }}'
    });
  </script>
{{- end }}
<!-- utterances -->
{{- if .Site.Params.utterances.owner}}
<div class="post bg-white">
    <script src="https://utteranc.es/client.js"
          repo="{{ .Site.Params.utterances.owner }}/{{ .Site.Params.utterances.repo }}"
          issue-term="{{ .Site.Params.utterances.issueTerm }}"
          theme="{{ .Site.Params.utterances.theme }}"
          crossorigin="anonymous"
          async>
    </script>
    <noscript>Please enable JavaScript to view the <a href="https://github.com/utterance">comments powered by utterances.</a></noscript>
</div>
{{- end }}
```

### 在文章模板页面引用
📝 content/themes/LeaveIt/layouts/_default/single.html  
如下代码加在```div.post-comment```内{{ end }}标签后

``` html
{{ partial "comments.html" . }}
```

### 配置
📝config.toml  
```
# Utterances.
[params.utterances]         # utteranc is a comment system based on GitHub issues. see https://utteranc.es
 owner = "github用户名" # github用户名
 repo = "你的仓库地名    # 仓库地址The repo to store comments
 issueTerm = "pathname" # 默认
 theme = "github-light" # 默认

# Valine.
# You can get your appid and appkey from https://leancloud.cn
# more info please open https://valine.js.org
# [params.valine]
#   enable = true #开关
#   appId = '你的appId'
#   appKey = '你的appKey'
#   notify = false  # mail notifier , https://github.com/xCss/Valine/wiki
#   verify = false # Verification code
#   avatar = 'mp' #神秘人(一个灰白头像) 
#   placeholder = '说点什么吧...' #在评论框显示的温馨提示等
#   visitor = true #访问量统计
```

## 增加Markdown目录

### 新增目录模块页
📝 content/themes/LeaveIt/layouts/partials/toc.html    
```html
<div class="post-toc" id="post-toc">
  <h2 class="post-toc-title">{{ T "toc" }}</h2>
  {{ $globalAutoCollapseToc := .Site.Params.autoCollapseToc | default false }}
  <div class="post-toc-content{{ if not (or .Params.autoCollapseToc (and $globalAutoCollapseToc (ne .Params.autoCollapseToc false))) }} always-active{{ end }}">
    {{.TableOfContents}}
  </div>
</div>
<script type="text/javascript">
  window.onload = function () {
    var fix = $('.post-toc');
    var end = $('.post-comment');
    var fixTop = fix.offset().top, fixHeight = fix.height();
    var endTop, miss;
    var offsetTop = fix[0].offsetTop;
    $(window).scroll(function () {
      var docTop = Math.max(document.body.scrollTop, document.documentElement.scrollTop);
      if (end.length > 0) {
        endTop = end.offset().top;
        miss = endTop - docTop - fixHeight;
      }
      if (fixTop < docTop) {
        fix.css({ 'position': 'fixed' });
        if ((end.length > 0) && (endTop < (docTop + fixHeight))) {
          fix.css({ top: miss });
        } else {
          fix.css({ top: 0 });
        }
      } else {
        fix.css({ 'position': 'absolute' });
        fix.css({ top: offsetTop });
      }
    })
  }
</script> 
```

### 在文章模板页面引用
📝 content/themes/LeaveIt/layouts/_default/single.html  
📢 加在```</header>```后面
```html
{{ if ( .Site.Params.toc | default true ) }}
    {{ partial "toc.html" . }}
{{ end }}
```

### 增加目录样式
📝 themes/LeaveIt/assets/css/_custom.scss   
📢 该样式使目录固定在右侧，并有缩放效果
```css

.post-toc {
    position: absolute;
    width: 260px;
    margin-left: 780px;
    padding: 30px;
    word-wrap: break-word;
    box-sizing: border-box;
    .post-toc-title {
        margin: 0;
        font-weight: 400;
        text-transform: uppercase;
    }
    .post-toc-content {
        &.always-active ul {
            display: block;
        }
        >nav>ul {
            margin: 10px 0;
        }
        ul {
            padding-left: 0;
            list-style: none;
            ul {
            padding-left: 15px;
            display: none;
            }
            .has-active > ul {
                display: block;
            }
        }
    }
    a:hover {
        color: #2d96bd;
        -webkit-transform: scale(1.1);
        -ms-transform: scale(1.1);
        transform: scale(1.1);
    }
    a {
        display: block;
        line-height: 30px;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
        -webkit-transition-duration: .2s;
        transition-duration: .2s;
        -webkit-transition-property: -webkit-transform;
        transition-property: -webkit-transform;
        transition-property: transform;
        transition-property: transform,-webkit-transform;
        -webkit-transition-timing-function: ease-out;
        transition-timing-function: ease-out;
    }
}
.post-warp .post-content img {
    max-width: 100%;
}
@media only screen and (max-width: 1224px) {
    .post-toc {
        display: none;
    }
} 
```
### 配置
📝 config.toml

```
[params]
toc = true                # 是否开启目录
autoCollapseToc = false   # Auto expand and collapse toc
```