# Database Relationships

## DB 일대일 일대다 다대다 관계 

1명의 고객이 많은 주문을 할 수 있는 이 모습을 1:다 관계라고 정의해요.   
  
아래 스크린샷의 우측을 보면 customer 테이블과 order테이블에 대한 정의를 보면 좀더 쉽게 파악 할 수 있어요.   
고객 ID 1번 - John에 ORDERS 테이블에 주문된 제품이 Basketball, BBQ Grill이 매칭된걸 볼 수 있어요. 

![](../.gitbook/assets/image%20%2831%29.png)



### 제약 사항 

아래 제품 Ball이라는 제품에 태그를 달려고 하는데요. 그럼 Sports 태그를 달수는 있지만 Summer라는 태그를 달지 못하게 제한 되는 속성이 있어요.   
그것이 **1 : 다** 의 특징이기도 해요. 

#### 다 : 다 관계 

우측 부분을 보면 ball에 sports태그도 달수 있고 summer태그도 연결 가능한 구도가 바로 다 : 다 관계 구도에요.   


![](../.gitbook/assets/image%20%2855%29.png)



### 다 : 다 관계 

![](../.gitbook/assets/image%20%2818%29.png)

우측 부분을 좀더 보면, Tags라는 테이블과 Intermediary Table, Product라는 테이블이 존재해요.   


중간에 Intermediary Table 테이블은 Product와 Tag 테이블이 조인해서 해당 ID들만 별도로 뽑아 Join한 테이블이란걸 알 수 있어요.   
  
즉, 이런 테이블의 역학적인 관계를 구조해서 데이터간 서로 맵핑하여 사용할수 있게되는거에요.   


DB의 개론적 설명은 이제 이쯤하고 본격적으로 소스코드 작성을 이어갈게요. 

#### models.py 

지난 시간 모델 설계를 더 이어 나가도록 할게요.   
Order클래스에 customer, product 클래스 변수를 생성하도록 할게요.   
그리고 ForeignKey\(\)를 각각 잡아줄게요. 그리고 첫번째 매개변수로는 상속 받을 부모 클래스 이름을 넣어주세요.  null = True를 작성하고 on\_delete = models.SET\_NULL로 설정해서 해당 부모클래스에 있는 데이터가 사라지면 이를 NULL로 설정해준다는 의미에요. 

```text
from django.db import models


class Customer(models.Model):
	name = models.CharField(max_length=200, null=True)
	phone = models.CharField(max_length=200, null=True)
	email = models.CharField(max_length=200, null=True)
	date_created = models.DateTimeField(auto_now_add=True, null=True)

	def __str__(self):
		return self.name

class Product(models.Model):

	name = models.CharField(max_length=200, null=True)
	price = models.FloatField(null=True)
	category = models.CharField(max_length=200, null=True)
	description = models.CharField(max_length=200, null=True)
	date_created = models.DateTimeField(auto_now_add=True, null=True)

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


	def __str__(self):
		return self.product.name
```

모델 수정을 했으니 makemigrations와 migrate 명령어를 사용하여 적용할게요.   
  
그리고 처음 얘기했던 Tag에 대해 추가 하도록 할게요. 

#### Tag 테이블 생성 

```text
class Tag(models.Model):
	name = models.CharField(max_length=200, null=True)

	def __str__(self):
		return self.name
```

#### Many to Many 관계 정의 

```text
...
...
...

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

	def __str__(self):
		return self.name
		
...
...
...

```

Product 클래스 내부에 tags 클래스 변수를 생성하고 ManyToManyField\(\)를 작성하고 Tag 클래스를 상속 받도록 할게요.   
그리고 14번째줄의 옵션중에 blank가 보이는데요. 말 그대로 텍스트를 입력하지 않아도 정상적으로 출력 되도록 설정하는거에요. 

#### 

#### admin.py 

관리자 페이지에 생성된 Tag테이블을 반영해보도록 할게요. 

```text
from django.contrib import admin

# Register your models here.

from .models import *

admin.site.register(Customer)
admin.site.register(Product)
admin.site.register(Tag)
admin.site.register(Order)
```

![](../.gitbook/assets/image%20%2874%29.png)

product에 등록하게 될경우 이렇게 표현되게 되요.   
일대다 관계를 표시 했으니 Tag에는 여러개가 선택되어야겠조?   
 shift + 클릭을해서 여러개 선택을 한다음 save할 수 있으니 적용 해 볼게요. 

