---
layout: post
title: 2019-03-10 Django CSS, Template
---

Djnago Tutorial
=

### CSS

#### 부트스트랩 사용하기

```
blog/templates/blog/post_list.html
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
```
- Bootstrap을 위의 코드로 적용시킨다

### 정적파일, 미디어파일

- 장고는 Static, Media Files가 존재한다.
- Media Files는 유저 업로드 다운로드 등을 지칭하고, Static은 웹서비스 개발시에 정적파일들을 이야기한다.

```
<linkt rel='stylesheet' href='mystyle.css'>
```
- 위의 코드처럼 같은 디렉토리에 있다고 해서 mystyle.css가 적용이 되는것은 아니다. 장고 static파일에 대한 이해가 필요하다.

### 정적 파일은 어디에 넣어야 되나?

- 서버에서 collectstatic을 실행할 때 처럼, 장고는 'admin'앱에서 정적파일을 어디서 찾아야하는지 이미 알고있다. 'blog'앱에 정적파일을 추가하면 된다.

```
 djangogirls
    ├── blog
    │   ├── migrations
    │   ├── static
    │   └── templates
    └── mysite
```
- blog, static이 같은 디렉토리에 있으면 된다.

```
    djangogirls
    └─── blog
         └─── static
              └─── blog
                   └─── blog.css

```

```
h1 a {
    color: #FCA205;
}

```
- h1태그 밑에 있는 a태그에 대해서

```
{% load staticfiles %}

<!DOCTYPE html>
    <head>
    <meta charset="utf-8">
    <title>Django Girls blog</title>
    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
    <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
    <link rel="stylesheet" href="{% static "blog/blog.css" %}" />
    </head>
```
- static경로가 언제든지 변경될 수 있기때문에 static경로를 장고에 위임을 한다.

### 템플릿 상속

```
blog
└───templates
    └───blog
            base.html
            post_list.html

```
- 템플릿 상속을 위해 base.html을 만든다.

```
# blog/templates/blog/base.html
<div class="col-md-8">
	{% block content %}
    {% endblock %}
</div>
```
- {% block %}으로 HTML내에 들어갈 수 있는 공간을 만들었다.


### 어플리케이션 확장하기
