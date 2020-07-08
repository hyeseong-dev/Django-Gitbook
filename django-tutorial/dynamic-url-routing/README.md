# Dynamic URL Routing

우리가 이번 시간에 할 것은 URL을 다이나믹하게 만들어 줄거에요.   
예를들어 게시판에 게시된 1번글을 보고 싶음 URL 끝에 /1/을 입력하는 경우가 있조? 고걸 구현 해보도록 할게요.   
  
accounts앱의 urls.py로 가볼게요. 

```text
from django.urls import path
from . import views


urlpatterns = [
    path('', views.home),
    path('products/', views.products),
    path('customer/<str:pk_test>/', views.customer),
]
```

별거 없어요 8번째 줄에 &lt;str:pk\_test&gt;/ 요렇게 만들어 주면되요. 예상 했다싶이 str은 string 문자열을 의미해요.   
이외에 int, slug 가 있는데 고건 따로 찾아보고 일단은 이렇게 적용 할 수 있어요.  
  
그래서 8번째 줄을 풀어 쓰면 customer함수에서 pk\_test라는 매개변수로 전달받게 되요.

{% tabs %}
{% tab title="views.py" %}
```text
```
```
```

def customer(request, pk_test):
    customer = Customer.objects.get(id=pk_test)
    return render(request, 'accounts/customer.html')
```

5번째 매개변수에 pk\_test는 웹 브라우저에 특정 숫자를 마지막으로 입력하게 되면 해당 페이지로 넘어가게 해줘요. \(만약 등록된 값이 존재한다면요.\)  
6번째 줄에 get\(\)메서드로 한개 값을 잡을건데 unique한 id값을 잡아준다는 말이에요. 
{% endtab %}
{% endtabs %}

오류가 발생한 이유는 1번 id값을 가진 고객이 존재하지 않기 때문이에요. 

![](../../.gitbook/assets/image%20%286%29.png)

그럼 이 id는 어디서 확인하냐. 일단 admin 관리자 웹페이지에서 확인 할수 있어요. 아래와 같이 가면 바로 확인 되요.   
등록된 고객중 아무나 클릭하면 브라우저 주소창에 숫자가 쓰여진거 보이조? 고거입니다.

![](../../.gitbook/assets/image%20%2852%29.png)

![](../../.gitbook/assets/image%20%2836%29.png)

이번에는 잘 출력 되는걸 확인 할 수 있어요.  
그런데 뭔가 밑밑한걸 확인 할 수 있어요. customer 페이지에 숫자도 넣고 글자도 넣을려면 View와 Template 작성이 필요해 보이네요. 

{% tabs %}
{% tab title="views.py" %}
```text
from django.shortcuts import render
from django.http import HttpResponse
# Create your views here.
from .models import *

````
```
```
def customer(request, pk_test):
	customer = Customer.objects.get(id=pk_test)

	orders = customer.order_set.all()
	order_count = orders.count()

	context = {'customer':customer, 'orders':orders, 'order_count':order_count}
	return render(request, 'accounts/customer.html',context)
```
{% endtab %}
{% endtabs %}

12, 13번째에 View를 조금 작성해봤는데요. 바로 Template에 적용 시켜 볼게요.  우리가 표현하고자 하는것은 customer.html을 수정하는거조?

{% tabs %}
{% tab title="customer.html" %}
```text
{%  extends 'accounts/main.html' %}

{% block content %}

...
...
...
<div class="row">
	<div class="col-md">
		<div class="card card-body">
			<table class="table table-sm">
				<tr>
					<th>Product</th>
					<th>Category</th>
					<th>Date Orderd</th>
					<th>Status</th>
					<th>Update</th>
					<th>Remove</th>
				</tr>

				{% for order in orders %}

				<tr>
					<td>{{order.product}}</td>
					<td>{{order.product.category}}</td>
					<td>{{order.date_created}}</td>
					<td>{{order.status}}</td>
					<td><a class="btn btn-sm btn-info" href="">Update</a></td>
					<td><a class="btn btn-sm btn-danger" href="">Remove</a></td>
				</tr>
				{% endfor %}

			</table>
		</div>
	</div>
