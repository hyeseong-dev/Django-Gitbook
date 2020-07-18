# 1st

## 들어가기 앞서

**Django를 이용하여 결제 기능을 구현해 볼게요.  
2가지 책을 판매하고 결제 해볼게요 제가 가장 좋아하는 연글술사와 올리브나무책을 실습으로 진행해볼게요.**

![](.gitbook/assets/image%20%28129%29.png)

그리고 Buy Now 버튼을 클릭하면 아래와 같이 화면 이동을하게되요.  
그리고 Palyparl을 통해 결제 할수 있는 버튼으로 넘어가게 되요.  
Debit, Credit Card로도 결제 가능하도록 기능을 구현해볼게요.

![](.gitbook/assets/image%20%28128%29.png)

![](.gitbook/assets/image%20%28130%29.png)

## 본론 

### paypal developer api 

1\) OPEN API를 가져다 쓸거에요.  아래 링크를 클릭해서 바로 접근하시거나!  
[https://developer.paypal.com/demo/checkout/\#/pattern/client](https://developer.paypal.com/demo/checkout/#/pattern/client)

2\) 공식 사이트 -&gt; checkout -&gt; 좌특 메뉴에서 BASIC INTEGRATION -&gt;  [Test the button code in the interactive demo](https://developer.paypal.com/demo/checkout) 링크를 클릭해주면 되요.

클릭하면 소스코드가 우측에 있어요.   
즉, 끌어다 우리가 적용하고 싶은 template에 붙여너기만 하면 사용 할 수 있다는걸  어느정도 눈치 챘을거에요.

![](.gitbook/assets/image%20%28131%29.png)

 아래 소스코드를 좀더 풀어 볼게요.   
  
11번째줄에 empty div태그가 보이네요.  
  
14번째줄 script 태그가 있어요. src 속상 값 안의 "~~~client-id=sb ---"   
sb부분에 client id를 입력 가능하게 변경 해줄거에요.  
누가 언제든지 PayPal 계정에 돈을 보낼 수 있게 해주는 결재 프로세스의 초입부라 할 수 있겠조?    

16번째에 또 script 태그가 있는데 14번째 소스코드가 실행된 이후 18번쨰paypal.Buttons\(~~~~~\).render\(\('\#paypal-button-container'

\)하게되는데 결론은 11번째의 empty div를 호출하게 되요.

"

{% tabs %}
{% tab title="Plain Text" %}
```text
<!DOCTYPE html>

<head>
    <!-- Add meta tags for mobile and IE -->
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
</head>

<body>
    <!-- Set up a container element for the button -->
    <div id="paypal-button-container"></div>

    <!-- Include the PayPal JavaScript SDK -->
    <script src="https://www.paypal.com/sdk/js?client-id=sb&currency=USD"></script>

    <script>
        // Render the PayPal button into #paypal-button-container
        paypal.Buttons({

            // Set up the transaction
            createOrder: function(data, actions) {
                return actions.order.create({
                    purchase_units: [{
                        amount: {
                            value: '0.01'
                        }
                    }]
                });
            },

            // Finalize the transaction
            onApprove: function(data, actions) {
                return actions.order.capture().then(function(details) {
                    // Show a success message to the buyer
                    alert('Transaction completed by ' + details.payer.name.given_name + '!');
                });
            }


        }).render('#paypal-button-container');
    </script>
</body>
```
{% endtab %}
{% endtabs %}

![](.gitbook/assets/image%20%28132%29.png)

앞서 말한 empty div가 차지되는 부분이 바로 위의 paypal 버튼들이라는거에요.   
결국 위 부분이 우리가 커스터마이징 할 건데요.   
  
21, 32번째 각각 createOrder, onApprove 메서드가 각각 제목처럼 작용하게 될거에

21번째 createOrder안의 25번째 라인에서 value 값을 변경하면 결재하려는 제품의 가격이 변경되요.  
  
32번째 메서드 안의 33번째 소스코드의 actions.order.capture\(\) 이 부분이 실제로 비용 결제가 실재 이루어 지는 부분이에요.   
  
자! 이제 샌드박스 계정을 생성해 보도록 할게요.  

