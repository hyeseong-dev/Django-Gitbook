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

userPage 메서드를 작성했어요. 해당 유저의 개인정보를 수정하기위한 로직이겠조? auth패키지에서 decorators 모듈의 login\_required 함수를 import했어요. 그리고 원하는 view함수에 데코레이터를 얹은 모습이에요.

```text
...
from django.contrib.auth.decorators import login_required
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

### decorators.py -1

왼쪽 파일의 소스 코드부분이 accounts/decorators.py 파일이에요.   
  
View에서 @unauthenticated\_user 데코레이터를 만나 decorators.py 파일의 해당 함수를 호출하게되요. 1번이라고 쓰여진 부분 보이조? 다음 return 함수에서 wrapper\_func을 호출하여 2번째 내부 함수를 실행하게 되요.

로그인이 되어 있다면 if문이 실행되고 로그인\(X\) 실행되어 있지 다면 view\_func함수가 호출되어  실행되는데요 처음 1번 함수의 인자인 view\_func은 바로 View.py에 있는 registerPage의 함수를 받는다는점 유의하세요.   
  
복잡하지만 한편으로 알면 간단한 원리에요.

> 참고로 decorators.py 파일 안의 unauthenticated\_user 메서드를 import하여 데코레이터로 setup을 깔아줘야 된다는점도 유의하세요.

![](../../.gitbook/assets/image%20%28106%29.png)

## User 접근 권한 로직 설정

### View - 2

localhost:8000/으로 접속하게 되면 메인화면이 보이지 않고 login페이지로 넘어가도록 현재 데코레이터를 입혀 뒀는데요.   
회원가입을 했어도 관리자 권한이 있는 사용자와 관리자 권한이 없는 사용자가 보는 화면에 제한을 둘거에요.   
  
방식은 데코레이터를 이용해 볼거에요. 

{% tabs %}
{% tab title="views.py" %}
```text
...
...
@login_required(login_url='login')
def home(request):
	orders = Order.objects.all()
	customers = Customer.objects.all()

	total_customers = customers.count()

	total_orders = orders.count()
	delivered = orders.filter(status='Delivered').count()
	pending = orders.filter(status='Pending').count()

	context = {'orders':orders, 'customers':customers,
	'total_orders':total_orders,'delivered':delivered,
	'pending':pending }

	return render(request, 'accounts/dashboard.html', context)
	
	...
	...
```
{% endtab %}
{% endtabs %}



### 그룹 등록 

이미 장고가 이런 부분을 고려해서 Group 모델과 테이블을 만들어 줬어요. 사용하기만 하면 되는데요. Group을 클릭해주고 admin, customer 두 개를 만들어 주고 저장해주세요.

![](../../.gitbook/assets/image%20%28117%29.png)

![](../../.gitbook/assets/image%20%28122%29.png)

![](../../.gitbook/assets/image%20%28120%29.png)



그 다음 유저를 우리가 만든 GROUP에 넣어볼게요.  
본인이 웹브라우저에서 만든 유저중 아무거나 선택해주고 스크롤을 조금 내려보면 아래 화면이 있어요. 그룹 하나 선택해서 오른쪽으로 넘기고 저장해주세요.

![](../../.gitbook/assets/image%20%28119%29.png)

다른 유저 하나를 또 선택해서 이번에는 customer를 선택해서 저장해주세요.   
  
현재 유저 2개를 선택했는데 각각 다른 Group에 속해있다는거 명심하세요.

### decorators.py -2

```text
from django.http import HttpResponse
from django.shortcuts import redirect

def unauthenticated_user(view_func):
  def wrapper_func(request, *args, **kwargs):
    if request.user.is_authenticated:
      return redirect('home')
    else:
      return view_func(request, *args, **kwargs)

  return wrapper_func


def allowed_users(allowed_roles=[]):
  def decorator(view_func):
    def wrapper_func(request, *args, **kwargs):
    
        return view_func(request, *args, **kwargs)
    return wrapper_func
  return decorator

```

### View - 3

allowed\_users 메소드를 import할게요.   
home 메서드의 바로 위에 allowed\_users\(\) 메서드 데코레이터를 작성할게요.  
  
views &lt;-&gt; decorators 서로 상호 작용하는 부분을 잘 봐야해요.   
아래 views의 8번째줄에서 처음 로그인을 하게될거에요.   
  
예. dennis라는 admin 계정을 갖고 있는 유저라고 가정할게요. \(admin, customer group에 속하는지 안하는지는 이미 바로위 관리자 패널에서 설정했어요.\)  
  
9번째줄에서 데코레이터가 발동되면 아래 스샷의 13번째줄의 메서드가 호출되고 실행되요. 그럼 아래 18번째 줄의 decorator가 실행되면 14번째 deocrator 메서드가 실행되는데 이때 decorator가 실행되는데 그 인자인 view\_func  == home 함수\(views.py\)를 말해요. 

이후 wrapper\_func이 실행되요.

{% tabs %}
{% tab title="views.py" %}
```text
...
...
from .decorators import unauthenticated_user, allowed_users

