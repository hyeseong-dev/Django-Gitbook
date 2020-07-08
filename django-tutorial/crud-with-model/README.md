# CRUD with Model

Create - Read - Update - Delete  
웹페이지에 이 4가지 기능을 구현한다는게 뭘까요?   
바로 버튼 이름을 보면 create , update, remove 보이조? 고걸 어떻게 사부작 한다는 말이에요.  


![](../../.gitbook/assets/image%20%2812%29.png)

### Template 생성 

create와 update 기능을 갖출 페이지를 만들어 볼게요.   
**order\_form.html** 페이지를 만들게요.   


{% tabs %}
{% tab title="order\_form.html" %}
```text
{%  extends 'accounts/main.html' %}
{% load static %}
{% block content %}


			<form action="" method="POST">
				{% csrf_token %}
				{{form}}
				
				<input type="submit" name="Submit">
			</form>


{% endblock %}
```
{% endtab %}
{% endtabs %}

### View 생성

```text
```
```
```

def createOrder(reqeust):

    context = {}
    return render(request, 'caccounts/order_form.html', context)
```

### urls 연결 

```text
from django.urls import path
from . import views


urlpatterns = [
    path('', views.home, name="home"),
    path('products/', views.products, name='products'),
    path('customer/<str:pk_test>/', views.customer, name="customer"),

    path('create_order/', views.createOrder, name="create_order"),
    

]
```

웹브라우저에서 ~~~/create\_order/를 딱! 치면 views 모듈의 createOrder 메서드를 호출하게되요. 추가로 name옵션에 값은 url 이름과 동일하게 문자로 작성할게요. 

#### 기존 Dashboard 템플릿과 order\_form 템플릿 연동 

![](../../.gitbook/assets/image%20%2848%29.png)

![](../../.gitbook/assets/image%20%281%29.png)

잘 연결되는게 보이나요? 이렇게 연결되면 되요.   
그런데 연결은 되었는데 많이 부족한 모습이 보이네요.   
  
파이썬 방식으로 form을 만드는 방법으로 조금더 보완해보도록 할게요.

### form.py 

이 곳이 바로 우리가 만든 model form이 설정되는 모듈이에요.

```text
from django.forms import ModelForm
from .models import Order


class OrderForm(ModelForm):
	class Meta:
		model = Order
		fields = '__all__'
```

첫 번째줄은 django.froms에 ModelForm이라는 클래스를 가져다 쓸거에요.   
그리고 두 번째줄에 우리가 만든 model도 가져다가 연결 시켜줘야해요.   
  
5번째줄에 OrderForm이라고 임의로 정한 클래스를 만들고 첫째줄에 임포트한 ModelFrom을 상속받을게요.   
6째줄에 Meta클래스를 만들고 그 안에다가 model과 fields Meta 클래스의 변수를 정의하도록 할게요.   


model 변수는 우리가 어떤 모델을 만들지 정해주는거에요. Order라는 테이블을 받아서 정해주는거조.  
fields 변수는 방금 정의한 모델\(테이블\)의 변수들을 어떤걸 받을지 정해주는거에요.  여기서는 전부다 받겠다고 해서 문자열로 "\_\_all\_\_"받았는데, 

> 만약 몇개만 받아서 form을 구성하고 싶으면 fields = \[ "customer', "product", "date\_created" \] 3개만 구성해도 되요.

위에서 forms.py파일로 만든건 당근 template에 form태그를 만들어서 어떻게 표현 할려고 로직을 짠건데 바로 실행은 안되요.   
  
template으로 갈려면 View를 거쳐야한다는점. 

### View 

아래 소스 코드를 설명하면 우선 5번째 줄에 방금 우리 작성한 OrderForm을 임포트할게요.  

{% tabs %}
{% tab title="views.py" %}
```text
from django.shortcuts import render, redirect 
from django.http import HttpResponse
# Create your views here.
from .models import *
from .forms import OrderForm

...
...
...

