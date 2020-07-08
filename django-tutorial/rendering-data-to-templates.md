# Rendering Data to Templates

{% tabs %}
{% tab title="views.py" %}
```text
total_orders = orders.count()
	delivered = orders.filter(status='Delivered').count()
	pending = orders.filter(status='Pending').count()

```
{% endtab %}
{% endtabs %}

## View 작성

```text
from django.shortcuts import render, redirect 
from django.http import HttpResponse

from .models import *

def home(request):
return render(request, 'accounts/dashboard.html')

def products(request):
	products = Product.objects.all()

	return render(request, 'accounts/products.html', {'products':products})

def customer(request, pk_test):
	return render(request, 'accounts/customer.html',context)

```

  
우선 처음 Product.objects.all\(\) 쿼리를 통해서 테이블의 모든 내용을 products 변수로 받아옵니다. 그다음 render\(\)메서드에 dictionary의 키값으로 products로 지정하고\( 여기 키이름은 template으로 넘어갈때 땡겨오게되요. 원하는 이름 아무거나 지정해도 되요.\) 이후 value값은 products 메서드안에서 만든 변수를 그대로 적어주면되요.   
  
'products' 키는 products.html 템플릿에서 사용할수 있고 products value는 메서드에서 정의된 변수 이름을 그대로 적어준거에요.

{% tabs %}
{% tab title="products.html" %}
```text
{% block content %}

		<br>

		<div class="row">
			<div class="col-md">
				<div class="card card-body">
					<h5>Products</h5>
				</div>
				<div class="card card-body">
					<table class="table">
						<tr>
							<th>Product</th>
							<th>Category</th>
							<th>Price</th>
						</tr>

						{% for i in products %}
							<tr>
								<td>{{i.name}}</td>
								<td>{{i.category}}</td>
								<td>{{i.price}}</td>
							</tr>
						{% endfor %}
						
					</table>
				</div>
			</div>
			
		</div>

{% endblock content %}
```
{% endtab %}
{% endtabs %}

18번째부터 24번째까지 for문을 작성해서 제품 이름, 카테고리, 가격을 넣어볼게요.  유의 할 점은 &lt;tr&gt;, &lt;td&gt;태그로 감싸줘야해요. 

![](../.gitbook/assets/image%20%2865%29.png)

그럼 아래와 같이 웹페이지에서 출력되는걸 확인 할 수 있어요. 

## Dashboard template 

{% tabs %}
{% tab title="views.py" %}
```text
from django.shortcuts import render
from django.http import HttpResponse
# Create your views here.
from .models import *


def home(request):
	orders = Order.objects.all()
	customers = Customer.objects.all()


	context = {'orders':orders, 'customers':customers }

	return render(request, 'accounts/dashboard.html', context)

def products(request):
	products = Product.objects.all()

	return render(request, 'accounts/products.html', {'products':products})

def customer(request):
	return render(request, 'accounts/customer.html')
```
{% endtab %}
{% endtabs %}

8, 9번째에 각각 Order, Customer 클래스의 객체인 orders, customers 만들어 줄게요.   
  
이전에 render\(\) 메서드에 dictionary를 넣었는데 이번엔 딕셔너리 변수명을을 삽입하고 원하는 수의 딕셔너리의 키값과 value를 넣어서 부피를 늘릴수 있도록 해볼게요.   
  
처음엔 context = {} 라는 딕셔너리를 작성하고 이후 클래스의 객체를 딕셔너리에 삽입하도록 할게요. context = {'orders':orders, 'customers':customers }  
  
아무래도 이 작업이 너무 추상적일 수 있으니 우선 템플릿 작성으로 좀더 피부에 와닿게 파악하도록 할게요. 

