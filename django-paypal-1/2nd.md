# 2nd



앞선 강의 이어 갈게요. 

## 계정 생성 및 설

#### sandbox.paypal.com 사이트에 회원 가입후 로그인

아래로 스크롤을 내려보면 아래 sandbox Accounts에 기본 2가지 Account가 생성된걸 볼수 있어요.   
  
하지만 **Create Account** 버튼을 클릭한 후 **Create custom account** 링크 글을 클릭해서 계정 커스터마이징을 할게요.   
계정 2개를 커스터마이징으로 생성 할건데요. 우선 Business로 생성을 진행해볼게요.

![](../.gitbook/assets/image%20%28149%29.png)



#### Business Account 생성 

* Business 선택
* Email Address : 이메입 입력\(전 fake 이메일을 입력했어요.\)
* Password 입력
* First Name 입력
* Paypal balance $1,000

create Account 버튼 클릭 -&gt; 로그인 -&gt;

![](../.gitbook/assets/image%20%28153%29.png)



#### Personal Account 생성

* Personal 선택
* Email Address : 이메입 입력\(전 fake 이메일을 입력했어요.\)
* Password 입력
* First Name 입력
* Paypal balance $2,000

![](../.gitbook/assets/image%20%28156%29.png)

총 계정은 4개이지만, 우리가 방금 막 커스터마이징하여 만든 개인용, 비즈니스용 이렇게 2개가 따끈따근하게 만들어진겡 보이네요.

#### 비즈니스 계정 설정 

#### My App & Credentials -&gt; Sand Box-&gt; REST API apps -&gt; Create App 클

![](../.gitbook/assets/image%20%28141%29.png)

App Name을 입력하고 sandbox business account 이메일을 지하고 create app 버튼을 클릭할게요.

![](../.gitbook/assets/image%20%28138%29.png)

방금 지정한 앱 이름이 생성된 것 확인했으 바로 클릭해주세요.

![](../.gitbook/assets/image%20%28143%29.png)

우리가 실제 소스코드에 붙여넣어서 사용할 수 있는 Client ID를 확인 할 수 있어요.  
Client 쪽에서 필요한 전부이기도 하고요.  
  
**Client ID를 sb단어를 선택한 후 paste하기.**

**쉽게 추론가능한 부분이 바로 이 client id를 입력해서 paypal이 어느 계정을 이용할지 알게된다는 말이에요.**  


![](../.gitbook/assets/image%20%28144%29.png)

![](../.gitbook/assets/image%20%28147%29.png)

그럼 위 소스코드를 실제 우리가 만들 앱의 템플릿에 연결해보도록 할게요.  
우선 아래 압축 파일을 풀어서 소스코드를 활용하여 실습할게요. 

## PayPal 버튼 생성 및 연

{% file src="../.gitbook/assets/django-paypal.7z" %}

![](../.gitbook/assets/image%20%28157%29.png)



[https://developer.paypal.com/demo/checkout/\#/pattern/client](https://developer.paypal.com/demo/checkout/#/pattern/client)  
위 링크의 소스코드 12번째줄을 복사하여 simple-checkout.html 파일에 붙여 넣을게요.

![](../.gitbook/assets/image%20%28139%29.png)

![](../.gitbook/assets/image%20%28154%29.png)

sb라는 글자 부분을 블락 지정하여 이전에 비즈니스 계정 client ID 값을 입력하도록 할게요.   


![](../.gitbook/assets/image%20%28146%29.png)

![](../.gitbook/assets/image%20%28140%29.png)

나머지 script 태그도 붙여넣어줄게요.  
소스코드를 짧게 리뷰하면 16번쨰줄에 paypal.Button\({----------------}\).render\('\#paypal-button-container'\)소스코드가 버튼 실행을하는 로직이에요. render메서드를 통해서 10번째줄의 empty div 태그를 호출하여 실행하는거죠.  


![](../.gitbook/assets/image%20%28136%29.png)

실제 로컬 서버를 실행해보면 잘 작동하는걸 확인 할 수 있어요.  


## 결제 실습 

결제를 통해서 실제 우리가 만든 sandbox account에 어떤 변화가 있는지 알아볼게요.  
  
우선 personal 계정에 로그인하여 결제를 진행할게요.

![](../.gitbook/assets/image%20%28135%29.png)

![](../.gitbook/assets/image%20%28148%29.png)

결를 순조롭게 마치고 sandbox.paypal.com 홈페이지에서 잔액 확인을 해볼게요. 

![](../.gitbook/assets/image%20%28133%29.png)

원래 $2,000 -&gt; $1,999.99로 금액 변동이 난걸 확인 할 수 있어요.  
즉, personal 계좌에 있는 돈이 business 계좌로 이동했다는 단순한 의미에요.  


####  Flow Chart

 developer.paypal.com/docs/checkout/

아래 그림과 번호 1~7번처럼 순차적으로 작동하고 이동하게되요.



![How the PayPal Checkout integration works](https://developer.paypal.com/img/docs/checkout/v2/paypal-checkout-overview-pay-now-orders-api.svg)

1. You add the PayPal Smart Payment Buttons to your web page.
2. Your buyer clicks the button.
3. The button calls PayPal [Orders API](https://developer.paypal.com/docs/api/orders/v2/) to set up a transaction.
4. The button launches the PayPal Checkout experience.
5. The buyer approves the payment.
6. The button calls PayPal [Orders API](https://developer.paypal.com/docs/api/orders/v2/) to finalize the transaction.
7. You show a confirmation to your buyer.

 조금 더 부연 설명하면 2번에서 버튼을 클릭하게 되면 아래 createOrder 메서드가 실행되요. 그럼   
팝업창이 아래 블록으로 지정한 결과로서 나타나게되요.

![](../.gitbook/assets/image%20%28137%29.png)

그리고 아래 onApprove 메서드가 실행되어 결제승인이 이루어지게되요. 그리고 alert\(\)가 실행되어서 결제가 완료되었다는 알람 역시 나타나게 되요.

![](../.gitbook/assets/image%20%28158%29.png)

이제 버튼을 커스터마이징 해볼게요.

![](../.gitbook/assets/image%20%28142%29.png)

#### simple-chekout.html 페이지로 돌아와서 아래 빨갛게 줄친 부분을 입력해주세요. 

![](../.gitbook/assets/image%20%28134%29.png)

![](../.gitbook/assets/image%20%28145%29.png)

저장하고 로컬서버에서 확인하면 잘 적용된걸 확인 할 수 있어요.  
위의 label 키에 대응하는 여러 value값을 customizing 할 수 있어요. 

![](../.gitbook/assets/image%20%28155%29.png)

결과적으로 구현하려고 하는것은 실제 사용하는 버튼 2개만 나오도록 간추리도록 할게요. 

![](../.gitbook/assets/image%20%28151%29.png)

위 방법을 구현하기 위해서 script태그의 src 속성의 값을  수정하는 방법 있어요..

![](../.gitbook/assets/image%20%28150%29.png)

마지막 USD로 끝나는 부분에 USD&disable-funding=credit이라고 문자를 추가로 이어줄게요.  
  
여기까지 내용은 github 저장소에 저장했으니 참고하면 좋을듯해요.

[https://github.com/osori-magu/django\_paypal\_usage](https://github.com/osori-magu/django_paypal_usage)





