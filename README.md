# Django 5회차
- [x] login, signup 이론, 실습
- [x] template 상속
- [x] url 관리
---
## 세션준비
1. git clone
    ```
    git clone https://github.com/SYULION8TH/django_5th
    ```
2. 가상환경 생성(clone받은 django_5th 경로에서 생성해주세요. )
    - window
    ```
    python -m venv env
    ```
    - mac
    ```
    python3 -m venv env
    ```
3. requirements.txt 설치
    ```
    pip install -r requirements.txt
    ```
---
## 코드
### accounts/urls.py
```
from django.urls import path
from . import views

urlpatterns = [
    path('signup/', views.signup, name="signup"),
    path('login/', views.login, name="login"),
    path('logout/', views.logout, name="logout"),
]
```
### config/urls.py
```
urlpatterns = [
    ...
    ...
    path('accounts/', include('accounts.urls')),
]
```
### accounts/views.py
```
from django.shortcuts import render, redirect
from django.contrib.auth.models import User # User 모듈 추가
from django.contrib import auth # auth 모듈 추가
```
```
def signup(request):
    if request.method == 'POST':
        if request.POST['password1'] == request.POST['password2']:
            user = User.objects.create_user(
                request.POST['username'], password=request.POST['password1'])
            auth.login(request, user)
            return redirect('index')
    return render(request, 'accounts/signup.html')
```
#### signup 함수 설명
`request`방식이 `POST`이고 입력비밀번호(password1)와 확인비밀번호(password2)가 같으면<br>
`create_user`함수를 호출하여 User객체를 생성합니다. <br>
`login`함수를 호출하여 자동으로 로그인 되도록 한 후<br>
`redirect`함수를 호출하여 root 페이지인 index로 이동합니다. <br>
만약 회원가입에 실패한다면 다시 회원가입 페이지로 이동합니다. 
```
def login(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = auth.authenticate(request, username=username, password=password)
        if user is not None:
            auth.login(request, user)
            return redirect('index')
        else:
            return render(request, 'accounts/login.html', {'error': 'username or password is incorrect.'})
    else:
        return render(request, 'accounts/login.html')
```
#### login 함수 설명
`request`방식이 `POST`이면 <br>
`username`, `password` 값을 변수에 저장한 후 <br>
`authenticate`함수 호출로 존재하는 계정인지 확인합니다. <br>
존재하는 계정일 경우(not None)<br>
`login`함수를 호출하여 자동으로 로그인 되도록 한 후<br>
`redirect`함수를 호출하여 root 페이지인 index로 이동합니다. <br>
존재 하지 않는 계정인 경우 에러메세지와 함께 다시 login 페이지로 이동합니다. <br>
request방식이 POST가 아닐 경우 다시 login 페이지로 이동합니다.
```
def logout(request):
    if request.method == 'POST':
        auth.logout(request)
        return redirect('index')
    return render(request, 'accounts/signup.html')
```
### config/templates/base.html
```
<!-- account -->
<li class="nav-item dropdown">
{% if user.is_authenticated %}
<a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
    {{ user.username }}
</a>
<div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
    <a class="dropdown-item" href="javascript:{document.getElementById('logout').submit()}">Logout</a>
    <form id="logout" method="POST" action="{% url 'logout' %}">
        {% csrf_token %} <input type="hidden" />
    </form>
</div>
{% else%}
<a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
    Signup
</a>
<div class="dropdown-menu" aria-labelledby="navbarDropdownMenuLink">
    <a class="dropdown-item" href="{% url 'signup' %}">Signup</a>
    <a class="dropdown-item" href="{% url 'login' %}">Login</a>
</div>
{% endif %}
</li>
<!-- //account -->
```
---
#### 최종 코드 보기
- [likelion_8th_session_11th](https://github.com/marobew/likelion_8th_session_11th)