{% tabs %}
{% tab title="dashboard.html" %}
```text
{%  extends 'accounts/main.html' %}

{% block content %}

{%  include 'accounts/status.html' %}
...
...
			<table class="table table-sm">
				<tr>
					<th>Customer</th>
					<th>Phone</th>
				</tr>

				{% for customer in customers %}
					<tr>

						<td>{{customer.name}}</td>
						<td>{{customer.phone}}</td>
					</tr>
				{% endfor %}
...
...
```
{% endtab %}
{% endtabs %}

![](../.gitbook/assets/image%20%2840%29.png)

좌측 부분에 잘 나타난게 보이네요.   
이제 화면의 우측 부분도 작성해서 데이터값을 채워볼게요. 

```text
				<tr>
					<th>Product</th>
					<th>Date Orderd</th>
					<th>Status</th>
					<th>Update</th>
					<th>Remove</th>
				</tr>

				{% for order in orders %}
					<tr>
						<td>{{order.product}}</td>
						<td>{{order.date_created}}</td>
						<td>{{order.status}}</td>
						<td><a href="">Update</a></td>
						<td><a href="">Delete</a></td>

					</tr>
				{% endfor %}

		
			</table>
		</div>
	</div>

</div>

{% endblock %}
```

9번째 ~ 18번째 줄까지 주문 영역을 작성했어요.   
order.product  
order.date\_created  
order.status  
&lt;a href=""&gt;Update&lt;/a&gt;  
&lt;a href=""&gt;Delete&lt;/a&gt;  
  


![](../.gitbook/assets/image%20%2863%29.png)

  
이번에는 Total Orders, Orders Deliverd, Orders Pending으로 쓰여진 부분에 숫자를 넣을 수 있도록 할게요.   


{% tabs %}
{% tab title="views.py" %}
```text
from django.shortcuts import render
from django.http import HttpResponse
# Create your views here.
from .models import *


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

def products(request):
	products = Product.objects.all()

	return render(request, 'accounts/products.html', {'products':products})

def customer(request):
	return render(request, 'accounts/customer.html')
```
{% endtab %}
{% endtabs %}

Djnago queriest에서 배웠던 cout\(\), filter\(\)를 활용해서 총주문, 배달, pending된 값들을 출력할수 있어요.   
  
total\_orders = orders.count\(\)   
delivered = orders.filter\(status='Delivered'\).count\(\)   
pending = orders.filter\(status='Pending'\).count\(\)  
  
이제 만들었는데 context 딕셔너리 변수에 키와 벨류값을 넣어주세요. 17-19번째 줄처럼 넣어줄게요.

#### status 템플릿 작성  

로직을 작성했는데 템플릿 구현으로 마무리 해볼게요. 

```text
<br>

<div class="row">
	<div class="col">
		<div class="col-md">
			<div class="card text-center text-white  mb-3" id="total-orders">
			  	<div class="card-header">
			  		<h5 class="card-title">Total Orders</h5>
			  	</div>
			  	<div class="card-body">
			    	<h3 class="card-title">{{total_orders}}</h3>
			  	</div>
			</div>
		</div>
	</div>

	<div class="col">
		<div class="col-md">
			<div class="card text-center text-white  mb-3" id="orders-delivered">
			  	<div class="card-header">
			  		<h5 class="card-title">Orders Delivered</h5>
			  	</div>
			  	<div class="card-body">
			    	<h3 class="card-title">{{delivered}}</h3>
			  	</div>
			</div>
		</div>
	</div>

	<div class="col">
		<div class="col-md">
			<div class="card text-center text-white  mb-3" id="orders-pending">
			  	<div class="card-header">
			  		<h5 class="card-title">Orders Pending</h5>
			  	</div>
			  	<div class="card-body">
			    	<h3 class="card-title">{{pending}}</h3>
			  	</div>
			</div>
		</div>
	</div>
</div>
```

11, 24, 37번째 줄에 context 딕셔너리에 추가했던 값을 템플릿의 title과 잘 매칭해서 입력해주세요.   


![](../.gitbook/assets/image%20%2858%29.png)