def createOrder(request):
	form = OrderForm()

	context = {'form':form}
	return render(request, 'accounts/order_form.html', context)

```

12번째 줄에 우리가 작성한 클래스를 적고 form 객체를 만들어주고 이를 context 딕셔너리 변수에 넣어주면 되요. 
{% endtab %}
{% endtabs %}

### template

{% tabs %}
{% tab title="order\_form.html" %}
```text
{%  extends 'accounts/main.html' %}
{% load static %}
{% block content %}

			<form action="" method="POST">
				{% csrf_token %}
				{{form}}
				
				<input type="submit" name="Submit">
			</form>




{% endblock %}
```

POST 방식으로 전달하게될 경우 안전하게 data를 전달하고자 csrf\_token을 6번째 줄에 작성 하도록 할게요.   
그리고 바로 아래 7번째 줄에 form 변수를 바로 적을게요.   
  
그리고 랜더링한 결과가 아래 스샷처럼 잘 나오게되요.
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%2816%29.png)



#### views.py 

#### createOrder메서드 수정 

3번째 줄에 if 문은 POST 방식이 요청될 경우라는 조건인데요.   
이 의미는 submit 버튼을 누를 경우 POST 방식의 요청인지 아닌지 파악하는 조건부분이에요.  


```text
def createOrder(request):
	form = OrderForm()
	if request.method == 'POST':
		print('Printing POST:', request.POST)

	context = {'form':form}
	return render(request, 'accounts/order_form.html', context)
```

![](../../.gitbook/assets/image%20%2856%29.png)

Submit 버튼 클릭시 어떻게 data가 전달 되는지 한번 볼게요.   


![](../../.gitbook/assets/image%20%2844%29.png)

csrf\_token 값부터 status 값까지 잘 나타나는게 보이네요. 



```text
from django.shortcuts improt render, redirect

def createOrder(request):
	form = OrderForm()
	if request.method == 'POST':
		#print('Printing POST:', request.POST)
		form = OrderForm(request.POST)
		if form.is_valid():
			form.save()
			return redirect('/')

	context = {'form':form}
	return render(request, 'accounts/order_form.html', context)
```

6번째 줄에 is\_valid\(\) 메서드를 통해서 form의 유효성을 검사해요.  
customer, product, status 3개가 모두 완전히 입력되어야한다는 말이에요.   
만약 1개나 2개, 혹은 입력하지 않고 submit 누르게 될경우 is\_valid\(\)메서드에서 나가리가 되는거에요.

다음 유효성이 완전하면 save\(\)메서드를 통해서 db에 저장하게 되요.   
저장을 db에 했으면 화면의 변화를 어떻게 줄까요?   
home페이지로 돌아가도록 할게요. 대신 render\(\)를 쓰지 않고  
  
10번째줄처 redirect\(\)를 사용하도록 할게요. 

> [**redirect 사용법 따로 포스팅 했으니 참고하세요.**  ](https://osori-magu.gitbook.io/django/django-tutorial/crud-with-model/redirect-v.s.-render)



## update 버튼 링크 달기 

### update view 작성 

{% tabs %}
{% tab title="views.py" %}
```text
def createOrder(request):
...
...
...
	

def updateOrder(request, pk):

	order = Order.objects.get(id=pk)
	form = OrderForm(instance=order)

	if request.method == 'POST':
		form = OrderForm(request.POST, instance=order)
		if form.is_valid():
			form.save()
			return redirect('/')

	context = {'form':form}
	return render(request, 'accounts/order_form.html', context)
```
{% endtab %}
{% endtabs %}

### urls.py 

create\_order를 작성한것 처럼 여기도 동일하게 create -&gt; update라는 이름으로 바꾸어서 추가하도록 할게요. 그리고 다이나믹하게 만들어 줄거에요. 꺽쇠 괄호로 str:pk를 넣어주세요.

```text
from django.urls import path
from . import views


