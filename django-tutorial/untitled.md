# URLs & Views

![](../.gitbook/assets/image%20%289%29.png)

## URL과 VIEW란?

1\) 사용자는 브라우저 주소창에 about이라는 문자를 작성

2\) url 맵핑에 따라 view에서 함수를 호출

3\) return 키워드를 통해서 json, httpresponse, html 랜더링등을 return.



## 실습 

crm1디렉토리 안의 urls.py파일을 작성해볼게요. 

```text
from django.contrib import admin 
from django.urls import path

from django.htttp import HttpResponse

def home(request):
    return HttpResponse('Home page')
    
def contact(request):
    return HttpResponse('Contact page')
    
urlpatterns = [
    path('admin/', admin.site.urls), 
    path('', home),
    path('about/', contact),
]
```

위 소스 코드를 설명하면 우선 urls.py 파일 안에 로직과 urlpatterns 변수를 작성한건데요.   
처음 로직을 먼저 작성해요. home, contact 메서드를요. 반드시 request 매개변수를 넣어주고 return할떄는 HttpResponse라는 클래스를 작성해서 돌려줘야해요. 그럼 이 메서드를 2개 만들었는데 어떻게 실행될까요?   
  
1\) 처음 사용자가 웹브라우저에서 웹주소를 입력해요.   
127.0.0.1:8000/ &lt;-- 그럼 14번째줄에서 첫번째 매개변수 빈 값이  두번째 매개변수인 home이라는 메서드를 실행시키게되요.   
그럼 이미 생성된 home이라는 메서드를 호출하고 'Home page'라는 두 글자를 돌려주게되요.   
동일하게 contact 함수도 이렇게 호출되고 그 값을 돌려주게되는 거고요.   


> 시험 삼아 view 로직을 urls.py에 작성했는데 여기에 실제로 작성해서 실무에 쓰는게 아니에요.   
> 작성한 소스코드는 지워주세요.

### 본격적으로 앱에 소스코드 작성 

1\) accounts앱에 urls.py 파일을 작성

6번쨰 소스코드는 처음 127.0.0.1:8000 웹 주소로 접속하게 되면 호출되게 되는 함수에요.   
그 함수의 이름을 home이라고 지정했고요. 

9번쨰, 12번째의 경우에는   
url 페이지마다 접속할시 호출될 함수를 작성한거에요.

{% code title="accounts/urls.py" %}
```text
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def home(request):
	return HttpResponse('home')

def products(request):
	return HttpResponse('products')
	
def customer(request):
	return HttpResponse('customer'
```
{% endcode %}

### 프로젝트의 url과 앱의 url 연동 

2번째 줄에 include 메서드를 추가할게요.   
그리고 urlpatterns 변수안에 path\( '', include\(accounts.urls'\)\)를 작성할게요. 

  
설혹 착각하는 경우가 있는데, 6번째 줄은 127.0.0.1:8000/accounts/를 의미하는게 아니에요.   
accounts앱의 urls.py파일을 포함한다는 의미에요.   
  
결론은 127.0.0.1/products 입력하면 accounts앱의 urls.py로 가서 -&gt; views.py의 products 함수를 호출하게되요.   
이 방식이 home, customer 함수에 동일하게 적용되는 구조에요. 

{% code title="crm1/crm1/urls.py" %}
```text
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('accounts.urls'))

]
```
{% endcode %}

