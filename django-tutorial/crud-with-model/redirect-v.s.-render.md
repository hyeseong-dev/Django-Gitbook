# Redirect V.S. Render

**render**

```text
render(request, template_name, context=None, content_type=None, status=None, using=None)
```

`render` 는 다음과 같은 파라미터들을 가집니다. 이 중에서 `request` 와 `template_name` 은 필수적으로 필요합니다. request 는 위와 동일하게 써주게 되고, template\_name 은 불러오고 싶은 템플릿을 기재해줍니다. 쉽게 생각해서 화면에 html 파일을 띄운다고 생각하면 됩니다. 이 때 `context` 로 원하는 인자를, 즉 view 에서 사용하던 파이썬 변수를 html 템플릿으로 넘길 수 있습니다. context 는 딕셔너리형으로 사용하며 key 값이 탬플릿에서 사용할 변수이름, value 값이 파이썬 변수가 됩니다.

```text
# views.py

from django.shortcuts import render

def my_view(request):
    name = "minsung"
    return render(request, 'myapp/index.html', {
        'name': name,
    }
```

**redirect**

```text
redirect(to, permanent=False, *args, **kwargs)
```

`redirect` 는 다음과 같은 파라미터를 가집니다. `to` 에는 어느 URL 로 이동할지를 정하게 됩니다. 이 때 상대 URL, 절대 URL 모두 가능하며 `urls.py` 에 `name` 을 정의하고 이를 많이 사용합니다. 단지 URL로 이동하는 것이기 때문에 render 처럼 context 값을 넘기지는 못합니다.

```text
# views.py

from django.shortcuts import redirect

def my_view(request):
    ...
    return redirect('view-name')             # view_name 사용
    # return redirect('/some/url/')                  # 상대 경로 
      # return redirect('https://example.com/')# 절대 경로 
```

**render 와 redirect 구분**

두 함수를 헷갈려 혼동하는 경우가 많습니다. 특히 장고가 익숙하지 않을 때는 둘다 return 뒤에 위치하여 함수를 종료할 시 사용되니 그럴만 합니다. 생각 외로 둘의 차이는 명확합니다. `render` 는 템플릿을 불러오고, `redirect` 는 URL로 이동합니다. URL 로 이동한다는 건 그 URL 에 맞는 views 가 다시 실행될테고 여기서 render 를 할지 다시 redirect 할지 결정할 것 입니다. 이 점에 유의해서 사용하신다면 상황에 맞게 사용하실 수 있을 겁니다