urlpatterns = [
    path('', views.home, name="home"),
    path('products/', views.products, name='products'),
    path('customer/<str:pk_test>/', views.customer, name="customer"),

    path('create_order/', views.createOrder, name="create_order"),
    path('update_order/<str:pk>', views.updateOrder, name="update_order"),
    
]
```

### template 

6번째, 7번째 줄의 href속성 값을 django template으로 입힌다음 값을 넣을거에요. {% url 'update\_order' order.id %} 를 작성해주면 되요.   


```text
				{% for order in orders %}
					<tr>
						<td>{{order.product}}</td>
						<td>{{order.date_created}}</td>
						<td>{{order.status}}</td>
						<td><a class="btn btn-sm btn-info" href="{% url 'update_order' order.id %}">Update</a></td>
						<td><a class="btn btn-sm btn-danger" href="{% url 'delete_order' order.id %}">Delete</a></td>

					</tr>
				{% endfor %}
```

![](../../.gitbook/assets/image%20%2872%29.png)

위 사진의 update 버튼을 클릭하게 되면 아래와 같이 잘 출력되는게 보이네요. 

![](../../.gitbook/assets/image%20%2825%29.png)

 그래도 미흡한 점이 보여요. 바로 update라면 수정을 의미하는데 기존의 form영역에 이미 설정된 값이 선택되어서 나와야되겠조?

### view 수정 

3번째 줄에 Order 클래스의 객체를 만들어 변수로 만들고,   
4번째줄에 OrderForm클래스를 상속받고 instance 매개변수의 값으로 작성한 order 객체를 값으로 넣어서 from 객체를 다시 만들어 줍니다. 이를 context 딕셔너리의 값에 삽입해주면 되요.

```text
def updateOrder(request, pk):

    order = Order.objects.get(id=pk)
    form = OrderForm(instance=order)
    context = {'form':form}
    return render(request, 'accounts/order_form.html', context)
    
```

![](../../.gitbook/assets/image%20%2861%29.png)

update버튼을 누르자마자 기존 저장되었던 초기 값들이 잘 나오네요.   
이제 Submit 버튼을 누르고 해당 값이 db에 저장되도록 해야겠조?    
기존 createOrder\(\)메서드에서 작성한 로직과 동일하게 db로 저장하는 부분을 복붙해서 작성하면 깔끔히 처리 할 수 있어요.  

```text
def updateOrder(request, pk):

    order = Order.objects.get(id=pk)
    form = OrderForm(instance=order)

    if request.method == 'POST':
        form =OrderForm(request.POST, instance=order)
        if form.is_valid():
            form.save()
            return redirect('/')    
        
    context = {'form':form}
    return render(request, 'accounts/order_form.html', context)
    
```

createOrder\(\)메서드와 db에 save하는  로직의 차이를 보면 매개변수의 두번째에 instance=order라는 값이 설정된걸 볼 수 있어요.  
요걸 해줘야 해당 지정된 객체에 값을 바로 수정해준다는점이라는거!   
이제 잘 되는지 확인해 볼게요.   
  


![](../../.gitbook/assets/image%20%2873%29.png)

Delivered라고 상태가 잡혀있는데 이를 아래와 같이 Pending으로 바꿔볼게요.   


![](../../.gitbook/assets/image%20%2824%29.png)

![](../../.gitbook/assets/image%20%288%29.png)

잘 바뀐게 확인되네요. 

## Delete 구

### template 생성 

아래와 같이 delete.html 템플릿을 생성할게요. main화면에 delte 버튼 클릭하면 나오는 페이지를 만드는거에요. 

```text
{%  extends 'accounts/main.html' %}
{% load static %}
{% block content %}

			<p>Are you sure you want to delete "{{item}}"?</p>

			<form  method="POST">
				
				<a href="{% url 'home' %}">Cancel</a>

				<input type="submit" name="Confirm">
			</form>


{% endblock %}
```

### view

request 요청 당근 들어가고 specific한 data를 지우는거니 pk매개변수도 넣어줄게요. 사전에 만들어 줬던 createOrder, updateOrder와 동일하게 소스코드를 간단하게 작성해줄게요.

```text
def delteOrder(request, pk):

    order = Order.objects.get(id.pk)
    context = {'order':order}
    return render(reqeust, 'accounts/deletes.html', context)
