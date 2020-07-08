# Templates&Inheritance

Bootstrap을 이용하여 HTML, CSS, JS를 가져다 쓸게요. 

## Templates 생성 

html 파일을 담을 디렉토리부터 생성할게요. 이후 accounts/ html파일들을 만들게요.    
아래와 같은 디렉토리 구조와 최종 파일들을 구성해주세요. 

```text
- crm1
        - accounts
                - templates
                        - accounts
                                - dashboard.html
                                - customer.html
                                - main.html
                                - navbar.html
                                - products.html
                                - status.html
```



아래와 같이 dashboard 템플릿 만들게요. 

{% tabs %}
{% tab title="dashboard.html" %}
{% code title="dashboard.html" %}
```text
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CRM</title>
</head>
<body>
  <h1>Dashboard</h1>
</body>
</html>
```
{% endcode %}
{% endtab %}
{% endtabs %}

  
위의 소스코드가 잘 출력되어야하는데. 중간 브릿징 역할을 해주는게 있어야해요.   
뭘까요? views.py 녀석이에요. 

#### 

#### views.py 

render\(\)를 첫번째 줄에 임포트할게요. 그리고 home 메서드의 return부분에 기존 HttpResponse\(\)를 render\(\)로 바꾸어서 첫번째 매개변수에 request라는 요청을 넣어줍니다.   
  
여기서 request는 바로 브라우저 주소창에 주소넣고 엔터딱! 때리는 순간 유저의 요청\(request\)을 말하는거에요. 그게 어디로 가야하냐면 두번째 매개변수로 이어져야해요.   
  
두번째 매개변수가 바로 templates 디렉토리에서 accounts라는 디렉토리를 찾고 그안에서 dashboard.html 파일을 찾아서 렌더링 해주면 우리가 만들었던 html 파일이 request의 응답, response로 나오게되요.   
  
나머지 products 그리고 customer 함수도 동일한 원리로 돌아가도록 변경해준거요. 

{% code title="views.py" %}
```text
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def home(request):
	return render(request, 'accounts/dashboard.html')

def products(request):
	return render(request, 'accounts/products.html')

def customer(request):
	return render(request, 'accounts/customer.html')
```
{% endcode %}



views.py에 각각 products.html, customer.html을 이어줬으니 그 파일들을 만들어 볼게요.   
그전에 분명  HTML 템플릿을 상속 받는 방법에 대해 알아볼게요. 

## 템플릿 상

부모 템플릿을 만들게요. 이름은 main.html 

{% tabs %}
{% tab title="main.html" %}
{% code title="main.html" %}
```text
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CRM</title>
</head>
<body>
  {% block content %}
  {% endblock %}
</body>
</html>
```
{% endcode %}

서버를 실행한 이후 웹 브라우저에 각각 customer/ 와 products/를 입력해서 예상되는 결과가 출력되는지 확인해보세요. 정상적으로 출력된다면 좋지만 spell문제가 처음엔 나타날 확률이 높아요. 꼼꼼히 확인해보세요. 
{% endtab %}

{% tab title="dashboard.html" %}
```
{% extends 'accounts/main.html' %}

{% block content %}

<p>Dashboard</p>

{% endblock %}

```
{% endtab %}

{% tab title="products.html" %}
```
{% extends 'accounts/main.html' %}

{% block content %}

<p>Products</p>

{% endblock %}

```
{% endtab %}

{% tab title="customer.html" %}
```
{% extends 'accounts/main.html' %}

{% block content %}

<p>Customers</p>

{% endblock %}

```
{% endtab %}

{% tab title="navbar.html" %}
```
{% extends 'accounts/main.html' %}

{% block content %}

<p>Dashboard</p>

{% endblock %}

```
{% endtab %}
{% endtabs %}

navbar\(네비게이션바\).html 역시 main.html에 작성하여 각각의 child html파일에 상속 받아 사용할 수 있어요. 

#### include 사용 

9번째 줄에 include 단어를 적고 이후 accounts/디렉토리 밑에 navbar.html을 따옴표로 감싸줘서 연결 시켜주면 main.html에 삽입되어 부모템플릿이 적용된 어떤 자식 템플릿에서 자연스럽게 출력되어 보여지게 되요. 

{% tabs %}
{% tab title="main.html" %}
{% code title="main.html " %}
```text
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>CRM</title>
</head>
<body>
  {% include 'accounts/navbar.html' %}
  {% block content %}
  {% endblock %}
</body>
</html>
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Bootstrap으로 스타일링

#### navbar 스타일링 

그리고 Dashboard Products Pricing 단어 3개가 나오도록 기존 단어들은 지우고 수정할게요. 

{% code title="navbar.html" %}
```text
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNav">
    <ul class="navbar-nav">
      <li class="nav-item active">
        <a class="nav-link" href="#">Dashboard
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Products</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="#">Pricing</a>
      </li>
    </ul>
  </div>
</nav>
```
{% endcode %}

![](../.gitbook/assets/image%20%2820%29.png)

구조는 일단 가져왔습니다. 색을 입히고 배치와 크기 조절을 해야하겠어요.   
Bootstrap의 CDN을 가져다 쓸게요.   
아래 부분 copy해서 main.html 파일의 &lt;head&gt; &lt;/head&gt; 태그 안에 넣어주세요. 

![](../.gitbook/assets/image%20%2849%29.png)



{% tabs %}
{% tab title="main.html" %}
{% code title="main.html " %}
```text
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
  <title>CRM</title>
</head>
<body>
  {% include 'accounts/navbar.html' %}
  {% block content %}
  {% endblock %}
