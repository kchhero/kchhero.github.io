---
tags: Reference
title: 'code block ref'
layout: post
category: Reference
description: This is a test page
---

https://github.com/kchhero/kchhero.github.io/commit/3caad5ec5d5b8dffffc40588fb307a8fcd310d35

blog home의 main.css loading 이 되지 않는 문제로 1시간 가량 삽질함
jekyll serve 실행 -> localhost로 확인 시 아무 문제 없었으나
kchhero.github.io 에서 정상 동작 하지 않는 문제가 발생함.

잘은 모르겠지만
/assets/css 의 위치를 /css 로 옮기고,
_include/head.html 에서 아래와 같이 수정함.

```shell?line_numbers=true
diff --git a/_includes/head.html b/_includes/head.html
index ec02e37..93cd2c8 100644
--- a/_includes/head.html
+++ b/_includes/head.html
@@ -19,7 +19,7 @@
   <link rel="alternate" type="application/rss+xml" title="{{ site.title }}" href="{{ "/feed.xml" | prepend: site.baseurl | prepend: site.url }}">

   <link href='https://fonts.googleapis.com/css?family=PT+Serif:400,400italic,700|Roboto+Condensed:700&subset=latin' rel='stylesheet' type='text/css'>
   -  <link rel="stylesheet" href="{{ "/css/main.css" | prepend: site.baseurl | prepend: site.url }}">
   +  <link rel="stylesheet" href="{{ "/css/main.css" | prepend: site.baseurl }}">

   <meta property="og:url" content="{{ site.url }}{{ page.url }}">
      <meta property="og:type" content="website">
```

~~~ ruby?line_numbers=true
def my_cool_method(message)
    puts message
end
~~~

```ruby?line_numbers=false
def my_cool_method(message)
  puts message
  end
```
