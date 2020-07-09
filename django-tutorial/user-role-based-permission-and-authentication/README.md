# User Role Based Permission & Authentication

## 들어가기 앞서 

이번에는 다른 유저 타입을 정해보도록 할게요.  
관리자 웹 페이지를 보면 아래와 같이 나와있어요.   
해당 웹페이지의 staff 인지 아닌지를 구분하는거조? 이에 맞는 정보에 대한 접근,삭제,수정, 생성을 구분해줘야 하는데요. 이번 시간에는 이와 관련된 기능과 소스코드를 짜보도록 할게요.

![](../../.gitbook/assets/image%20%2887%29.png)

![&#xACE0;&#xAC1D; &#xC720;&#xC800;&#xB294; &#xC815;&#xBCF4; &#xC811;&#xADFC;&#xC5D0; &#xC81C;&#xD55C;&#xB41C; &#xBAA8;&#xC2B5;](../../.gitbook/assets/image%20%2891%29.png)

### Template - 1

{% tabs %}
{% tab title="user.html" %}
```text
{% extends 'accounts/main.html' %}
{% block content %}
{% include 'accounts/status.html' %}

<h3>User Profile</h3>

{% endblock content %}
```
{% endtab %}
{% endtabs %}

### View - 1

userPage 메서드를 작성했어요. 해당 유저의 개인정보를 수정하기위한 로직이겠조?

```text
...
...

@login_required(login_url='login')
def home(request):
...
...

def userPage(request):
	context = {}
	return render(request, 'accounts/user.html', context)

...
```

### urls.py 

11번째줄에 url을쓰고 view를 호출할 메서드 이름과 url호출 할 리모컨 역할인 name 매개변수도 작성하였어요.

```text
from django.urls import path
from . import views


urlpatterns = [
    path('register/', views.registerPage, name="register"),
    path('login/', views.loginPage, name="login"),
    path('logout/', views.logoutUser, name="logout"),

    path('', views.home, name="home"),
    path('user/', views.userPage, name="user-page"),
    path('products/', views.products, name='products'),
    path('customer/<str:pk_test>/', views.customer, name="customer"),

    path('create_order/<str:pk>/', views.createOrder, name="create_order"),
    path('update_order/<str:pk>/', views.updateOrder, name="update_order"),
    path('delete_order/<str:pk>/', views.deleteOrder, name="delete_order"),


]
```



#### 일반 사용자 계정과 관리자 계정의 차이점 

관리자 계정에 Permission 박스가 체크된 모

![](../../.gitbook/assets/image%20%2896%29.png)

아래는 일반 사용자 계정에는 staff status와 superuser status check가 해제된 모습

![](../../.gitbook/assets/image%20%2898%29.png)

### 소스코드 개선

5-6번째 , 11-12번째 소스코드가 동일하게 중복되네요. 그리고 이는 매우 비효율적인 소스코드에요. 이를 개선하고자 decorator와 모듈 기법을 통해 뚱뚱한 소스코드를 날씬하게 만들게요. 

{% tabs %}
{% tab title="비효율 views.py" %}
```text
...
...

def registerPage(request):
	if request.user.is_authenticated:
		return redirect('home')
		...
		...
		
def loginPage(request):
	if request.user.is_authenticated:
		return redirect('home')
...
...
```
{% endtab %}
{% endtabs %}

결론적으로 보게되면 아래와 같이 소스코드를 만들고 decorators.py 파일을 만들어서 모듈화 해줄거에요. 

### decorators.py

왼쪽 파일의 소스 코드부분이 accounts/decorators.py 파일이에요.   
  
View에서 @unauthenticated\_user 데코레이터를 만나 decorators.py 파일의 해당 함수를 호출하게되요. 1번이라고 쓰여진 부분 보이조? 다음 return 함수에서 wrapper\_func을 호출하여 2번째 내부 함수를 실행하게 되요.

로그인이 되어 있다면 if문이 실행되고 로그인\(X\) 실행되어 있지 다면 view\_func함수가 호출되어  실행되는데요 처음 1번 함수의 인자인 view\_func은 바로 View.py에 있는 registerPage의 함수를 받는다는점 유의하세요.   
  
복잡하지만 한편으로 알면 간단한 원리에요.

![](../../.gitbook/assets/image%20%28106%29.png)