</body>
</html>
```
{% endcode %}

소스코드 복붙하고 실행하면 아래와 같이 예상된 출력 결과가 나와요. 
{% endtab %}
{% endtabs %}



![](../.gitbook/assets/image%20%2864%29.png)

그런데 흰색으로 출력되지 않고 검정색으로 navbar를 출력하고 싶다면?  
navbar.html에서 light -&gt; dark로 수정하여 주면 되요. bootstrap 사용법중 하나에요. 

{% tabs %}
{% tab title="전" %}
{% code title="navbar.html" %}
```text
<nav class="navbar navbar-expand-lg navbar-light bg-light">
  <a class="navbar-brand" href="#">Navbar</a>
...
...
...
</nav>
```
{% endcode %}
{% endtab %}

{% tab title="후" %}
```
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
  <a class="navbar-brand" href="#">Navbar</a>
...
...
...
</nav>
```
{% endtab %}
{% endtabs %}

![](../.gitbook/assets/image%20%2815%29.png)

검정색으로 잘 출력되는걸 볼수 있어요. 그리고 브라우저 화면폭을 줄여보면 햄버거 단추도 나와요. 그렇지만 클릭하면 변화가 없는데 javascript 소스코드를 넣어주지 않아서 그래요.  
이전 css를 넣었던것처럼 js 소스코드도 복붙해주세요.   




{% tabs %}
{% tab title="main.html" %}
{% code title="main.html " %}
```text
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css" integrity="sha384-9aIt2nRpC12Uk9gS9baDl411NQApFmC26EwAOH8WgZl5MYYxFfc+NcPb1dKGj7Sk" crossorigin="anonymous">
  <title>CRM</title>
</head>
<body>
  {% include 'accounts/navbar.html' %}
  {% block content %}
  {% endblock %}
</body>

<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/js/bootstrap.min.js" integrity="sha384-OgVRvuATP1z7JjHLkuOU7Xw704+h835Lr+6QL9UvYjZE3Ipu6Tp75j7Bh/kR0JKI" crossorigin="anonymous"></script>

</html>
```
{% endcode %}

&lt;/body&gt; 태그 바로 아래에 붙여넣어주세요.   
{% endtab %}
{% endtabs %}

#### Dashboard 템플릿 추가 수정 

```text
{%  extends 'accounts/main.html' %}

{% block content %}

{%  include 'accounts/status.html' %}

<br>

<div class="row">
	<div class="col-md-5">
		<h5>CUSTOMERS:</h5>
		<hr>
		<div class="card card-body">
			<a class="btn btn-primary  btn-sm btn-block" href="">Create Customer</a>
			<table class="table table-sm">
				<tr>
					<th></th>
					<th>Customer</th>
					<th>Orders</th>
				</tr>

			</table>
		</div>
	</div>

	<div class="col-md-7">
		<h5>LAST 5 ORDERS</h5>
		<hr>
		<div class="card card-body">
			<a class="btn btn-primary  btn-sm btn-block" href="">Create Order</a>
			<table class="table table-sm">
				<tr>
					<th>Product</th>
					<th>Date Orderd</th>
					<th>Status</th>
					<th>Update</th>
					<th>Remove</th>
				</tr>
		
			</table>
		</div>
	</div>

</div>

{% endblock %}
```

위 소스코드를 입력하고 아래와 같이 잘 출력되면 성공~!

![](../.gitbook/assets/image%20%282%29.png)

#### status 템플릿 생성후 dashboard 삽입

{% code title="status.html" %}
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
			    	<h3 class="card-title"></h3>
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
			    	<h3 class="card-title"></h3>
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
			    	<h3 class="card-title"></h3>
			  	</div>
			</div>
		</div>
	</div>
</div>
```
{% endcode %}

status를 삽입하고 출력하면 흰색 계열이라 잘 안보이는데 css를 넣어볼게요. 

&lt;style&gt;&lt;/style&gt;태그로 감싸고 안에 소스코드를 넣었어요. 

{% tabs %}
{% tab title="main.html" %}
{% code title="" %}
```text
<!DOCTYPE html>
<html>
<head>
	<title>CRM</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">


	<style>
		
		#logo{
		}

		body{
			background-color: #ebeff5;
		}


		#total-orders{
			background-color: #4cb4c7;
		}


		#orders-delivered{
			background-color: #7abecc;
		}

		#orders-pending{
			background-color: #7CD1C0;
		}




	</style>
</head>
<body>
	{%  include 'accounts/navbar.html' %}
	<div class="container-fluid">
	{% block content %}



	{% endblock %}
    </div>
	<hr>
	<h5>Our footer</h5>
	
</body>

<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
</html>
```
{% endcode %}
{% endtab %}

{% tab title="customer.html" %}
```
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
			<p>Email: </p>
			<p>Phone: </p>
		</div>
	</div>

	<div class="col-md">
		<div class="card card-body">
			<h5>Total Orders</h5>
			<hr>
			<h1 style="text-align: center;padding: 10px"></h1>
		</div>
	</div>
</div>


<br>
<div class="row">
	<div class="col">
		<div class="card card-body">
			<form method="get">

		    <button class="btn btn-primary" type="submit">Search</button>
		  </form>
		</div>
	</div>
	
</div>
<br>

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

			</table>
		</div>
	</div>
</div>

{% endblock %}
```
{% endtab %}

{% tab title="" %}
```
{%  extends 'accounts/main.html' %}

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
						
					</table>
				</div>
			</div>
			
		</div>

{% endblock content %}
```
{% endtab %}
{% endtabs %}