</div>

{% endblock %}
```

21 - 31번째까지 &lt;tr&gt;, &lt;td&gt; 태그로 감싸서 작성할거에요.   
24번째 줄은 order객체의 외래키로 만든 product를  
25번째는 동일하게 product 클래스 변수인데 category를 가져와야해요. 이 녀석은 Products 클래스에 있어요. category 변수를 도트 연산자로 호출하고   
status는 Order클래스에 있어서 바로 호출하면 되요. 이외에 update와 remove 버튼을 만들어 줄건데요. class 속성에 부트 스트랩 값을 넣어서 색을 좀 입혀보도록 할게요. 
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%2851%29.png)

  
여전히 비워져 있는 곳이 군데 군데 보이네요. 채워 나가도록 할게요.   
Email, Phone 작성은 쉬워요. 

```text
{%  extends 'accounts/main.html' %}

{% block content %}

	<br>

<div class="row">
	<div class="col-md">
		<div class="card card-body">
			<h5>Customer:</h5>
			<hr>
			<a class="btn btn-outline-info  btn-sm btn-block" href="">Update Customer</a>
			<a class="btn btn-outline-danger  btn-sm btn-block" href="">Delete Customer</a>

		</div>
	</div>

	<div class="col-md">
		<div class="card card-body">
			<h5>Contact Information</h5>
			<hr>
			<p>Email: {{customer.email}}</p>
			<p>Phone: {{customer.phone}}</p>
		</div>
	</div>

	<div class="col-md">
		<div class="card card-body">
			<h5>Total Orders</h5>
			<hr>
			<h1 style="text-align: center;padding: 10px">{{order_count}}</h1>
		</div>
	</div>
</div>

...
...


{% endblock %}
```

22, 23번째 줄에 각각 템플릿 문법으로 customer.email, customer.phone을 작성해주면 깔끔하게되요.

![](../../.gitbook/assets/image%20%2867%29.png)

{% tabs %}
{% tab title="views.py" %}
```text
from django.shortcuts import render
from django.http import HttpResponse
# Create your views here.
from .models import *

````
```
```
def customer(request, pk_test):
	customer = Customer.objects.get(id=pk_test)

	orders = customer.order_set.all()
	order_count = orders.count()
 
	context = {'customer':customer, 'orders':orders, 'order_count':order_count}
	return render(request, 'accounts/customer.html',context)
```

13번째 줄에 로직을 작성해주고 context 딕셔너리에 키와 벨류를 작성해주세요.  그다음 templates 부분에 
{% endtab %}

{% tab title="customer.html" %}
```text
{%  extends 'accounts/main.html' %}

{% block content %}

...
...

	<div class="col-md">
		<div class="card card-body">
			<h5>Total Orders</h5>
			<hr>
			<h1 style="text-align: center;padding: 10px">{{order_count}}</h1>
		</div>
	</div>
</div>

...
...


{% endblock %}
```

12번째 줄에 템플릿 문법으로 order\_count를 작성하면되요. 
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%2830%29.png)

잘 출력되는게 보이네요.   
근데 update버튼과 Remove 버튼이 별로네요. 좀더 멋지게 스타일링 해볼게요. 

{% code title="customer.html " %}
```text
				{% for order in orders %}

				<tr>
					<td>{{order.product}}</td>
					<td>{{order.product.category}}</td>
					<td>{{order.date_created}}</td>
					<td>{{order.status}}</td>
					<td><a class="btn btn-sm btn-info" href="">Update</a></td>
					<td><a class="btn btn-sm btn-danger" href="">Remove</a></td>
				</tr>
				{% endfor %}

			</table>
		</div>
	</div>
</div>

{% endblock %}
```
{% endcode %}

아래와 같이 class 속성에 값을 할당해주면 잘 연출 할 수 있어요. 

```text
<td><a class="btn btn-sm btn-info" href="">Update</a></td>
<td><a class="btn btn-sm btn-danger" href="">Remove</a></td>
```

![](../../.gitbook/assets/image%20%2838%29.png)

