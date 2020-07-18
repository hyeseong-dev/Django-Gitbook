# 2nd



앞선 강의 이어 갈게요. 

#### sandbox.paypal.com 사이트에 회원 가입후 로그인

아래로 스크롤을 내려보면 아래 sandbox Accounts에 기본 2가지 Account가 생성된걸 볼수 있어요.   
  
하지만 **Create Account** 버튼을 클릭한 후 **Create custom account** 링크 글을 클릭해서 계정 커스터마이징을 할게요.   
계정 2개를 커스터마이징으로 생성 할건데요. 우선 Business로 생성을 진행해볼게요.

![](../.gitbook/assets/image%20%28138%29.png)



#### Business Account 생성 

* Business 선택
* Email Address : 이메입 입력\(전 fake 이메일을 입력했어요.\)
* Password 입력
* First Name 입력
* Paypal balance $1,000

create Account 버튼 클릭 -&gt; 로그인 -&gt;

![](../.gitbook/assets/image%20%28139%29.png)



#### Personal Account 생성

* Personal 선택
* Email Address : 이메입 입력\(전 fake 이메일을 입력했어요.\)
* Password 입력
* First Name 입력
* Paypal balance $2,000

![](../.gitbook/assets/image%20%28140%29.png)

총 계정은 4개이지만, 우리가 방금 막 커스터마이징하여 만든 개인용, 비즈니스용 이렇게 2개가 따끈따근하게 만들어진겡 보이네요.

#### 비즈니스 계정 설정 

#### My App & Credentials -&gt; Sand Box-&gt; REST API apps -&gt; Create App 클

![](../.gitbook/assets/image%20%28134%29.png)

App Name을 입력하고 sandbox business account 이메일을 지하고 create app 버튼을 클릭할게요.

![](../.gitbook/assets/image%20%28133%29.png)

방금 지정한 앱 이름이 생성된 것 확인했으 바로 클릭해주세요.

![](../.gitbook/assets/image%20%28135%29.png)

우리가 실제 소스코드에 붙여넣어서 사용할 수 있는 Client ID를 확인 할 수 있어요.  
Client 쪽에서 필요한 전부이기도 하고요.  
  
**Client ID를 sb단어를 선택한 후 paste하기.**

**쉽게 추론가능한 부분이 바로 이 client id를 입력해서 paypal이 어느 계정을 이용할지 알게된다는 말이에요.**  


![](../.gitbook/assets/image%20%28136%29.png)

![](../.gitbook/assets/image%20%28137%29.png)

그럼 위 소스코드를 실제 우리가 만들 앱의 템플릿에 연결해보도록 할게요.  
우선 아래 압축 파일을 풀어서 소스코드를 활용하여 실습할게요. 

{% file src="../.gitbook/assets/django-paypal.7z" %}

![](../.gitbook/assets/image%20%28141%29.png)

