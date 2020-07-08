# Additional - Dynamic URL's

추가로 설명을 짧게 이어 갈게요. 

{% tabs %}
{% tab title="urls.py" %}
```text
from django.urls import path
from . import views


urlpatterns = [

    path('', views.home, name="home"),
    path('products/', views.products, name='products'),
    path('customer/<str:pk_test>/', views.customer, name="customer"),

]
```
{% endtab %}
{% endtabs %}

path\(\) 메서드에 name이라는 매개변수를 두고 해당 페이지의 관련성 있는 약칭을 설정할게요. home, products, customers 이렇게 3개를 name의 value값으로 각각 설정해주면 되요.   
  
그런데 어디다 써먹을려고 이걸 짤까요?  
메인 페이지의 버튼 몇개를 빠뜨렸거든요. 그걸 만들어 볼게요. 

![](../../.gitbook/assets/image%20%2850%29.png)



#### dashboard.html 

&lt;td&gt;태그를 만들어서 href 속성의 값으로 아래 빨간줄처럼 작성하면 되요.   
그 부분을 풀어서 설명하면 url 이라는 부분은 app의 url 설정 부분으로가서  따옴표로 처리된 부분이 urls.py 파일에서 작성한 path\(\)내에 옵션 매개변수로 작성한 name의  값에 해당하는 부분이에요. 그리고 마지막 customer.id는 바로 위의 for문의 customer로 받은 값의 id값을 입력해 준거에요.   
  
이해하기 쉽게 보자면 http://127.0.0.1/customer/customer.id

![](../../.gitbook/assets/image%20%2813%29.png)

![](../../.gitbook/assets/image%20%2871%29.png)

![](../../.gitbook/assets/image%20%2853%29.png)

이 방식이 좋은 점은 바로 실제 하드코딩으로 작성된 url 부분의 변동에 따라 template의 href 속성값을 하나하나 다 바꿔 줄 필요가 없어요.   
만약 아래에 customer\_profile로 url이름을 변동해도 name이라는 옵션 매개변수를 넣어서 설정해주면 기존 customer이름을 그대로 받아서 작동하기에 문제가 없다는거에요.

![](../../.gitbook/assets/image%20%2826%29.png)

Dahsboard 화면에서 View 버튼을 클릭하면 아래처럼 화면이 잘 연결 되는걸 볼수 있어요. 

![](../../.gitbook/assets/image%20%2814%29.png)

### 네비게이션 바 작성 

아래 Dashboard, Products 버튼 클릭하면 해당 화면으로 넘어 가게 template 문법으로 작성할게요. 바로 위에서 작성한것과 같아요. 현재 해당 부분은 navbar.html로 가서 작성해줘야해요. 

![](../../.gitbook/assets/image%20%287%29.png)

![](../../.gitbook/assets/image%20%2828%29.png)

![](../../.gitbook/assets/image%20%2846%29.png)

잘 적용되는것이 위 스샷으로 학인되네요. 

### 버튼을 부트스트랩으로 스타일링하기

#### dashboard.html 

![](../../.gitbook/assets/image%20%2822%29.png)

위 부분의 class 속성을 이렇게 설정할게요. 

#### dashboard.html

class 속성에 info, danger라고 글자를 입히면 각각 파란색과 빨간색이 나오조?   
이 부분은 부트스트랩에서 정한 약속이에요. 

![](../../.gitbook/assets/image%20%2817%29.png)

![](../../.gitbook/assets/image%20%2823%29.png)

위와 같이 잘 출력되면 되요!

