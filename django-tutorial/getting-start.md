# Getting Start

## 설치

```text
$ pip install django
```

### 설치 확인 

```text
$ django-admin 
```

![](../.gitbook/assets/image%20%2843%29.png)

## 프로젝트 시작 

```text
$ django-admin startproject 프로젝트명(예.crm1)
$ cd crm1 

```

### crm1 프로젝트 구조 

![](../.gitbook/assets/image%20%2860%29.png)

### Djnago runserver

```text
$ ./manage.py runserver
```

#### 서버 끄기 

```text
ctrl + c
```

## 앱생성 

```text
$ django-admin startapp 앱이릅(예, accounts)
```

### 프로젝트에 앱 추가 설정 

crm1\(프로젝트\) -&gt; crm1\(앱\) -&gt; settings.py -&gt; INSTALLED\_APPS 리스트 변수에 'accounts' 앱 이름 등록.

![](../.gitbook/assets/image%20%2839%29.png)

