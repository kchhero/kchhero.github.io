---
title: 'blogging issue'
layout: post
tags: reference
category: Reference
description: blog를 만들때 발생했던 문제점들 정리
---
https://github.com/kchhero/kchhero.github.io/commit/3caad5ec5d5b8dffffc40588fb307a8fcd310d35

blog home의 main.css loading 이 되지 않는 문제로 1시간 가량 삽질함
jekyll serve 실행 -> localhost로 확인 시 아무 문제 없었으나
kchhero.github.io 에서 정상 동작 하지 않는 문제가 발생함.

잘은 모르겠지만
/assets/css 의 위치를 /css 로 옮기고,
_include/head.html 에서 아래와 같이 수정함.

```shell?linenum=false
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


Main page의 indexing 관련 수정 부분, jekyll editor의 meta data에는 excerpt field가 없으므로
description으로 바꿔줌

```shell?linenum=false
  diff --git a/index.html b/index.html
  index 9029507..abea3e0 100644
  --- a/index.html
  +++ b/index.html
  @@ -6,9 +6,10 @@ title: "Suker"
   summary: "SUKER BLOG MAIN"
  ---
      
```

$ jekyll serve
/usr/local/lib/site_ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in `require': cannot load such file -- bundler (LoadError)
                                                                         from /usr/local/lib/site_ruby/2.3.0/rubygems/core_ext/kernel_require.rb:55:in `require'
                                                                              from /var/lib/gems/2.3.0/gems/jekyll-3.4.1/lib/jekyll/plugin_manager.rb:34:in `require_from_bundler'
                                                                                   from /var/lib/gems/2.3.0/gems/jekyll-3.4.1/exe/jekyll:9:in `<top (required)>'
                                                                                        from /usr/local/bin/jekyll:22:in `load'
                                                                                             from /usr/local/bin/jekyll:22:in `<main>'


==> sudo gem install bundler





/var/lib/gems/2.3.0/gems/bundler-1.14.6/lib/bundler/resolver.rb:386:in `block in verify_gemfile_dependencies_are_found!': Could not find gem 'jekyll-paginate' in any of the gem sources listed in your Gemfile. (Bundler::GemNotFound)

==> sudo gem install jekyll-sitemap


