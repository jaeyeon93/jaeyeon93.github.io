---
layout: post
title: 2019-03-02 Django Viewer
---

Django Viewer
=

## url

```
urlpatterns = [
    url(r'^admin/', admin.site.urls),
]

```
- 장고는 admin/로 시작하는 모든 URL을 view와 대조해 찾아냅니다. 무수히 많은 URL이 admin URL에 포함될 수 있어 일일이 모두 쓸 수 없다. 그래서 정규표현식을 사용합니다.
- 장고가 URL을 뷰에 매칭시킬때 regex를 사용한다. URL패턴을 만들기 위해서는 정규표현식을 알면 좋다.


### 정규표현식
- ^ : 문자열 시작
- $ : 문자열 끝날때
- \d : 숫자
- () : 패턴의 부분을 저장할 때

```
#blog/urls.py
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^%', views.post_list, name='post_list')
]
```
- r'^$' 시작과 끝사이에 아무것도 없는 빈문자열이다.
- post_list라는 이름의 view가 루트 URL에 할당이 되었다. 이 URL패턴은 빈문자열에 매칭이 된다.
- name='post_list'는 URL에 이름을 붙은 것으로 뷰를 식별한다.
- 뷰는 애플리케이션의 로직을 넣는곳이다. blog/views.py에 post_list라는 함수를 만든다.
- post_list는 요청(request)를 넘겨받아 render메서드를 호출한다. render메서드를 호출받은(return) blog/post_list.html템플릿을 보여준다.

### 템플릿

- 템플릿은 서로 다른 정보를 일정한 형태로 표시하기 위해 재사용 가능한 파일을 말한다.
- 장고 템플릿 양식은 HTML을 사용한다.

```
blog
└───templates
 └───blog
```
- blog디렉토리 밑에 templates디렉토리 내 blog라는 하위디렉토리를 생성한다. 장고의 기본적인 룰이다.


### 장고 ORM과 쿼리셋
- Django Model이 장고의 ORM이다.
- 쿼리셋(QuerySet)은 전달받은 모델의 객체 목록이다. 쿼리셋은 데이터베이스로부터 데이터를 읽고, 필터를 걸거나 정렬할 수 있다.

```
$python manage.py shell
```
- shell옵션을 통해 장고쉘에 접속할 수 있다. 장고 인터랙티브콘솔을 사용하는것이다.

#### 객체생성하기
```
#User
>>> Post.objects.create(author=me, title='Sample title', text='Test')
>>> from django.contrib.auth.models import User
>>> User.objects.all()
<QuerySet [<User: ola>]>
>>> me = User.objects.get(username='ola')

#Post
>>> from blog.models import Post
>>> Post.objects.create(author=me, title='Sample title', text='Test')
>>> Post.objects.all()
<QuerySet [<Post: my post title>, <Post: another post title>, <Post: Sample title>]>
```

### 템플릿 동적 데이터
- 블로그 글은 각각 다른 장소에 조각조각 나뉘어져있다. Post모델은 models.py파일에 post_list모델은 views.py에 있다.
- view는 모델과 템플릿을 연결하는 역할을 한다. post_list를 뷰에서 보여주고 이를 템플릿에 전달하기 위해서는 모델을 가져와야 한다. 일반적으로 뷰가 템플릿에서 모델을 선택하도록 만든다.

```

from django.shortcuts import render
from .models import Post
from django.utils import timezone

def post_list(request):
    qs = Post.objects.all()
    qs = qs.filter(published_date__lte=timezone.now())
    qs = qs.order_by('published_date')
    return render(request, 'blog/post_list.html', {
        'post_list' : qs
    })
```
- return render에서 post_list라는 이름으로 html에 전달이 된다.
- 탬플릿태그와 탬플릿필터가 있다.
- HTML에 파이썬 코드를 바로 넣을수가 없다. 템플릿태그는 파이썬을 HTML로 바꿔주어, 빠르고 쉽게 동적인 웹사이트를 만들 수 있게 도와준다.

```
# post_list.html
<!DOCTYPE html>
<head>
        <title>Django Girls blog</title>
    </head>
    <body>
        {{ post_list }}
        <div>
            <h1><a href="">Django Girls Blog</a></h1>
        </div>

        {%  for post in post_list %}
            {{ post.title }}
            {{ post.text }}
            {{ post.create_date }}
            {{ post.published_date }}
        {% endfor %}
        <hr>

        <div>
            <p>published: 14.06.2014, 12:14</p>
            <h2><a href="">My first post</a></h2>
            <p>Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum. Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut fermentum massa justo sit amet risus.</p>
        </div>

        <div>
            <p>published: 14.06.2014, 12:14</p>
            <h2><a href="">My second post</a></h2>
            <p>Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum. Donec id elit non mi porta gravida at eget metus. Fusce dapibus, tellus ac cursus commodo, tortor mauris condimentum nibh, ut f.</p>
        </div>
    </body>
</html>
```
