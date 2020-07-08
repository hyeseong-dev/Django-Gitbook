# Database Models

Django는 기본적으로 sqlite3를 사용해요. 하지만 추후 compatiability를 고려하여 post sql,  mysql등 설정을 바꾸어 사용하게 할 수도 있어요. 

{% code title="settings.py" %}
```text
...
...

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
...
...
...
```
{% endcode %}

### DB 생성 혹은 적용 

17개의 미적용된 migrations가 있다고 하는데요. DB의 구조적 변경 혹은 적용되지 않은 사항들을 말해주는거에요.   
  
적용할려면 아래 스샷의 화살표로 가면 해당 명령어를 입력하면 되요. 

```text
python manage.py migrate
```

![](../.gitbook/assets/image%20%2829%29.png)

적용된 모델들은 관리자 웹페이지의 user테이블과  group 테이블을 표현한거에요.  
관리자 웹 페이지에 접속하기 위해 관리자 ID를 만들어 볼게요. 

### 관리자 ID 생성 

```text
$ python manage.py createsuperuser
$ 아이디 입력 : 
$ 이메일 입력 : 
$ 비밀번호 입력 : 
$ 비밀번호 재입력 : 

```

차근 차근 다 입력하고 서버를 실행해 볼게요.   
웹 브라우저 창에 127.0.0.1:8000/admin 페이지로 접속해주면 아래와 같은 화면이 출력되요. 

![](../.gitbook/assets/image%20%2857%29.png)

## models.py 

파이썬의 클래스를 사용하여 SQL문을 만들어주는 밑그림 혹은 사전 밑 작업을 이 파일에서 해주게 되요. 

![](../.gitbook/assets/image%20%2841%29.png)

위 그림을 보게되면 좌측의 models.py 에서 만든 클래스가 우측의 DB 테이블로 어떻게 만들어 질수 있는지 구조적 모형을 보여주고 있어요.   
이를 ORM\(Object Relational Management\)라고 해요. 

{% code title="accounts/models.py " %}
```text
from django.db import models

class Customer(models.Model):
       name = models.CharField(max_length=200, null=True)
       phone= models.CharField(max_length=200, null=True)
       email= models.CharField(max_length=200, null=True)
       date_created= models.DateTimeField(auto_now_add=True, null=True)
```
{% endcode %}

CharField\(\)을 사용하게 되면 필수적으로 작성해야 하는 옵션이 max\_length에요.   
그리고 DateTimeFiled는 시간을 나타내는데요. auto\_now\_add 옵션의 경우 생성된 record의 시간을 즉각적으로 표현해줘요. 

#### migration 하기 

migrate 명령을 적용하기전 makemigrations 명령어를 적용해볼게요. 

![](../.gitbook/assets/image%20%2875%29.png)

makemigrations 명령어를 적용하면 아래와 같이 migrations 폴더가 생성되고 아래에 00001\_initial.py 파일이 생성되고 sql문이 생성되는걸 볼수 있어요.   
  
참고로 name, phone, email, date\_created 는 각각 설정했는데 id는 우리가 설정하지 않아도 자동으로 django가 생성해준것이 보이네요. 

![](../.gitbook/assets/image%20%285%29.png)

그럼 SQL문을 만들었으면 이번에는 DB에 apply해 줄게요.   
그리고 관리자 웹 페이지에서 적용되었는지 확인할텐데 그 전에 accounts앱 안에 admin.py 파일이 보일거에요.   
  
즉, 여기서 관리자 페이지로 등록해주는 역할을 하게되요. 

```text
$ pyhon manage.py migrate
```

{% code title="accounts/admin.py " %}
```text
from django.contrib import admin 
from .models import Customer 

admin.site.register(Customer)
```
{% endcode %}

위 2번째 4번째 소스코드를 작성해주세요.  그럼 아래와 같이 출력될 거에요. 

![](../.gitbook/assets/image%20%2869%29.png)

Add 버튼을 클릭해서 우리가 만들었던 사용자 정보 등록도 가능해요.   
막상 사용자 등록을 해보니 문제가 있네요. 아래와 같이 사용자 이름이 바로 나오지 않네요. 

![](../.gitbook/assets/image%20%2810%29.png)

이를 해결 하기 위해서 class내에 \_\_str\_\_ 메서드를 작성하도록 할게요. 

{% code title="models.py " %}
```text
from django.db import models

class Customer(models.Model):
       name = models.CharField(max_length=200, null=True)
       phone= models.CharField(max_length=200, null=True)
       email= models.CharField(max_length=200, null=True)
       date_created= models.DateTimeField(auto_now_add=True, null=True)
       
       def __str__(self):
       return self.name
```
{% endcode %}

![](../.gitbook/assets/image%20%2827%29.png)

## 추가 클래스 작성 

**Product 클래스\(DB 테이블에 해당\)**

price 클래스 변수는 FloatField로 지정한 점이 기존 Customer 클래스를 생성했을떄와 차이가 있네요. 

CATEGORY 튜플 변수를 만들었는데요. 아마 Dropdown 박스를 이용할 것으로 보여요.   
그래서 category라는 변수를 만들어 CharField안에 choices라는 옵션의 값으로 설정해 두는것이 보이네요. 

**Order 클래스**

위와 비슷한 방식으로 dropdown 기능을 구현하기 위한 변수 설정과 choices 옵션 설정이 보이네요. 

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
	date_created = models.DateTimeField(auto_now_add=True, null=True)
	status = models.CharField(max_length=200, null=True, choices=STATUS)


	def __str__(self):
		return self.product.name
```

#### makemigrations & migrate 하기 

```text
$ python manage.py makemigrations
$ python manage.py migrate
```

#### admin.py 파일 수정 

{% tabs %}
{% tab title="admin.py 전 " %}
{% code title="" %}
```text
from django.contrib import admin 
from .models import Customer 

admin.site.register(Customer)
```
{% endcode %}
{% endtab %}

{% tab title="admin.py 후 " %}
```
from django.contrib import admin 
from .models import *

admin.site.register(Customer)
admin.site.register(Product)
admin.site.register(Order)
```
{% endtab %}
{% endtabs %}

![](../.gitbook/assets/image%20%2870%29.png)

![](../.gitbook/assets/image%20%2832%29.png)

근데 잘 보면 클래스 이름은 분명 단수로 지었는데 브라우저에 출력되는 것은 복수조?   
그건 장고가 일부러 그렇게 해준거에요. 만약 이를 개발자가 원하는대로 수정하고 싶다면 아래 소스코드를 작성해주세요. 

그럼 실제 테이블 이름과 단복수 역시 원하는대로 적용되요.   
그리고 해당 클래스는 본인이 바꾸고 싶은 클래스의 내부에 작성하는 이중 클래스 구조라는 점 유의하세요. 

```text
  class Meta: 
    db_table = 'customer' 
    verbose_name = '사용자' 
    verbose_name_plural = '사용자'
```

