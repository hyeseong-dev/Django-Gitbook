# Filter Form Table Search

## 이번에 할 것? 

Multi Parameter Search Filter using django filter package  
위에꺼 만들건데 말그대로 **검색 filter 기능**을 말들거란 말이에요.  
구현은 아래와 같이 할거에요. 

![](../.gitbook/assets/image%20%2890%29.png)

### 패키지 설치 

저 기능을 구현하려면 django-filter라는 패키지 설치를 해야해요. 

```text
$ pip install django-filter
```

이 패키지를 설치했음 우리의 프로젝트에 등록시켜줘야해요. 

#### 앱 등록 목록에 작성

{% code title="settings.py" %}
```text
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    
    'accounts',
    'django-filter',
]
```
{% endcode %}

#### filters.py 생성 - 1

{% tabs %}
{% tab title="accounts/filters.py" %}
```text
import django_filters

from .models import * 

class OrderFilter(django_filters.FilterSet):
    class Meta:
        model = Order
        fields = '__all__'
        
```
{% endtab %}
{% endtabs %}

1번째줄에 우리가 설치한 패키지를 임포트 할게요.   
2번째줄은 우리가 만든 model 객체들을 전부\( \* \) import 할건데요.   
django\_filter 패키지로 만든 것들과 연결해서 filter 기능을 다채롭게 구현하고 연결해주기 위해서에요.   
  
5번째줄에 임의로 지정한 OrderFilter클래스를 정의하고 django\_filters 모듈의 FilterSet 클래스를을 상 속받을게요.   
  
6번째줄에 Meta 클래스를 정의할건데요. 이 클래스는 최소로 model, fields라는 2가지 속성을 가져야 해요. model의 경우는 우리가 만들고자 하는 모델 객체를 값으로 정하고, fields의 개수를 정할건데 models.py의 Order클래스의 모든 클래스 변수를 끌어다 쓸거에요. 전체를 의미하는 '\_\_all\_\_'로 값을 지정하면 되요.

#### view 작성 - 1

#### filters 모듈의 클래스 View에 임포트

```text
...
...
from .forms import OrderForm
from .filters import OrderFilter

...
...

def customer(request, pk_test):
	customer = Customer.objects.get(id=pk_test)

	orders = customer.order_set.all()
	order_count = orders.count()

	myFilter = OrderFilter()


	context = {'customer':customer, 'orders':orders, 'order_count':order_count,
	'myFilter':myFilter}
	return render(request, 'accounts/customer.html',context)
```

15번째 줄에 OrderFilter클래스를 정의할게요. 그리고 customer.html을 작성하도록 할게요. Search라는 버튼이 작성된 곳에 6번째줄에 myFilter

{% code title="customer.html" %}
```text
<br>
<div class="row">
	<div class="col">
		<div class="card card-body">
			<form method="get">
					{{ myFilter }}
		    <button class="btn btn-primary" type="submit">Search</button>
		  </form>
		</div>
	</div>
	
</div>
<br>
```
{% endcode %}

그럼 객체에 대한 값이 출력되요. 하지만 부족해요. 도트 연산자와 함께 form을 써주세요.  


![](../.gitbook/assets/image%20%2886%29.png)

아래에 드디어 이제 우리가 원하던 것에 한발자국 다가 갔습니다.

![](../.gitbook/assets/image%20%2892%29.png)

#### view 작성 -2 

```text
...
...
from .forms import OrderForm
from .filters import OrderFilter

...
...

def customer(request, pk_test):
	customer = Customer.objects.get(id=pk_test)

	orders = customer.order_set.all()
	order_count = orders.count()

	myFilter = OrderFilter(request.GET, queryset=orders)
	order = myFilter.qs
	
	context = {'customer':customer, 'orders':orders, 'order_count':order_count,
	'myFilter':myFilter}
	return render(request, 'accounts/customer.html',context)
```

OrderFilter 클래스의 매개변수로 request.GET요청을 받은 이유는 굳이 POST 방식을 할 필요가 없기 때문이에요. 왜? 비밀번호도 아니고 개인정보도 아니며 웹주소창에 벗어날정도의 긴 string을 요구하지 않기 때문이조. 

두 번째 매개변수는 queryset 속성을 넣는데 값은 orders로 받게되요.  
16번째줄에 myFilter의 속성인 qs\(queryset\)을 호출하여 order 객체로 만들어 줍니다.

![](../.gitbook/assets/image%20%2893%29.png)

1번 선택을하고 2번 search하면 3번의 결과물이 나타나고 추가적으로 해당 정보에대한 접근방식을 url 주소창에 표현해줍니다.   
  
수정해야 할 점이 있네요. customer 선택란이 있는데 굳이 이미 john으로 왔는데 필요 없조. 그리고 기간에대한 filter기능 추가도 필요해요.

#### filter.py -2 

필터에 날짜 기능을 구현해 볼게요. 우선 필요한 패키지와 클래스를 2번째 줄과 같이 작성한것처 import해줄게요. 