...
...

@login_required(login_url='login')
@allowed_users(allowed_roles=['admin'])
def home(request):
	orders = Order.objects.all()
	customers = Customer.objects.all()

	total_customers = customers.count()

	total_orders = orders.count()
	delivered = orders.filter(status='Delivered').count()
	pending = orders.filter(status='Pending').count()

	context = {'orders':orders, 'customers':customers,
	'total_orders':total_orders,'delivered':delivered,
	'pending':pending }

...
...
```
{% endtab %}
{% endtabs %}

### decorators - 3

{% tabs %}
{% tab title="decorators.py" %}
```text
def allowed_users(allowed_roles=[]):
  def decorator(view_func):
    def wrapper_func(request, *args, **kwargs):
    
        group = None
        if request.user.groups.exists():
          group = request.user.groups.all()[0].name
        
        if group in allowed_roles:
          return view_func(request, *args, **kwargs)
        else:
          return HttpResponse('You are not authorized to view this page') 
    return wrapper_func
  return decorator
```

5번째 줄에 None 값을 가진 group을 만들게.   
6번째 줄에는 해당 유저가 그룹에 속해있는지 아닌지\(ex. admin, customer\) 확인해요.   
  
9번째의 allowed\_roles는 'admin' 값 하나만 가지고 있는 리스트 변수인데요. 이건 1번째 함수의 인자에요. 근데 저 인자는 비워져 있는데 그값은 어디서 가져오는 걸까요? 바로 views.py의 home 메서드의 decorator를 보면 allowed\_users라는 데코레이터 함수의 allowed\_roles의 값으로 'admin'하나만 정의된걸 확인할 수 있어요.   
  
그리고 10번째줄에서 admin에 해당하는 유저라면 view\_func 함수\( == view의 home 메서드\)를 돌려주며 호출하게되요.   
  
그런데 admin에 속하는 유저가 아니라면 11번째, 12번째 소스코드가 실행되게 되는거조.
{% endtab %}
{% endtabs %}

![&#xB85C;&#xADF8;&#xC778;&#xC2DC; admin&#xC5D0; &#xC18D;&#xD558;&#xC9C0; &#xC54A;&#xC740; &#xC720;&#xC800;&#xD654;&#xBA74;&#xC758; &#xD648;&#xD654;&#xBA74;](../../.gitbook/assets/image%20%28121%29.png)

![&#xB85C;&#xADF8;&#xC778;&#xC2DC; admin&#xC5D0; &#xC18D;&#xD55C; &#xC720;&#xC800;](../../.gitbook/assets/image%20%28125%29.png)

  
그럼 **@allowed\_users\(allowed\_roles=\['admin'\]\)  
다른 필요 로직에도 동일하게 적용할게요. 즉 관리자 권한이 있는 계정만 접근한 페이지를 설정한다는 말이에요.**

### **View - 4**

**@login\_required\(login\_url='login'\) 바로 아래에 @allowed\_users\(allowed\_roles=\['admin'\] \) 로직을 작성할게요.**

**작**

{% tabs %}
{% tab title="views.py" %}
```text
...

@login_required(login_url='login')
@allowed_users(allowed_roles=['admin'] )
def products(request):
    
...

@login_required(login_url='login')
@allowed_users(allowed_roles=['admin'] )
def customer(request):
...

@login_required(login_url='login')
@allowed_users(allowed_roles=['admin'] )
def createOrder(request):
...

@login_required(login_url='login')
@allowed_users(allowed_roles=['admin'] )
def updateOrder(request):
...

@login_required(login_url='login')
@allowed_users(allowed_roles=['admin'] )
def deleteOrder(request):

...
```
{% endtab %}
{% endtabs %}



한 가지 문제가 있는데요. 그럼 관리자 권한이 없는 유저가 로그인 할 때마다  home화면으로 빠져서 보고싶지 않은 화면을 봐야하는 문제에 직면해있어요.   


![&#xB85C;&#xADF8;&#xC778;&#xC2DC; admin&#xC5D0; &#xC18D;&#xD558;&#xC9C0; &#xC54A;&#xC740; &#xC720;&#xC800;&#xD654;&#xBA74;&#xC758; &#xD648;&#xD654;&#xBA74;](../../.gitbook/assets/image%20%28121%29.png)

그럼 로그인하고 home화면으로 접근 가능한건 오직 admin만 되서 정상적으로 data가 모두 나오게 해야겠조?



### decorator -4 

19번째줄부터 admin\_only 메서드를 정의해줬어요.  
말그대로 해당 페이지는 오직 관리자만 접근하도록 한다는 의미인데요.  
  
다른 로직들은 3번째줄에 구성된 allowed\_users 함수와 도찐개찐한데 25-26번째줄을 보면 redirect로 'user-page'로가게 설정해뒀어요. 즉 customer group에 속한 user는 user-page로 자동으로 가게 설정해서 아까처럼 쓰잘데기 없는 접근제한이 있다는 페이지를 볼 필요가 없조.



```text
...
...
def allowed_users(allowed_roles=[]):
  def decorator(view_func):
    def wrapper_func(request, *args, **kwargs):
    
        group = None
        if request.user.groups.exists():
          group = request.user.groups.all()[0].name
        
        if group in allowed_roles:
          return view_func(request, *args, **kwargs)
        else:
          return HttpResponse('You are not authorized to view this page') 
    return wrapper_func
  return decorator
  

