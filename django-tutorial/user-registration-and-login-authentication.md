# User Registration and Login Authentication

회원가입 기능과 로그인 기능 구현을 아래와같이 해볼게요.

![](../.gitbook/assets/image%20%28101%29.png)



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

![](../.gitbook/assets/image%20%2899%29.png)

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

![form.as\_p](../.gitbook/assets/image%20%2894%29.png)

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

![](../.gitbook/assets/image%20%28100%29.png)

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

![](../.gitbook/assets/image%20%28103%29.png)

그리고 작성한 소스코드를 바탕으로 서버를 돌려서 실행해볼게요. 정상적으로 폼에 값을 입력하면 관리자 웹페이지에서 계정이 생성된걸 확인 할 수 있어요.  
  
그런데 불필요한 air field로 나온걸 보기 싫으니 지워볼게요.

### Template - 3 

for문을 사용하고 loop하는동안 받을 객체는 field로해서 화면에 출력 할 수 있도록 할게요.

{% tabs %}
{% tab title="register.html - 1" %}
```
<h3>register</h3>

<form mothod="POST" action="">
  {% csrf_token %}
  {{ for field in form }}
    {{ field }}
  {% endfor %}
  
  <input type='submit' name='Create User'>
</form>
```

깔끔하게 문자들은 다~ 날라간게 보이네요.

![](../.gitbook/assets/image%20%2890%29.png)
{% endtab %}

{% tab title="register.html - 2" %}
```
<h3>register</h3>

<form mothod="POST" action="">
  {% csrf_token %}
  {{ form.username.label }}
  {{ from.username }}
  
  {{ form.email.label }}
  {{ from.email}}
  
  {{ form.password1.label }}
  {{ from.password1}}
  
  {{ form.password2.label }}
  {{ from.password2}}
  
  <input type='submit' name='Create User'>
</form>
```

#### label을 달아 볼게요.

![](../.gitbook/assets/image%20%2896%29.png)
{% endtab %}
{% endtabs %}

### Template - 스타일링 하기 

