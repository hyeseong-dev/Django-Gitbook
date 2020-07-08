# Static Files & Images

소스코드와 관련없는 정적파일들은 별도의 관리 시스템이 필요한데요.   
django에서는 이를 static 폴더안에 둬서 관리합니다.   
우선 이전 소스코드의 &lt;style&gt;&lt;/style&gt;태그를 잘라내어 main.css파일을 생성해서   
이를 복붙할게요. 

우선 이전 main.html의 style태그를 복사하고 기존 영역은 삭제할게요. 

{% tabs %}
{% tab title="전" %}
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
{% endtab %}

{% tab title="후" %}
```
<!DOCTYPE html>
<html>
<head>
	<title>CRM</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

# style태그 부분 사라

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
{% endtab %}
{% endtabs %}

## STATIC 폴더 생성

아래와 같이 디렉토리 구조와 파일을 생성해주세요. 

```text
- crm1
    - static
            - css
                    - main.css
            - js
            - images 
```

{% code title="" %}
```text
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
```
{% endcode %}



### Django의 static 폴더 관 

기본적으로 Django는 static 디렉토리를 인식하고 있어요.   
바로 settings.py 파일 젤 밑으로 스크롤을 내려보면 **STATIC\_URL = '/static/'** 이라는 변수와 값이 할당된 상태로 관리 되고 있어요.

#### 변수 추가 

STATICFILES\_DIR은 정적 파일의 위치를 알려주는거에요.   
매개변수 첫번째 BASE\_DIR은 settins.py 파일 제일 첫 부분에 이미 생성되어 있어요. 이를 활용한것이에요. 그리고 두번째 매개변수 'static'은 해당 디렉토리까지 찾아가는 역할을 말하는거에요. 즉 

이 static폴더를 구성해서 소스코드에 박아서 사용하기 위해서는 아래와 같이 경로 설정이 전제되어야해요. 

```text
 ...
 ...
 ....
 
 STATIC_URL = '/static/'

 STATICFILES_DIR = [
  os.path.join(BASE_DIR, 'static')
 ] 
```

## static 소스 코드 삽입 

첫 번쨰 줄에 **{% load static %}** 삽입해주세요. 이래야 django가 현 프로젝트 폴더에서 static 폴더를 해당 html과 연결시켜주게되요.   
  
그럼 기존 방식대로 html에 css를 입히려면 &lt;head&gt;태그 사이에 &lt;link&gt; 태그를 둬서 css를 입혔는데요. rel, type까지는 기존 방식대로 똑같이 적으면되요.   
  
하지만, href속성 값은 템플릿 문법으로 작성하도록 할거에요.   
**{% static '/css/main.css' %}** 라고 값을 입력해주세요. 

결국 첫 줄에 연결 받은 static 부분을 href 속성 값\(변수\)으로 경로를 지정하고 문자열로 css/main.css값을 써주면 마무리 되는거에요.  

{% tabs %}
{% tab title="templates/main.html" %}
```
{% load static %}

!DOCTYPE html>
<html>
<head>
	<title>CRM</title>
	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
	<link rel='stylesheet' type="text/css" href="{% static '/css/main.css' %}">


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

![](../.gitbook/assets/image%20%2866%29.png)
{% endtab %}
{% endtabs %}

## logo 삽입 

DENNIS IVY 유투브 채널에서 제공해준 로고 이미지를 활용해서 웹페이지를 꾸며보도록 할게요. 

![](../.gitbook/assets/logo.png)

#### 디렉토리 

이미지 디렉토리에 png 파일을 넣어주세요. 

```text
- static
        - ..
        - ...
        - images
                - logo.png 
                
```

### 

### navbar.html 삽입 

모든 html파일에 소스코드를 삽입하면 안되겠조? 한곳에다가 넣어두면 알아서 모든 html 파일에 상속 받도록 할건데요.   
즉 logo 파일을 심을 곳은 navbar.html이에요.  

  
그전에 Djnago가 image파일을 모아둔 폴더를 인식 해야하는데요. 

#### 이미지 폴더 경로 인식 

* settings.py 파일에 MEDIA\_URL 변수와 경로값 설정 

{% tabs %}
{% tab title="settings.py" %}
```text
...
...

MEDIA_URL = '/images'/

...

```
{% endtab %}
{% endtabs %}

위 와같이 변수와 값을 설정했으면 아래 navbar.html의 첫번째줄에 {% %} 기호로 load 변수를 작성하고 static 변수 역시 써주세요. 

&lt;img&gt; 태그 내의 src속성이 보일거에요. 쌍따옴표 안에 템플릿 문법을 작성해서 경로를 이어주고 파일을 불러올게요.   


{% tabs %}
{% tab title="templates/navbar.html" %}
```text
{% load static %}

<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
 <img src="{% static 'images/logo.png' %}">
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>
  <div class="collapse navbar-collapse" id="navbarNav">
    <ul class="navbar-nav">
      <li class="nav-item active">
        <a class="nav-link" href="{% url 'home' %}">Dashboard</a>
      </li>
      <li class="nav-item">
        <a class="nav-link" href="{% url 'products' %}">Products</a>
      </li>
    </ul>
  </div>
  <span id='hello-msg'>Hello, {{ request.user }} </span>
  <span><a class='hello-msg' href="{% url 'logout' %}">Logout</a> </span>
</nav>
```
{% endtab %}
{% endtabs %}