def admin_only(view_func):
    def wrapper_function(request, *args, **kwargs):
        group = None
        if request.user.groups.exists():
            group = request.user.groups.all()[0].name
            
        if group == 'customer':
            return redirect('user-page')
            
        if group == 'admin':
            return view_func(request, *args, **kwargs)
            
    return wrapper_function
```



### View - 5

데코레이터에 로직 썻으면 그 이름표를 view에 달아줘야겠조?  
일단 admin\_only 메서드 import부터 할게요.  
  
데코레이터 한줄만 바꿧습니다. 그리고 서버 돌려서 customer Group에 등록된 유저로 로그인하면 자동으로 ~~~~/user/ 웹 url로 점프하는걸 볼수 있어요.  


{% tabs %}
{% tab title="전" %}
```text
from .decorators imort unauthenticated_user, allowed_users, admin_only

...
...


@login_required(login_url='login')
@allowed_users(allowed_roles=['admin'])
def home(request):

...
...
```
{% endtab %}

{% tab title="후" %}
```
from .decorators imort unauthenticated_user, allowed_users, admin_only

...
...




@login_required(login_url='login')
@admin_only
def home(request):
```
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%28127%29.png)

그런데 회원가입을 하고나서 해당 유저가 customer그룹인지 admin 그룹인지 그 값을 설정하는 것을 관리자 웹페이지에서 했던거 기억나나요?   
그 짓 하지 않고. 바로 가입하자마자 일반 고객은 customer 그룹에 자동으로 속하도록 로직을 짜볼게요.

### View - 5

django가 만든 Group 클래스를 import할게요.   
회원 가입할때 자동으로 해당 유저를 customer 그룹에 등록하려면 registerPage 메서드에서 진행해야겠조?

12번째줄에 user 변수로 회원가입 입력란의 저장받은 값들을 db에 저장하는 로직을 표현하고 13번째줄에 username을 form에서 땡겨와서 그 이름을 변수에 저장해요.   
  
15번째 줄에서는 import했던 Group 클래스의 쿼리를 활용해서 cusomer이름을 가져와 group이라는 변수에 담고 16번째줄에서 user 그룹 속성에 group에 추가시켜주게 되요. 17번째 소스코드를 통해서 Customer 클래스의 쿼리문에 create를 통해서 유저 생성이 이루어지게 되요.

```text
...
from django.contrib.auth.models import Group
...

unauthenticated_user
def registerPage(request):

	form = CreateUserForm()
	if request.method == 'POST':
		form = CreateUserForm(request.POST)
		if form.is_valid():
			user = form.save()
			username = form.cleaned_data.get('username')

			group = Group.objects.get(name='customer')
			user.groups.add(group)
			Customer.objects.create(
				user=user,
			)

			messages.success(request, 'Account was created for ' + username)
			return redirect('login')

	context = {'form':form}
	return render(request, 'accounts/register.html', context)
...
...
...
```

![](../../.gitbook/assets/image%20%28116%29.png)

회원가입후 로그인해 바로 정상적으로 작동되는지 확인해볼게요.

![](../../.gitbook/assets/image%20%28123%29.png)

보는 바와 같이 회원가입시 바로 자동으로 customer group에 해당되어 home화면에서 -&gt; /user/ 화면으로 자동 redirect되는걸 확인 할 수 있어요



### Template 

18번째의 조건문은 user가  is\_staff이라는 속성에 해당되면 dashboard와 product 버튼을 보여주지만 그렇지 않으면 보여주지 않아요.

{% tabs %}
{% tab title="navbar.html" %}
```text
{% load static %}

<style>
  #hello-msg{
    font-size: 18px;
    color: #fff;
    margin-right:20px;
  }
</style>

<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
 <img src="{% static 'images/logo.png' %}">
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNav">
    <ul class="navbar-nav">
      {% if request.user.is_staff %}
      <li class="nav-item active">
        <a class="nav-link" href="{% url 'home' %}">Dashboard</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="{% url 'products' %}">Products</a>
      </li>
      {% endif %}
    </ul>
  </div>
  <span id='hello-msg'>Hello, {{ request.user.username }} </span>
  <span><a class='hello-msg' href="{% url 'logout' %}">Logout</a> </span>
</nav>
```
{% endtab %}
{% endtabs %}

![customer &#xADF8;&#xB8F9;&#xC758; &#xC720;&#xC800; ](../../.gitbook/assets/image%20%28124%29.png)

![](../../.gitbook/assets/image%20%28118%29.png)

