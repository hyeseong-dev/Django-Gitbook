# User Registration and Login Authentication

회원가입 기능과 로그인 기능 구현을 아래와같이 해볼게요.

![](../.gitbook/assets/image%20%2894%29.png)



## Template

1\) login.html 

2\) register.html

{% tabs %}
{% tab title="login.html" %}
```text
<h3>login</h3>
```
{% endtab %}

{% tab title="register.html" %}
```
<h3>register</h3>

<form mothod="POST" action="">
  {{form}}

</form>
```
{% endtab %}
{% endtabs %}

## View 

#### 1\) registerPage 정의 

#### 2\) loginPage 정의 

```text
...
from django.contrib.auth.forms import UserCreationForm 


def registerPage(request):
	form = UserCreationForm()
	context = {'form':form}
	return render(request, 'accoutns/register.html', context)

def loginPage(request):
	context = {}
	return render(request, 'accoutns/login.html', context)
    
...
...
```

### accounts/urls.py

6번, 7번째줄의 소스코드를 넣어주세요. 

```text
from django.urls import path
from . import views


urlpatterns = [
    path('register/', views.registerPage, name="register"),
    path('login/', views.loginPage, name="login"),


    path('', views.home, name="home"),
    path('products/', views.products, name='products'),
    path('customer/<str:pk_test>/', views.customer, name="customer"),

    path('create_order/<str:pk>/', views.createOrder, name="create_order"),
    path('update_order/<str:pk>/', views.updateOrder, name="update_order"),
    path('delete_order/<str:pk>/', views.deleteOrder, name="delete_order"),


]
```

여기까지 다 작성 했다면 한번 실행해 볼게요. 

![](../.gitbook/assets/image%20%2892%29.png)

이렇게 나오는 군요.  이메일도 넣어야하고 스타일링도 해줘야겠어요.

### Template - 2

{% tabs %}
{% tab title="register.html" %}
```
<h3>register</h3>

<form mothod="POST" action="">
  {% csrf_token %}
  {{form.as_p}}
  
  <input type='submit' name='Create User'>
</form>
```
{% endtab %}

{% tab title="login.html" %}
```text
<h3>login</h3>
```
{% endtab %}
{% endtabs %}

form 뒤에 as\_p를 붙였는데 출력을 paragraph로 만들었어요. 한마디로 p태그로 감싸주기 때문에 기존의 헝클어진 모습이 깔끔하게 나와요.  
  
그리고 데이터를 안전하게 보내주기 위해서 csrf\_token도 작성해줄게요.

![form.as\_p](../.gitbook/assets/image%20%2890%29.png)

### View - 2

```text
...
from django.contrib.auth.forms import UserCreationForm 


def registerPage(request):
	form = UserCreationForm()
	
	if request.method == 'POST':
		form = UserCreationForm(request.POST)
		if form.is_valid():
			form.save()
		
	context = {'form':form}
	return render(request, 'accoutns/register.html', context)

def loginPage(request):
	context = {}
	return render(request, 'accoutns/login.html', context)
    
...
...
```

8~11번째 줄을 작성한 소스코드는 POST방식으로 웹 서버로 전달될경우\(submit 버튼 클릭시\) UserCreationForm클래스에 request.POST데이터를 상속 받아 form변수로 만들어 주고 10번째 줄에서 from의 유효성을 is\_valid\(\)를 사용해서 잘 있으면

여기서 잘 있다는 것은 id, pwd, email, re-pwd 입력이 완전히 오류 없이 입력되었는지를 말해요.

11번째의 from.save\(\)통해서 db에 입력한 값들을 저장하게되요.  
확인해봐야조?

![](../.gitbook/assets/image%20%2893%29.png)

좌측에 값을 입력하고 submit 버튼을 눌러서 값이 db에 저장되는지 관리자 페이지에서 확인해보면 정상적으로 확인할수 있어요.

### form.py 

3~4번째 줄에 forms, User 관련 모듈과 클래스 함수를 임포트 할게요.  
14번째줄에 CreateUserForm을 만들거에요.UserCreationForm 클래스를 상속받아서 정의할게요.   
그리고 이전에 정의했던 OrderForm과 같은 방식으로 작성할게요.   
  
단!, fields의 값만 몇가지만 넣을게요. username, email, password1, password2

```text
from django.forms import ModelForm
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User
from django import forms

from .models import Order


class OrderForm(ModelForm):
	class Meta:
		model = Order
		fields = '__all__'

class CreateUserForm(UserCreationForm):
	class Meta:
		model = User
		fields = ['username', 'email', 'password1', 'password2']
```

### View -3 

위에서 만든 클래스를 import 해야겠조? forms 모듈에 있는 CreateUserForm을 import할게요.   
  
그리고 기존이 만들어 둔 UserCreationForm --&gt; CreateUserForm으로 대체하면 되요.

{% tabs %}
{% tab title="views.py" %}
```text
...
from .forms import OrderForms, CreateUserForm
from django.contrib.auth.forms import UserCreationForm 


def registerPage(request):
	form = UserCreationForm()
	
	if request.method == 'POST':
		form = UserCreationForm(request.POST)
		if form.is_valid():
			form.save()
		
	context = {'form':form}
	return render(request, 'accoutns/register.html', context)

def loginPage(request):
	context = {}
	return render(request, 'accoutns/login.html', context)
    
...
...
```
{% endtab %}
{% endtabs %}

