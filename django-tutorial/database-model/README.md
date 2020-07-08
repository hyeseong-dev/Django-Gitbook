# Database Model Queries



![](../../.gitbook/assets/image%20%2854%29.png)

## Django Queries 

아래 예제를 보면서 연습 할게요.   
우선 prompt 창을 열고 아래 명령어를 입력하면 django shell모드로 진입하게 되요. 

```text
$ python manage.py shell 
```



![](../../.gitbook/assets/image%20%2862%29.png)

```text
#***(1)Returns all customers from customer table
customers = Customer.objects.all()

#(2)Returns first customer in table
firstCustomer = Customer.objects.first()

#(3)Returns last customer in table
lastCustomer = Customer.objects.last()

#(4)Returns single customer by name
customerByName = Customer.objects.get(name='Peter Piper')

#***(5)Returns single customer by name
customerById = Customer.objects.get(id=4)

#***(6)Returns all orders related to customer (firstCustomer variable set above)
firstCustomer.order_set.all()

#(7)***Returns orders customer name: (Query parent model values)
order = Order.objects.first() 
parentName = order.customer.name

#(8)***Returns products from products table with value of "Out Door" in category attribute
products = Product.objects.filter(category="Out Door")

#(9)***Order/Sort Objects by id
leastToGreatest = Product.objects.all().order_by('id') 
greatestToLeast = Product.objects.all().order_by('-id') 


#(10) Returns all products with tag of "Sports": (Query Many to Many Fields)
productsFiltered = Product.objects.filter(tags__name="Sports")

'''
(11)Bonus
Q: If the customer has more than 1 ball, how would you reflect it in the database?
  
A: Because there are many different products and this value changes constantly you would most 
likly not want to store the value in the database but rather just make this a function we can run
each time we load the customers profile
'''

#Returns the total count for number of time a "Ball" was ordered by the first customer
ballOrders = firstCustomer.order_set.filter(product__name="Ball").count()

#Returns total count for each product orderd
allOrders = {}

for order in firstCustomer.order_set.all():
	if order.product.name in allOrders:
		allOrders[order.product.name] += 1
	else:
		allOrders[order.product.name] = 1

#Returns: allOrders: {'Ball': 2, 'BBQ Grill': 1}


#RELATED SET EXAMPLE
class ParentModel(models.Model):
	name = models.CharField(max_length=200, null=True)

class ChildModel(models.Model):
	parent = models.ForeignKey(ParentModel)
	name = models.CharField(max_length=200, null=True)

parent = ParentModel.objects.first()
#Returns all child models related to parent
parent.childmodel_set.all()


```



shell창에 accounts앱의 models 모듈을 임포트 할거에요.   
첫 번째로 알아볼 ORM의 쿼리

 **Returns all customers from customer table\|**

**all\(\) 메서드는 모든 정보를 돌려줘요.** 

```text
>>> from accounts.models import *
>>> customers = Customer.objects.all()
>>> print(customers)
<QuerySet [<Customer: Peter Piper>, <Customer: John Doe>]>
>>>
```

QuerySet이라는 객체가 반환되고 Customer 테이블의 모든 내용이 보여지게 되요. 

## \(2\)Returns first customer in table

```text
>>> firstCustomer = Customer.objects.first()
>>> print(firstCustomer)
Peter Piper
```

first\(\) 메소드는 가장 천번째로 row값을 보여줘요.

## \(3\)Returns last customer in table

```text
>>> lastCustomer = Customer.objects.last()
>>> print(lastCustomer)
John Doe
```

가장 마지막으로 입력된 row == 가장 최근에 입력된 값을 보여줘요.   
그중에서도 한개만 보여주는거조.

## \(4\)Returns single customer by name

```text
>> customerByName = Customer.objects.get(name='Peter Piper')
>>> print(customerByName)
Peter Piper
```

get\(\) 메서드를 설정하면 얻는다는 뜻인데 무엇을 얻냐면 Customer테이블의 해당되는 컬럼값을 입력하고 그에 해당하는 값을 입력하면 되요.  
만약 name 매개변수에 매칭되는 키값이 존재 하지 않는 경우 아래와 같이 없다고 화면에 나타나요. 

```text
raise self.model.DoesNotExist(
accounts.models.Customer.DoesNotExist: Customer matching query does not exist.
```

customerByName이라는 객체에 점\(.\)찍고 email을 호출하면 peter의 이메일도 출력 할 수 있어요. \(아래와 같이\) 또한 자동으로 생성된 id값 역시 확인 가능해요. 

```text
>>> print(customerByName.email) 
pete#gmail.com

pete#gmail.com
>>> print(customerByName.id)
2
```

#### 단점 : Peter Piper라는 이름이 2개일 경우 문제가 발생하겠조? 

get\(\)메서드는 오직 한개의 값만 return해주기 때문이에요.  
이 경우 primary key\(unique한\)로 접근하는게 테이블내에서 동명이인 혹은 100명이 있더라도 고유한 값으로 접근하기에 오류가 발생하지 않아요. 

```text
>>> customer1 = Customer.objects.get(id=2)
>>> print(customer1)
Peter Piper
```

**ORM의 핵심 파트**

Peter Piper의 customer 테이블에는 name, phone, email, date\_created 이렇게 4개 컬럼만 존재해요. 실제 쇼핑몰에서는 해당 고객의 이름으로 주문한 것들이 서로 연결되어서 나와야 하는데 어떻게 해야할까요? 

바로 이미 Order 테이블에 ForeignKey로 설정된 컬럼을 이용하면되요. 

