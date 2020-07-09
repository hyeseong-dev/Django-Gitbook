# Inline Formsets

![](../../.gitbook/assets/image%20%2876%29.png)

  
create\_order페이지를 위와 같이 만들어 볼거에요.   
여러개의 상품을 선택하고 Status 역시 선택해서 한번 submit 버튼을 누르자마자 여러 값들을 생성하게 만들려는 거조.  
  
일단 불필요한 Create Order 버튼을 삭제할게요. 

  
  


![](../../.gitbook/assets/image%20%2882%29.png)



{% tabs %}
{% tab title="dashboard.html 전" %}
```text
...
...

<div class="col-md-7">
    <h5>LAST 5 ORDERS</h5>
    <hr>
    <div class"card card-body">
        <a class="btn btn-primary btn-sm-block" href="{% url 'create_order' %}">Create Order</a>
        <table class="table table-sm">
        ...
        ...
        
```
{% endtab %}

{% tab title="dashboard.html 후" %}
```
...
...

<div class="col-md-7">
    <h5>LAST 5 ORDERS</h5>
    <hr>
    <div class"card card-body">
      
        <table class="table table-sm">
        ...
        ...
        
```

요렇게 삭제해주면 되요.
{% endtab %}
{% endtabs %}

#### urls.py 

```text
from django.urls import path
from . import views


urlpatterns = [
 ...
 ...
 ...
    path('create_order/<str:pk>/', views.createOrder, name="create_order"),
    path('update_order/<str:pk>/', views.updateOrder, name="update_order"),
    path('delete_order/<str:pk>/', views.deleteOrder, name="delete_order"),

]
```

create\_order url부분에 &lt;str:pk&gt;를 넣어줬으니 view 역시 매개변수로 pk를 넣어줘야해요. 

#### views.py 

pk\(primary key\)를 메서드의 매개변수로 넣어줄게요.

```text
def createOrder(request, pk):
	customer = Customer.objects.get(id=pk)
	form = OrderForm(initial={'customer':customer})
	if request.method == 'POST':
		#print('Printing POST:', request.POST)
		form = OrderForm(request.POST)
		
		if form.is_valid():
			form.save()
			return redirect('/')

	context = {'form':form}
	return render(request, 'accounts/order_form.html', context)

```

dashboar html페이지로 가서 Delete Order -&gt; Place Order로 바꾸고 class값 역시 info로 바꿔주세요.

![](../../.gitbook/assets/image%20%2877%29.png)

그리고 보게되면 href 속성값이 비어있는데 장고 템플릿 문법으로 입력할게요.  {% url 'create\_order' customer.id %} 이거 입력하고 다시 클릭해보면 create\_order페이지로 연결되는 모습을 확인 할 수 있어요. 

![](../../.gitbook/assets/image%20%2880%29.png)

그런데 사실 John Doe라는 사용자 이름으로 주문을 하려는데 이름이 자동으로 선택되어야 겠조? 고렇게 되도록 해볼게요. 

{% tabs %}
{% tab title="views.py" %}
```text
...
...
...

def createOrder(request, pk):
    customer = Customer.objects.get(id=pk)
    form = OrderForm(intial={'customer':customer}
    
...
...
```
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%2884%29.png)

![](../../.gitbook/assets/image%20%2881%29.png)

![](../../.gitbook/assets/image%20%2879%29.png)

John Doe라는 이름이 자동으로 선택되어 있는게 보이네요. 

## inline formset

2번째줄에 froms 모듈의 onlineformset\_factory 메서드를 임포트할게요.   
  
createOrder 메서드로 가서 바로 아래 OrderFormSet 클래스 변수를 만들건데요. 방금 import한 inlineformset\_factory 메서드를 상속받을 건데요. 부모 클래스를 처음에 넣고, 다음 자식 클래스를 둘게요.  세번째 매개변수는 fields인데요. 자식 클래스의 클래스 변수를 한정지을수 있어요. 튜플로 작성해서 product, status 두개만 입력해 뒀어요. 

OrderFormSet변수이름과 동일하기 클래스처럼 쓰고 내부 매개변수로 instance를 작성하고 customer를 그대로 값으로 작성할게요 그 인스턴스는 formset으로 작성할게요.  


context 딕셔너리 변수 역시 내부 값을 formset으로 바꿀게요.

{% tabs %}
{% tab title="views.py" %}
{% code title="views.py" %}
```text
from django.shortcuts import render, redirect
from django.forms import inlineformset_factory
...
...


def createOrder(request, pk):
	OrderFormSet = inlineformset_factory(Customer, Order, fields = ('product', 'status')
	customer = Customer.objects.get(id=pk)
	formset = OrderFormSet(instance=customer)
	#form = OrderForm(initial={'customer':customer})
	if request.method == 'POST':
		#print('Printing POST:', request.POST)
		form = OrderForm(request.POST)
		
		if form.is_valid():
			form.save()
			return redirect('/')

	context = {'form':form}
	return render(request, 'accounts/order_form.html', context)

```
{% endcode %}
{% endtab %}

{% tab title="order\_form.html" %}
```
...
...
...

{{formset}}

...
...
```

기존 form -&gt; formset 으로 바꿀게요.
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%2883%29.png)

개떡같은 모습이지만 일단 등록 했던 상품의 data가 그대로 선택된 채로 나왔으며, 추가 입력도 가능한 form의 형태로 나왔어요.

#### template 

{% tabs %}
{% tab title="order\_form.html" %}
```text
...
...

{% csrf_token %}
{% for form in formset %}
    {{form}}
    <hr>
{% endfor %}

...

```
{% endtab %}
{% endtabs %}

![](../../.gitbook/assets/image%20%2878%29.png)

오와 열을 맞추고 그나마 간격이격까지 잘 된 모습이에요.   
추가로 view의 createOrder메서드를 수정해볼게요. 

**view  
9번째줄의 OrderFormSet에 request.POST 매개변수를 넣어줄게요.** 

{% tabs %}
{% tab title="views.py" %}
{% code title="views.py" %}
```text
def createOrder(request, pk):
	OrderFormSet = inlineformset_factory(Customer, Order, fields=('product', 'status'), extra=10)
	customer = Customer.objects.get(id=pk)
	formset = OrderFormSet(queryset=Order.objects.none(), instance=customer)
	# form = OrderForm(initial={'customer':customer})
	if request.method == 'POST':
		#print('Printing POST:', request.POST)
		# form = OrderForm(request.POST)
		formset = OrderFormSet(request.POST, instance=customer)
		if formset.is_valid():
			formset.save()
			return redirect('/')

	context = {'formset':formset}
	return render(request, 'accounts/order_form.html', context)

```
{% endcode %}
{% endtab %}

{% tab title="order\_form.html" %}
```
{%  extends 'accounts/main.html' %}
{% load static %}
{% block content %}


<div class="row">
	<div class="col-md-6">
		<div class="card card-body">

			<form action="" method="POST">
				{% csrf_token %}
				{{ formset.management_form }}
				{% for form in formset %}
					{{form}}
					<hr>
				{% endfor %}
				<input type="submit" name="Submit">
			</form>

		</div>
	</div>
</div>


{% endblock %}
```
{% endtab %}
{% endtabs %}











