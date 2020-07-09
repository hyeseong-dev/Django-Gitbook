# Decorator

## 데커레이터\(Decorator\) <a id="&#xB370;&#xCEE4;&#xB808;&#xC774;&#xD130;decorator"></a>

### 사전 개념 <a id="&#xC0AC;&#xC804;-&#xAC1C;&#xB150;"></a>

데커레이터를 이해하기 위해서는 아래와 같은 3가지 개념이 사전에 필요하다.  
가장 간단하게 설명하면

1. [일급 객체](http://whatisthenext.tistory.com/111)

* 함수 내에 함수를 정의할 수 있다
* 함수를 인자로 전달할 수 있다.

1. [클로저](http://whatisthenext.tistory.com/112)

* 내부\(inner\) 함수가 외부\(outer\) 함수의 인자를 기억하고 있는 것

1. \*args\(위치 인자\)와 \*\*kwargs\(키워드 인자\)

* \*args\(위치인자\) : 인자가 순서가 있음

  ```text
  ## 위치 인자 def sum(*args):    sum = 0    for i in args:        sum += i    return sum print(sum(5,3,1,2)) ## 키워드 인자 def print_kwargs(**kwargs):print("키워드인자를 출력 : ", kwargs) # print_kwargs('아침' : '시리얼', '점심' : '소고기', '저녁' : '없음') print_kwargs(아침='샐러드', 점심='소고기', 저녁='없음')
  ```

* \*\*kwargs\(키워드인자\)

### Decorate 개념 <a id="decorate-&#xAC1C;&#xB150;"></a>

> decorator : 실내장식가

데커레이터\(Decorator\)는 하나의 함수를 취해서 또 다른 함수를 반환하는 함수이다.  
코드는 이 [블로그](http://schoolofweb.net/blog/posts/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%8D%B0%EC%BD%94%EB%A0%88%EC%9D%B4%ED%84%B0-decorator/)를 참조했다.

#### 데코레이터 예제1 : 수동으로 활용하기 <a id="&#xB370;&#xCF54;&#xB808;&#xC774;&#xD130;-&#xC608;&#xC81C;1-&#xC218;&#xB3D9;&#xC73C;&#xB85C;-&#xD65C;&#xC6A9;&#xD558;&#xAE30;"></a>

```text
# 데코레이터 예제  def decorator_function(original_function): #1, #4     def wrapper_function(): #5 #8         return original_function() #9     return wrapper_function #6  def display(): #2, #10     print("display 함수가 실행됐습니다") #11  decorated_display = decorator_function(display)  #3 display 함수를 젇날 decorated_display() #7 
```

위 코드는 `#`의 순서를 따른다.

1. `#3`: decorated\_display라는 변수는 decorated\_function 함수의 리턴값을 할당받는다.
2. `#6`: return값은 wrapper\_function이라는 함수다.
3. `#3`변수는 결국 함수를 실행시킬 수 있는 함수다.
4. `#7`: 변수가 함수니까 괄호를 붙여 실행시켜보자.
5. `#8`, `#9` : return 값으로 original\_function을 돌려준다.
   * 여기서 closure 함수의 개념이 쓰인다.
   * 바깥 함수\(decorator\_function\)에서 전달받은 `original_function`을 기억하고 있다.
6. `#10`, `#11` : 여기서 비로소 함수를 실행하고, print문을 실행한다.

```text
# 데코레이터 예제  def decorator_function(original_function):    def wrapper_function():        print("{} 함수가 호출되기 전입니다.".format(original_function.__name__))        return original_function()    return wrapper_function def display_1():    print("display_1 함수가 실행됐습니다.") def display_2():    print("display_2 함수가 실행됐습니다.") display_1 = decorator_function(display_1)display_2 = decorator_function(display_2) display_1()print("")display_2()
```

wrapper 함수를 통해서 간단하게 기능 추가를 할 수 있다.

#### 데코레이터 예제2 : `@` 심볼 활용하기 <a id="&#xB370;&#xCF54;&#xB808;&#xC774;&#xD130;-&#xC608;&#xC81C;2-&#xC2EC;&#xBCFC;-&#xD65C;&#xC6A9;&#xD558;&#xAE30;"></a>

```text
# @ 심볼 활용하기  def decorator_function(original_function):    def wrapper_function():        print("{} 함수가 호출되기 전입니다.".format(original_function.__name__))        return original_function()    return wrapper_function @decorator_functiondef display_1():    print("display_1 함수가 실행됐습니다.") @decorator_functiondef display_2():    print("display_2 함수가 실행됐습니다.") # display_1 = decorator_function(display_1) # display_2 = decorator_function(display_2)  display_1() # 변수에 함수형 리턴값 할당 없이 바로 함수 호출이 가능하다. display_2() # 변수에 함수형 리턴값 할당 없이 바로 함수 호출이 가능하다. 
```

#### 데코레이터 예제3 : 인자가 전달되는 함수는 어떻게 데코레이팅 할까? <a id="&#xB370;&#xCF54;&#xB808;&#xC774;&#xD130;-&#xC608;&#xC81C;3-&#xC778;&#xC790;&#xAC00;-&#xC804;&#xB2EC;&#xB418;&#xB294;-&#xD568;&#xC218;&#xB294;-&#xC5B4;&#xB5BB;&#xAC8C;-&#xB370;&#xCF54;&#xB808;&#xC774;&#xD305;-&#xD560;&#xAE4C;"></a>

**예제1 : 위치인자와 키워드인자 활용하기**

```text
def decorator_function(original_function):    def wrapper_function():        print("{} 함수가 호출되기 전입니다.".format(original_function.__name__))        return original_function()    return wrapper_function @decorator_functiondef display_1():    print("display_1 함수가 실행됐습니다.") @decorator_functiondef display_info(name, age): # 위 예제와 다르게 인자가 전달된다.     print("display_info( {}, {} ) 함수가 실행됐습니다.").format(name, age) # display_1 = decorator_function(display_1) # display_2 = decorator_function(display_2)  display_1()display_info('김아무개', 37)>>> 출력결과> TypeError: wrapper_function() takes 0 positional arguments but 2 were given 
```

display\_1 함수는 정상적으로 출력되지만, `display_info` 함수는 타입에러가 생겼다.  
wrapper 함수에 위치인자와 키워드인자를 넣어주면 해결된다.

```text
def decorator_function(original_function):    def wrapper_function(*args, **kwargs):        print("{} 함수가 호출되기 전입니다.".format(original_function.__name__))        return original_function(*args, **kwargs)    return wrapper_function
```

**예제2 : 위치인자와 키워드인자 활용하기**

다음 예제는 `Introducing Python`이라는 책에서 발췌한 예제이다.  
여기서는 위치인자와 키워드인자까지 활용했다.

```text
def document_it(func):    def new_function(*args, **kwargs):        print('Running function : ', func.__name__)        print('Positional arguments : ', args)        print('Keyword arugments :' , kwargs)        result = func(*args, **kwargs)        print('Result : ', result)        return result    return new_function def add_inits(a, b):    return a + b  def div_inits(a,b):    return a / b add = document_it(add_inits)div = document_it(div_inits) print(add(5,3))print("")print(div(5,3)) >>> 출력값> Running function :  add_inits> Positional arguments :  (5, 3)> Keyword arugments : {}> Result :  8> 8 > Running function :  div_inits> Positional arguments :  (5, 3)> Keyword arugments : {}> Result :  1.6666666666666667> 1.6666666666666667
```

#### 그래서, 데코레이터를 언제 쓰는데? <a id="&#xADF8;&#xB798;&#xC11C;-&#xB370;&#xCF54;&#xB808;&#xC774;&#xD130;&#xB97C;-&#xC5B8;&#xC81C;-&#xC4F0;&#xB294;&#xB370;"></a>

1. 로그를 남길 때
2. 유저의 로그인 상태를 확인하여 로그인 페이지로 리다이렉트\(redirect\)
3. 프로그램 성능을 위한 테스트

아직 제가 이정도를 논할 수준이 아니여서, 추후 포스팅하도록 하겠습니다.  
대신, 좀 더 좋은 블로그를 소개하도록 하겠습니다.

