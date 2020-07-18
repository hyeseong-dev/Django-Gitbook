# 3rd

1st, 2nd에서는 simple-checkout.html에서 몸풀기로 다뤄봤습니다. 

이제는 아래 홈화면에서 본격적으로 다뤄볼건데요.

![](../.gitbook/assets/image%20%28149%29.png)

  
노라색 버튼을 클릭하면 현재는 아래와 같이 나오는데 가격 정보 밑에 paypal 버튼을 삽입하고 기능 구현까지 실행해보도록 할게요.  


![](../.gitbook/assets/image%20%28159%29.png)

작업할 파일은 store.html, checkout.html 파일 2개에요.

![](../.gitbook/assets/image%20%28146%29.png)

## View 

simpleChekout 메서드 이외에 다른 3개의 메서드를 이제 본격적으로 활용해 볼거에요.   
우선 12번째줄의 store메서드는 페이지 랜더링을 할 건데요.  
13번째줄에서 Product.objects.all\(\) 소스코드를 DB에서 쿼리하여 products 변수로 정의할거에요.  
  
그 다음 context dictionary안에 string 키값과 value값을 앞에서 정의한 products로 넣어 둘게요.

![](../.gitbook/assets/image%20%28135%29.png)

## Model 

2가지 모델 클래스가 정의되어 있어요.   
기본적으로 Product클래스에는 CharField, TextField, FloatField를 이용해서 기본적인 필드속성을 입력하고 이에 대응하는 변수를 생성했어요.

나머지 Order 클래스에는 product변수는 ForeignKey\(many to one relationship\)로 잡아서 Product클래스를 상속받아 생성하게되요.  
created 클래스 변수는 DateTimeField의 auto\_now\_add 필드 변수를 이용해서 생성하구 말이조.

![](../.gitbook/assets/image%20%28151%29.png)

위 모델로 만들어진 Base앱의 Order class와 Product 클래스는 admin 페이지에서 아래와 같이 확인가능해요.  


![](../.gitbook/assets/image%20%28150%29.png)

![](../.gitbook/assets/image%20%28157%29.png)

![](../.gitbook/assets/image%20%28143%29.png)

클래스 변수 역시 잘 생성된 모습을 볼 수 있어요.  
  
그리고 실제 Order class가 적용된 모습\(결제 완료\)도 확인할 수 있어요.  


![](../.gitbook/assets/image%20%28155%29.png)

![](../.gitbook/assets/image%20%28166%29.png)

앞서서 Model,  View를 살펴봤어요.  


## Template

24번째에 for문을 통해서 db에 저장된 product 정보를 loop하여 나타내주게되요.  
이름, 설명, 가격말이지요.  
그리고 Buy Now 버튼을 클릭하면 a태그의 href 속성 값인 "{% url 'checkout' product.id %}" 여기서 product.id는 View의 checkout메서드의 매개변수에 해당해요.  
  
또한 urls.py의 path\('checkout/&lt;int:pk&gt;/', views.checkout, name='checkout"\), 에서도 꺽쇠 괄호로 잘 연결된 걸 확인 할 수 있어요.  


{% tabs %}
{% tab title="store.html" %}
```text
<!DOCTYPE html>
<html>
<head>
	<title>Store</title>
	<meta id="meta" name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1.0; user-scalable=0;">

	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">


	<style type="text/css">
		body{
			background-color: #f0f0f0;
		}
		img{
			max-width: 228px;
			max-height: 346px;
			box-shadow: 0 2px 5px 1px rgba(0,0,0,.2);
		}
	</style>
</head>
<body>
	<div class="container">
		<div class="row">
			{% for product in products %}

				<div class="col-lg-6">
					<br>
					<div class="card card-body">
						<h4>{{product.name}}</h4>
						<img src={{product.image_url}}>
						<hr>
						<p>{{product.description}}</p>
						<hr>
						<h4>${{product.price}}</h4>
						<hr>
						<a target="_blank" class="btn btn-sm btn-warning" href="{% url 'checkout' product.id  %}">Buy Now</a>
					</div>
				</div>
			{% endfor %}
		</div>
	</div>
</body>
</html>
```
{% endtab %}
{% endtabs %}

![](../.gitbook/assets/image%20%28142%29.png)

주소창에서 checkout/2/로 표현된게 보이조. 저기서 2가 Product 테이블에 2번쨰 id로 등록된 제품을 말해요.  


#### checkout.html 

```text
<!DOCTYPE html>
<html>
<head>
	<title>Store</title>
	<meta id="meta" name="viewport" content="width=device-width; initial-scale=1.0; maximum-scale=1.0; user-scalable=0;">

	<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">


	<style type="text/css">
		body{
			background-color: #f0f0f0;
			
		}

	</style>
</head>
<body>
	<div class="container">
		<div class="row">
			<div class="col-lg">
				<br>
				<img src="{{product.image_url}}">
			</div>

			<div class="col-lg">
				<br>
				<div class="card card-body">
					<h3>{{product.name}}</h3>
					<hr>
					<h4>Total: ${{product.price}}</h4>
					<hr>

					<div id="paypal-button-container"></div>

				</div>
			</div>
		</div>
	</div>

	 <script src="https://www.paypal.com/sdk/js?client-id=sb&currency=USD"></script>

	 <script>

	 	function getCookie(name) {
		    var cookieValue = null;
		    if (document.cookie && document.cookie !== '') {
		        var cookies = document.cookie.split(';');
		        for (var i = 0; i < cookies.length; i++) {
		            var cookie = cookies[i].trim();
		            // Does this cookie string begin with the name we want?
		            if (cookie.substring(0, name.length + 1) === (name + '=')) {
		                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
		                break;
		            }
		        }
		    }
		    return cookieValue;
		}
		var csrftoken = getCookie('csrftoken');

	 	var total = '{{product.price}}'
	 	var productId = '{{product.id}}'

	 	function completeOrder(){
	 		var url = "{% url 'complete' %}"

	 		fetch(url, {
	 			method:'POST',
	 			headers:{
	 				'Content-type':'application/json',
	 				'X-CSRFToken':csrftoken,
	 			},
	 			body:JSON.stringify({'productId':productId})
	 		})
	 	}


        // Render the PayPal button into #paypal-button-container
        paypal.Buttons({

            // Set up the transaction
            createOrder: function(data, actions) {
                return actions.order.create({
                    purchase_units: [{ 
                        amount: {
                            value: total
                        }
                    }]
                });
            },

            // Finalize the transaction
            onApprove: function(data, actions) {
                return actions.order.capture().then(function(details) {
                    // Show a success message to the buyer
                    completeOrder()
                    alert('Transaction completed by ' + details.payer.name.given_name + '!');
                });
            }


        }).render('#paypal-button-container');
    </script>

</body>
</html>
```

####  23-34번째 소스코드를 위와 같이 정의할게요. 34번째 paypal-button-cotainer id값은 paypal 버튼을 껍데기를 만들어줘요.