{% tabs %}
{% tab title="Customer 테이블" %}
```text
class Customer(models.Model):

	name = models.CharField(max_length=200, null=True)
	phone = models.CharField(max_length=200, null=True)
	email = models.CharField(max_length=200, null=True)
	date_created = models.DateTimeField(auto_now_add=True, null=True)

```
{% endtab %}

{% tab title="Order 테이블" %}
```
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
```
{% endtab %}
{% endtabs %}

```text
>>> orders = customer1.order_set.all()
>>> print(orders)
<QuerySet [<Order: Ball>, <Order: BBQ Grill>]>

```

customer1객체가 주문한 내용들이 잘 출력되네요.   


**여기서 중요한 문법!!**

> **생성객체.테이블명소문자\_set.all\(\)**

**이렇게 위와 같이 작성해주면 되요.**

\*\*\*\*

**반대로 order 테이블에 누가 1번째 주문을 했는지 알아 볼게요.** 

```text
>>> order = Order.objects.first()
>>> print(order.customer.name)
John Doe
```

다시 한번 풀어서 설명하면 Order 클래스의 첫번째 row data 객체를 order로 만들었어요. 그리고 print문으로 ForeignKey로 설정된 order객체는 customer클래스\(소문자\)로 접근할 수 있어요. 그리고 거기에 해당되는 name을 호출하면 첫번째 주문을 한 사람의 이름이 출력되요.  


## \(8\)Returns products from products table with value of "Out Door" in category attribute

filter\(\) 메소드를 이용해 볼게요. 

```text
>>> products = Product.objects.filter()
>>> print(products)
<QuerySet [<Product: BBQ Grill>, <Product: Dishes>, <Product: Ball>]>
```

일단 filter\(\) 메소드를 빈 매개변수로 실행하면 위와 같이 3개의 상품이 출력되요. 그럼 매개변수를 입력해 볼게요. 

```text
>>> products = Product.objects.filter(category='Out Door')
>>> print(products)
<QuerySet [<Product: BBQ Grill>, <Product: Ball>]>
```

out door 제품이 2개가 나오는걸 확인 할 수 있어요.   
참고로 get과는 차이가 분명히 보이는 걸 확인 할 수 있어요. get은 한개의 값만 출력하지만 fitler는 중복된 데이터 전체를 출력해서 나타내주는 넉넉함도 엿볼수 있네요.   
  
또한 filter\(category = "?", name = "?", price = ?, description, tags, date\_registered\) 해당 매개변수를 원하는 만큼 입력할 수도 있어요.   


## \(9\)\*\*\*Order/Sort Objects by id

내림차순 오름차순 정리. 기본적으로 내림차순 설정을 하지않으면 default값으로 출력되는 것은 오름차순이에요. 

```text
>>> leastToGreatest = Product.objects.all().order_by('id')
>>> print(leastToGreatest)
<QuerySet [<Product: BBQ Grill>, <Product: Dishes>, <Product: Ball>]> 

>>> greatestToLeast = Product.objects.all().order_by('-id')
>>> print(greatestToLeast)
<QuerySet [<Product: Ball>, <Product: Dishes>, <Product: BBQ Grill>]> 
```

order\_by\(\) 메서드를 이용하고 그 안에 id 매개변수를 입력하면 1,2,3,~~ 숫자가 증가하는 오름차순식으로 값이 나열되게 되요.   
  
그 반대로 내림차순으로 출력하고 싶은 경우 음수 부호인 " - " 를 입력하면 가장큰수 부터 예를 들면 1000, 999, 998, ..... 3, 2, 1 순으로 나오게 되요.   
  
물론 문자도 a부터 Z 식으로 설정되어 출력되는걸 확인 할 수 있어요. 

## \(10\) Returns all products with tag of "Sports": \(Query Many to Many Fields\)

다 : 다 관계 정의를 활용해 볼게요.   
우선 tag컬럼을 이용해볼건데요.   
어떻게 만들어 졌는지 모델에서 확인해 볼게요.   
아래 클래스변수에서 보는 것처럼 ManyToManyField로 만들어져있어요.

```text
class Product(models.Model):
	CATEGORY = (
			('Indoor', 'Indoor'),
			('Out Door', 'Out Door'),
			) 

	name = models.CharField(max_length=200, null=True)
	price = models.FloatField(null=True)
	category = models.CharField(max_length=200, null=True, choices=CATEGORY)
	description = models.CharField(max_length=200, null=True, blank=True)
	date_created = models.DateTimeField(auto_now_add=True, null=True)
	tags = models.ManyToManyField(Tag)
```

```text
>>> productsFiltered = Product.objects.filter(tags__name="Sports")
>>> print(productsFiltered)
<QuerySet [<Product: Ball>]>
```

위에 보게 되면 매개변수로 tags를 처음 입력하는데 이건 Product 클래스의 클래스변수이고 이후 언더바 2개\(\_\_\)가 오는데 이는 다:다 관계를 표현하는거에요. 그리고 Tag 클래스의 클래스 변수인 name값을 입력하고 등록된 값을 확인하기 위해 Spots 문자값을 입력하면 되요.   


## Returns the total count for number of time a "Ball" was ordered by the first customer

만약 첫번째 고객의 주문중에서 Ball 주문 개수를 파악하고 싶다면 어떻게 해야 할까요? 아래 ORM 쿼리문을 작성하면 되요. 

```text
>>> firstCustomer = Customer.objects.first()
>>> ballOrders = firstCustomer.order_set.filter(product__name="Ball").count()
```

```text
#Returns total count for each product orderd
allOrders = {}

for order in firstCustomer.order_set.all():
	if order.product.name in allOrders:
		allOrders[order.product.name] += 1
	else:
		allOrders[order.product.name] = 1

#Returns: allOrders: {'Ball': 2, 'BBQ Grill': 1}
```