[http://jsfiddle.net/ivanov11/hzf0jxLg](http://jsfiddle.net/ivanov11/hzf0jxLg)  
위 웹 주소에서 회원가입이 스타일링된 템플릿을 가져와서 사용할게요. 

jsfiddler



{% tabs %}
{% tab title="껍데기" %}
```text
<!DOCTYPE html>
<html>
    
<head>
	<title>Login</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
	<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.6.1/css/all.css" integrity="sha384-gfdkjb5BdAXd+lj+gudLWI+BXq4IuLW5IT+brZEZsLFm++aCMlF1V92rMkPaX4PP" crossorigin="anonymous">

	<style>
		body,
		html {
			margin: 0;
			padding: 0;
			height: 100%;
			background: #7abecc !important;
		}
		.user_card {
			width: 350px;
			margin-top: auto;
			margin-bottom: auto;
			background: #74cfbf;
			position: relative;
			display: flex;
			justify-content: center;
			flex-direction: column;
			padding: 10px;
			box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			-webkit-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			-moz-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			border-radius: 5px;

		}

		.form_container {
			margin-top: 20px;
		}

		#form-title{
			color: #fff;
		}
		.login_btn {
			width: 100%;
			background: #33ccff !important;
			color: white !important;
		}
		.login_btn:focus {
			box-shadow: none !important;
			outline: 0px !important;
		}
		.login_container {
			padding: 0 2rem;
		}
		.input-group-text {
			background: #f7ba5b !important;
			color: white !important;
			border: 0 !important;
			border-radius: 0.25rem 0 0 0.25rem !important;
		}
		.input_user,
		.input_pass:focus {
			box-shadow: none !important;
			outline: 0px !important;
		}

	</style>

</head>
<body>
	<div class="container h-100">
		<div class="d-flex justify-content-center h-100">
			<div class="user_card">
				<div class="d-flex justify-content-center">
					<h3 id="form-title">REGISTER ACCOUNT</h3>
				</div>
				<div class="d-flex justify-content-center form_container">

					<form method="POST" action="">
						<div class="input-group mb-3">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-user"></i></span>
							</div>
							<input type="text" name="username">
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-envelope-square"></i></span>
							</div>
							<input type="email" name="email">
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-key"></i></span>
							</div>
							<input type="text" name="password1">
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-key"></i></span>
							</div>
							<input type="text" name="password2">
						</div>

				   		<div class="d-flex justify-content-center mt-3 login_container">
				 			<input class="btn login_btn" type="submit" value="Register Account">
				   		</div>
					</form>
				</div>
		
				<div class="mt-4">
					<div class="d-flex justify-content-center links">
						Already have an account? <a href="{% url 'login' %}" class="ml-2">Login</a>
					</div>
				</div>
			</div>
		</div>
	</div>
	<script>
						/* Because i didnt set placeholder values in forms.py they will be set here using vanilla Javascript
		//We start indexing at one because CSRF_token is considered and input field 
		 */

		//Query All input fields
		var form_fields = document.getElementsByTagName('input')
		form_fields[1].placeholder='Username..';
		form_fields[2].placeholder='Email..';
		form_fields[3].placeholder='Enter password...';
		form_fields[4].placeholder='Re-enter Password...';


		for (var field in form_fields){	
			form_fields[field].className += ' form-control'
		}
	</script>
</body>
</html>

```
{% endtab %}

{% tab title="기존 내용" %}
```
<h3>register</h3>

<form mothod="POST" action="">
  {% csrf_token %}
  {{ form.username.label }}
  {{ from.username }}
  
  {{ form.email.label }}
  {{ from.email}}
  
  {{ form.password1.label }}
  {{ from.password1}}
  
  {{ form.password2.label }}
  {{ from.password2}}
  
  <input type='submit' name='Create User'>
</form>
```
{% endtab %}

{% tab title="껍데기 + 기존내용" %}
```
<!DOCTYPE html>
<html>
    
<head>
	<title>Login</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
	<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.6.1/css/all.css" integrity="sha384-gfdkjb5BdAXd+lj+gudLWI+BXq4IuLW5IT+brZEZsLFm++aCMlF1V92rMkPaX4PP" crossorigin="anonymous">

	<style>
		body,
		html {
			margin: 0;
			padding: 0;
			height: 100%;
			background: #7abecc !important;
		}
		.user_card {
			width: 350px;
			margin-top: auto;
			margin-bottom: auto;
			background: #74cfbf;
			position: relative;
			display: flex;
			justify-content: center;
			flex-direction: column;
			padding: 10px;
			box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			-webkit-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			-moz-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			border-radius: 5px;

		}

		.form_container {
			margin-top: 20px;
		}

		#form-title{
			color: #fff;
		}
		.login_btn {
			width: 100%;
			background: #33ccff !important;
			color: white !important;
		}
		.login_btn:focus {
			box-shadow: none !important;
			outline: 0px !important;
		}
		.login_container {
			padding: 0 2rem;
		}
		.input-group-text {
			background: #f7ba5b !important;
			color: white !important;
			border: 0 !important;
			border-radius: 0.25rem 0 0 0.25rem !important;
		}
		.input_user,
		.input_pass:focus {
			box-shadow: none !important;
			outline: 0px !important;
		}

	</style>

</head>
<body>
	<div class="container h-100">
		<div class="d-flex justify-content-center h-100">
			<div class="user_card">
				<div class="d-flex justify-content-center">
					<h3 id="form-title">REGISTER ACCOUNT</h3>
				</div>
				<div class="d-flex justify-content-center form_container">

					<form method="POST" action="">
					  {% csrf_token %}
						<div class="input-group mb-3">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-user"></i></span>
							</div>
							{{form.username}}
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-envelope-square"></i></span>
							</div>
							{{form.email}}
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-key"></i></span>
							</div>
							{{form.password1}}
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-key"></i></span>
							</div>
							{{form.password2}}
						</div>

				   		<div class="d-flex justify-content-center mt-3 login_container">
				 			<input class="btn login_btn" type="submit" value="Register Account">
				   		</div>
					</form>
				</div>
		
				<div class="mt-4">
					<div class="d-flex justify-content-center links">
						Already have an account? <a href="{% url 'login' %}" class="ml-2">Login</a>
					</div>
				</div>
			</div>
		</div>
	</div>
	<script>
						/* Because i didnt set placeholder values in forms.py they will be set here using vanilla Javascript
		//We start indexing at one because CSRF_token is considered and input field 
		 */

		//Query All input fields
		var form_fields = document.getElementsByTagName('input')
		form_fields[1].placeholder='Username..';
		form_fields[2].placeholder='Email..';
		form_fields[3].placeholder='Enter password...';
		form_fields[4].placeholder='Re-enter Password...';


		for (var field in form_fields){	
			form_fields[field].className += ' form-control'
		}
	</script>
</body>
</html>

```

![](../.gitbook/assets/image%20%2898%29.png)
{% endtab %}
{% endtabs %}

#### JavaScript 적용하기

사실 이미 우리가 새거 가져왔을때 거기에 이미 자바스크립트 코드가 적혀있었어요. 119 ~ 135번째 줄을 확인하면 해당 소스코드가 있는데요.   
  
이게 뭔지 궁금할텐데. 잘 보면 placeholder안에 이름을 부여해서 시각적으로 보여질수 있게 프런트단의 기능을 구현한거에요.

## Login 페이지 만들기

### Template

#### 출처 : [http://jsfiddle.net/ivanov11/dghm5cu7/](http://jsfiddle.net/ivanov11/dghm5cu7/)

#### 이미 로그인 페이지에는 회원가입 버튼에 링크를 걸어뒀고, 반대로 회원가입페이지에는 로그인 버튼을 링크해뒀어요.

{% tabs %}
{% tab title="로그인창 껍데기" %}
```text
<!DOCTYPE html>
<html>
    
<head>
	<title>Login</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
	<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
	<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.6.1/css/all.css" integrity="sha384-gfdkjb5BdAXd+lj+gudLWI+BXq4IuLW5IT+brZEZsLFm++aCMlF1V92rMkPaX4PP" crossorigin="anonymous">


	<style>
		body,
		html {
			margin: 0;
			padding: 0;
			height: 100%;
			background: #7abecc !important;
		}
		.user_card {
			width: 350px;
			margin-top: auto;
			margin-bottom: auto;
			background: #74cfbf;
			position: relative;
			display: flex;
			justify-content: center;
			flex-direction: column;
			padding: 10px;
			box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			-webkit-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			-moz-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
			border-radius: 5px;

		}

		.form_container {
			margin-top: 20px;
		}

		#form-title{
			color: #fff;
			
		}

		.login_btn {
			width: 100%;
			background: #33ccff !important;
			color: white !important;
		}
		.login_btn:focus {
			box-shadow: none !important;
			outline: 0px !important;
		}
		.login_container {
			padding: 0 2rem;
		}
		.input-group-text {
			background: #f7ba5b !important;
			color: white !important;
			border: 0 !important;
			border-radius: 0.25rem 0 0 0.25rem !important;
		}
		.input_user,
		.input_pass:focus {
			box-shadow: none !important;
			outline: 0px !important;
		}

		#messages{
			background-color: grey;
			color: #fff;
			padding: 10px;
			margin-top: 10px;
		}
	</style>

</head>
<body>
	<div class="container h-100">
		<div class="d-flex justify-content-center h-100">
			<div class="user_card">
				<div class="d-flex justify-content-center">


					<h3 id="form-title">LOGIN</h3>
				</div>
				<div class="d-flex justify-content-center form_container">
					<form method="POST" action="">
						<div class="input-group mb-3">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-user"></i></span>
							</div>
							<input type="text" name="username" placeholder="Username..." class="form-control">
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-key"></i></span>
							</div>
								<input type="password" name="password" placeholder="Password..." class="form-control" >
						</div>

							<div class="d-flex justify-content-center mt-3 login_container">
				 				<input class="btn login_btn" type="submit" value="Login">
				   			</div>
					</form>

				</div>
		
				<div class="mt-4">
					<div class="d-flex justify-content-center links">
						Don't have an account? <a href="{% url 'register' %}" class="ml-2">Sign Up</a>
					</div>
			
				</div>
			</div>
		</div>
	</div>
</body>

</html>
```
{% endtab %}

{% tab title="" %}
```

```
{% endtab %}
{% endtabs %}

### View- 4 

```text
from django.shortcuts import render, redirect
...
...

def registerPage(request):
    form = CreateUserForm()
    
    if request.method == 'POST'
        form = CreateUserForm(request.POST)
        if form.is_valid()
        form.save()
        return redirect('login')
...
...
        
```

이렇게 작성하면 회원가입이 성공적으로 되면 로그인 페이지로 자동으로 redirect되게 해뒀어요.   


그런데 비밀번호1, 비밀번호2 입력이 서로 일치하지 않을 경우 경고창을 출력하게 만들어야 하지 않을까요?   
구현해 볼게요. 

### Template - 4 

6번째에 form.errors을 작성해볼게요.

```text
...
...

				</div>
					{{form.errors}}
				<div class="mt-4">
					<div class="d-flex justify-content-center links">
						Don't have an account? <a href="{% url 'register' %}" class="ml-2">Sign Up</a>
					</div>
			
				</div>
			</div>
		</div>
	</div>
</body>

</html>
```

![](../.gitbook/assets/image%20%2895%29.png)

나오긴 했는데 뭔가 허접하조? Flash Message가 출력되도록 할게요.  
가이드라인은 아래와 같이 할 거에요. 





### view - 5

```text
...
...
from django.contrib import messages

...
...

def registerPage(request):
    form = CreateUserForm()
    
    if request.method == 'POST'
        form = CreateUserForm(request.POST)
        if form.is_valid()
        form.save()
        user = form.cleaned_data.get('username')
        messages.success(request, 'Account was created for '+ user)
        return redirect('login')
...
...
        
```

### Template - 5

5~7번째줄을 작성할게요. 

{% tabs %}
{% tab title="login.html" %}
```text
...
...

				</div>
				{% for message in messages %}
					<p id='messages'>{{ message }}</p>
				{% endfor %}
			
				<div class="mt-4">
					<div class="d-flex justify-content-center links">
						Don't have an account? <a href="{% url 'register' %}" class="ml-2">Sign Up</a>
					</div>
			
				</div>
			</div>
		</div>
	</div>
</body>

</html>
```
{% endtab %}
{% endtabs %}

![](../.gitbook/assets/image%20%2888%29.png)

계정이 2개가 생성되었다는 이유는 기존 dennis3 쿠키가 남아있어서 저렇게 뜨는거에요. 일반적으로 한번 생성하면 한번뜨게되요.

### View - 6 

인증 , 로그인, 로그아웃 매서드를 auth 모듈에서 가져와서 Login & Logout 기능의 완결성을 갖추어 나가볼게요. 

{% tabs %}
{% tab title="views.py" %}
```text
from django.contrib.auth import authenticate, login, logout
from django.contrib import messages

...
...

		    
def loginPage(request):

		if request.method == 'POST':
			username = request.POST.get('username')
			password = request.POST.get('password')

			user = authenticate(request, username=username, password=password)

			if user is not None:
				login(request, user)
				return redirect('home')
			else: 
				messages.info(request, 'Username OR password is incorrect')

		context = {}
		return render(request, 'accounts/login.html', context)
```

10번째 POST방식으로 전달 받게되면\(로그인버튼 클릭시\) 해당 입력된 값들이 username, password에 각각 저장되게 되요.    


authenticate\(\)안에 매개변수를 request 넣고 db에 저장된 username, password를 각각 매칭해주고 이를 user 변수로 만들어줘요.   
  
16번째 줄에 if문을 사용해서 해당 username과 password를 가진 user변수가 있다면\( user is not None\) home으로 redirect되도록 18번째 줄을 설정해줄게요. 그리고 17번째 줄에 login 메서드가 있조? 

> 참고로 redirect했을때 home이라는 부분은 urls.py에서 name으로 설정한 옵션을 가져온거에요.

  
만약 loginPage이름을 login으로 View에 메서드로 설정했다면 conflict가 발생했을거에요. 
{% endtab %}
{% endtabs %}

### Template - 6

우선 6번째 줄에 csrf\_token부터 넣을게요.

{% code title="login.html" %}
```text
<h3 id="form-title">LOGIN</h3>
  
				</div>
				<div class="d-flex justify-content-center form_container">
					<form method="POST" action="">
            {% csrf_token %}
						<div class="input-group mb-3">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-user"></i></span>
							</div>
							<input type="text" name="username" placeholder="Username..." class="form-control">
						</div>
						<div class="input-group mb-2">
							<div class="input-group-append">
								<span class="input-group-text"><i class="fas fa-key"></i></span>
							</div>
								<input type="password" name="password" placeholder="Password..." class="form-control" >
						</div>

							<div class="d-flex justify-content-center mt-3 login_container">
				 				<input class="btn login_btn" type="submit" value="Login">
				   			</div>
					</form>

				</div>
```
{% endcode %}



### view - 7 

로그인 화면에서 아이디와 비밀번호가 정확하지 않을경우 오류 air메시지를 출력해 볼게요.  
19~ 20번째줄을 작성해주면 되요. 그럼 현재 페이지로 다시 rendering이 23번째에 기존 설정한대로 가주게 되요.  


```text
from django.contrib.auth import authenticate, login, logout
from django.contrib import messages

...
...

		    
def loginPage(request):

		if request.method == 'POST':
			username = request.POST.get('username')
			password = request.POST.get('password')

			user = authenticate(request, username=username, password=password)

			if user is not None:
				login(request, user)
				return redirect('home')
			else: 
				messages.info(request, 'Username OR password is incorrect')
				xt)
		context = {}
		return render(request, 'accounts/login.html', context)
```

![](../.gitbook/assets/image%20%28107%29.png)

### 페이지 접근 권한 설정, 로그아웃, 로그인 한 경우 Home페이지에 사용자 이름 띄우기 

### View - 8 

3줄만 적으면되요.   
urls.py 수정도 해줘야겠조?

{% tabs %}
{% tab title="views.py" %}
```text
def logoutUser(request):
	logout(request)
	return redirect('login')
```

이렇게 로그아웃 로직은 만들었는데 Template에 구현해줘야겠조?
{% endtab %}

{% tab title="accounts/urls.py" %}
```
...

urlpatterns = [
    ...
    ...
    path('logout/', views.logoutUser, name="logout"),
    
    ...
]
```
{% endtab %}
{% endtabs %}

### Template - 7 

로그아웃 이름은 대개 navbar에 나타납니다. 그럼 해당 html페이지를 수정작업 들어갈게요. 

{% tabs %}
{% tab title="navbar.html" %}
```text
{% load static %}

<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
 <img src="{% static 'images/logo.png' %}">
 ...
 ...
  </div>
  <span id='hello-msg'>Hello, {{ request.user }} </span>
  <span><a class='hello-msg' href="{% url 'logout' %}">Logout</a> </span>
</nav>
```

8~9번째에 소스코드를 작성할게요. 확인해보면 아래와 같이 출력되요. Logout버튼을 클릭하면 login화면으로 넘어가요.  
  
&gt; 참고로 8번째 줄은 request.user.username으로 사용해도 정상 출력이되요.   
사실 Customer 테이블에는 username이 없는데 어디서 가져온걸까요?   
  
db안 테이블중에 이미 django가 만들어준 auth\_user라는 테이블이 있어요. 거기서 request.user를 호출하면 default로 username 컬럼이 잡혀서 return되요.

![](../.gitbook/assets/image%20%28104%29.png)
{% endtab %}
{% endtabs %}

### View - 9 

2~3번째 줄의 경우는 로그인 유무를 확인해주는 조건문과 is\_authenticated\(\)에요.   
만약 로그인 되어있다면 redirect로 home으로 이동되게 하였어요.  
  
loginPage 함수의 19-20번째에도 위와 같이 동일한 로그인 유무를 확인해주는 로직을 넣어줄게요. 

```text
def registerPage(request):
	if request.user.is_authenticated:
		return redirect('home')

	else :
		form = CreateUserForm()
		if request.method == 'POST':
			form = CreateUserForm(request.POST)
			if form.is_valid():
				form.save()
				user = form.cleaned_data.get('username')
				messages.success(request, 'Account was created for ' + user)
				return redirect('login')

		context = {'form':form}
		return render(request, 'accounts/register.html', context)

def loginPage(request):
	if request.user.is_authenticated:
		return redirect('home')
		
	else :
		if request.method == 'POST':
			username = request.POST.get('username')
			password = request.POST.get('password')

			user = authenticate(request, username=username, password=password)

			if user is not None:
				login(request, user)
				return redirect('home')
			else: 
				messages.info(request, 'Username OR password is incorrect')

		context = {}
		return render(request, 'accounts/login.html', context)


```