```text
import django_filters
from django_filters import DateFilter, CharFilter
from .models import *

	class Meta:
		model = Order
		fields = '__all__'
		exclude = ['customer', 'date_created']
```

그리고 필터 기능에서 필요없는 form 몇가지가 있었어요. customer와 date\_create 인데요. 

13번째줄에 exclude라는 리스트 변수를 만들어서 Order 클래스에서 필요 없는 부분을 넣어주면 되요. 

![](../.gitbook/assets/image%20%2889%29.png)

잘 사라진게 보입니다. 

#### filter.py - 3

#### 시작 날짜 ~ 끝 날짜 form 필터 기능 구현 

```text
import django_filters
from django_filters import DateFilter, CharFilter
from .models import *

class OrderFilter(django_filters.FilterSet):
	start_date = DateFilter(field_name="date_created", lookup_expr='gte')
	end_date = DateFilter(field_name="date_created", lookup_expr='lte')
	
	class Meta:
		model = Order
		fields = '__all__'
		exclude = ['customer', 'date_created']
```

5번째줄에서 7번째줄까지 소스코드를 작성해줄게요.   
start\_date, end\_date 변수를 DateFilter클래스를 상속받아서 각각 만들어 줄거에요. 그리고 DataFilter클래스의 매개변수로 filed\_name과 look\_expr 를 지정해줄게요.   
lookup\_expr로 지정할 수 있는 몇가지중에 4가지를 살펴볼게요.

* gte - greater than equal \(이상\)
* lte - lesser than equal\(이하\)
* gt - greater than\(초과\)
* lt - lesser than \(미만\)  그럼 다 만들어 보면 아래와 같이 잘 나오는게 보이네요.

![](../.gitbook/assets/image%20%2891%29.png)

그런데 상품의 특이사항이나 문자로 검색하는 것이 필요하겠조?  
Order class에 클래스변수\(테이블 컬럼 추가\)하고 filter모듈의 OrderFilter클래스의 클래스변수에 note 변수도 만들어 볼게요. 

#### Model 

12번째 줄에 note 클래스 변수를 만들어 주세요. CharField로 지정하고 max\_length는 넉넉하게 1000으로 지정할게요. 그리고 null 값을 허용하도록 할게요.   
  
모델 수정\(생성, 삭제, 수정\)시 꼭 필요한 사항이 있조?

```text
$ python manage.py makemigrations 
$ python manage.py migrate
```

{% tabs %}
{% tab title="models.py" %}
```text
class Order(models.Model):
	STATUS = (
			('Pending', 'Pending'),
			('Out for delivery', 'Out for delivery'),
			('Delivered', 'Delivered'),
			)

	customer = models.ForeignKey(Customer, null=True, on_delete= models.SET_NULL)
	product = models.ForeignKey(Product, null=True, on_delete= models.SET_NULL)
	date_created = models.DateTimeField(auto_now_add=True, null=True)
	status = models.CharField(max_length=200, null=True, choices=STATUS)
	note = models.CharField(max_length=1000, null=True)

	def __str__(self):
		return self.product.name
```
{% endtab %}
{% endtabs %}

#### template - customer.html 

{% tabs %}
{% tab title="Plain Text" %}
```text
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
					<td>{{order.category}}</td>
					<td>{{order.date_created}}</td>
					<td>{{order.status}}</td>
					<td><a class="btn btn-sm btn-info" href="{% url 'update_order' order.id %}">Update</a></td>

					<td><a class="btn btn-sm btn-danger" href="{% url 'delete_order' order.id %}">Delete</a></td>
				</tr>
				{% endfor %}

			</table>
		</div>
	</div>
</div>

{% endblock %}
```

category - &gt; note로 바꿔주기만 했어요. 
{% endtab %}
{% endtabs %}

![](../.gitbook/assets/image%20%2885%29.png)

#### filters.py 

```text
import django_filters
from django_filters import DateFilter, CharFilter
from .models import *

class OrderFilter(django_filters.FilterSet):
	start_date = DateFilter(field_name="date_created", lookup_expr='gte')
	end_date = DateFilter(field_name="date_created", lookup_expr='lte')
	note = CharFilter(field_name='note', lookup_expr='icontains')
	
	class Meta:
		model = Order
		fields = '__all__'
		exclude = ['customer', 'date_created']
```

8번째 줄을 살펴보면 note변수를 만들었어요. 그리고 2번째줄에 CharFilter 를 사용해서 필드 이름과 lookup\_expr을 작성했어요.   


그중 lookup\_expr의 값을 'icontain'으로 썻는데 의미는 ignoresensativity라는 의미로 말그대로 대소문자 구분을 무시한다는 말이에요.

그럼 문제 없이 아래처럼 잘 구현될거에요. 

> 참고로 미리 note에 대한 값은 admin 패널에서 입력하고나서 note form에 조회해야 값이 모두 나와요. 그렇지 않으면 모두 Null값이겠조?



![](../.gitbook/assets/image%20%2888%29.png)