```

### url.py 

delete버튼을 클릭하자 마자 일어나는 변화인데요. 바로 주소창에는 delete\_order주소갑과 해당 order id값이 잡히고 view에서 delteOrder메서드가 호출되요. 

```text
...
...
path('delete_order/<str:pk>', views.deleteOrder, name='delete_order'),
```

그런데 delete 버튼에 href 속성값을 입력하지 않았네요. 입력하도록 할게요. 

{% tabs %}
{% tab title="dashboard.html" %}
```text

		...
	...
	...
	
	
	<td><a class="btn btn-sm btn-info" href="{% url 'update_order' order.id %}">Update</a></td>
	<td><a class="btn btn-sm btn-danger" href="{% url 'delete_order' order.id %}">Delete</a></td>
	
	...
	...
	...
	
```

dashboard화면에서 delte 버튼 누르게 되면 아래와 같이 출력되어야 해요. 

![](../../.gitbook/assets/image%20%2819%29.png)

그런데 "Order object\(2\)"라고 출력되는데요 이는 models.py의 Order클래스 안에 def \_\_str\_\_\(self\) 메서드를 정의해 주지 않아서 그래요. 
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%2842%29.png)

![](../../.gitbook/assets/image%20%2837%29.png)

상품이름은 잘 출력된걸 확인할수 있어요. 그리고 Cancel 버튼을 누를 경우 홈화면으로 돌아가는걸 확인할수 있지만 Submit버튼을 누르면 오류가 나타나요.   
csrf\_token을 넣어주지 않았기 때문이에요. 

```text
{%  extends 'accounts/main.html' %}
{% load static %}
{% block content %}

			<p>Are you sure you want to delete "{{item}}"?</p>

			<form  method="POST">
				
				{% csrf_token %}
				<a href="{% url 'home' %}">Cancel</a>

				<input type="submit" name="Confirm">
			</form>


{% endblock %}
```

그럼 item이 잘 삭제 된걸 확인 할 수 있어요. 그런데 지금 삭제 화면의 버튼 꼴을 보면 말이 아니에요. 적어도 Dash보드의 화면의 버튼처럼 나와야하지 않을까요? 

#### dashboard.html 

블락된 부분을 복사해서 delete.html에 복붙할건데요.  그전에 customer페이지도 통일성을 위해서 동일한 양식과 글자를 적용할게요. 

![](../../.gitbook/assets/image%20%2868%29.png)

![](../../.gitbook/assets/image%20%2859%29.png)

{% code title="delete.html" %}
```text
{%  extends 'accounts/main.html' %}
{% load static %}
{% block content %}

<br>
<div class="row">
	<div class="col-md-6">
		<div class="card card-body">


			<p>Are you sure you want to delete "{{item}}"?</p>

			<form action="{% url 'delete_order' item.id  %}" method="POST">
				
				{% csrf_token %}
				<a class="btn btn-warning" href="{% url 'home' %}">Cancel</a>

				<input class="btn btn-danger" type="submit" name="Confirm">
			</form>
		</div>
	</div>
</div>

{% endblock %}
```
{% endcode %}

div태그를 이용해서 bootstrap을 입혔어요. 또한 class 속성값을 넣어줘서 기존 보다 더 스타일리쉬하게 연출했답니다.

![](../../.gitbook/assets/image%20%2834%29.png)

이전보다 한결 보기 좋아졌어요.

#### order\_form.html 

이 페이지도 보기 좋게 바꿀게요. 

```text
{%  extends 'accounts/main.html' %}
{% load static %}
{% block content %}


<div class="row">
	<div class="col-md-6">
		<div class="card card-body">

			<form action="" method="POST">
				{% csrf_token %}
				{{form}}
				
				<input type="submit" name="Submit">
			</form>

		</div>
	</div>
</div>


{% endblock %}
```

