# OneToOneField

### OneToOneField와 ForeignKey의 차이 <a id="onetoonefield&#xC640;-foreignkey&#xC758;-&#xCC28;&#xC774;"></a>

[스택오브플로우](https://stackoverflow.com/questions/5870537/whats-the-difference-between-django-onetoonefield-and-foreignkey)

OneToOneField는 ForeignKey\(model, unique=True\)와 개념적으로 같다. 둘의 차이점은 `역관계(reverse relationships)`에 있다. One-to-one 모델의 역참조는 **하나의 객체\(single object\)** 를 반환하지만, ForeignKey의 역참조는 **QuerySet** 을 반환한다.

#### 역관계\(reverse relationships\)란? <a id="&#xC5ED;&#xAD00;&#xACC4;reverse-relationships&#xB780;"></a>

먼저, `역관계(reverse relationships)`가 무엇인지 알아야 할 필요가 있다.

```text
from django.db import models  class Group(models.Model):    name = models.CharField(max_length=20)     def __str__(self):        return self.name  class Profile(models.Model):    content = models.CharField(max_length=20)    group = models.ForeignKey(Group)     def __str__(self):        return "구단소개 : {}".format(self.content)
```

위 코드를 SQLite3 DB로 살펴보면 아래와 같다.  
  
  
**class Group**

![&#xC2A4;&#xD06C;&#xB9B0;&#xC0F7;, 2017-06-09 02-10-29](http://i.imgur.com/rvy2F5Y.png)

  
  
**class Profile**  
![&#xC2A4;&#xD06C;&#xB9B0;&#xC0F7;, 2017-06-09 02-10-20](http://i.imgur.com/6Xe7MdJ.png)

여기서 주목해야할 것이 `group_id`인데,  `Profile`은 group\_id를 가지고 있기 때문에 바로 `Group`에 접근이 가능하다.

반대로, `Group`에서 `Profile`에 접근하려고 하면, `id`값이 없기 때문에 `_set`을 써줘야 해당 모델에 대한 접근\(access\), 편집\(edit\), 쿼리\(query\)가 가능하다.GroupProfile

```text
>>> g1 = Group.objects.get(id=1)>>> p1 = Profile.objects.get(id=1).content >>> g1<Group: 레알 마드리드> >>> p1<Profile: 최고 명문의 팀> 정방향참조>>> p1.group.name'레알 마드리드' 역방향 참조(잘못된 예)>>> g1.prifle.nameAttributeError : 'Group' object has no attribute 'profile' 역방향참조>>> g1.profile_set(id=g1.id).content # 역방향 참조는 _set으로 접근. (id값도 필요) `최고 명문의 팀`
```

지금까지 역방향 참조를 살펴보았다. 

요약하자면, ForeignKey가 없는 Profile은 자신의 id를 직접 들고 \_set으로 접근해야 한다는 것이다.

### OneToOneField와 ForeignKey는 어떻게 다를까? <a id="onetoonefield&#xC640;-foreignkey&#xB294;-&#xC5B4;&#xB5BB;&#xAC8C;-&#xB2E4;&#xB97C;&#xAE4C;"></a>

```text
class Engine(models.Model):    name = models.CharField(max_length=25)     def __unicode__(self):        return self.name class Car(models.Model): # OneToOneField     name = models.CharField(max_length=25)    engine = models.OneToOneField(Engine)     def __unicode__(self):        return self.name class Engine2(models.Model):    name = models.CharField(max_length=25)     def __unicode__(self):        return self.name class Car2(models.Model): # foreignKey     name = models.CharField(max_length=25)    engine = models.ForeignKey(Engine2, unique=True)     def __unicode__(self):        return self.name
```

**car와 e, car2, e2를 만들고 실험진행**

![&#xC2A4;&#xD06C;&#xB9B0;&#xC0F7;, 2017-06-09 15-23-22](http://i.imgur.com/oHYtsdi.png)

**출력결과**  
![&#xC2A4;&#xD06C;&#xB9B0;&#xC0F7;, 2017-06-09 15-25-58](http://i.imgur.com/0vNUpTX.png)  
OneToOneField로 걸어준 친구는 Car 오브젝트 **한 개** 를 준다. 또한 Engine은 역방향 관계에 있는 Car에 접근하기 위해서 `_set`을 사용할 필요가 없다. \(어짜피 하나만 반환되기 때문에\)

반대로 Engine2는 역방향 참조를 하기 위해서 `_set`을 이용해서 Car2에 접근해야 한다. 또한 `QuerySet`을 반환하는 것을 볼 수 있다.

### 언제 OneToOneField를 쓸까? <a id="&#xC5B8;&#xC81C;-onetoonefield&#xB97C;-&#xC4F8;&#xAE4C;"></a>

[스택오버플로](https://stackoverflow.com/questions/25206447/when-to-use-one-to-one-relationships-in-django-models)

예를 들어 1명의 유저는 하나의 프로필만을 가져야 한다고 강제한다면, one-to-one을 사용할 수 있다.  
첫번째 테이블이 `User`, 두번째 테이블은 `Profile`이다. 두번째 테이블은 반드시 첫번째 테이블과 한번만 매칭된다.

지금 당장 One-to-one 모델에 대해 이해하지 못했다고 해서 걱정할 필요 없다고 조언한다. 흔하게 찾아볼 수 있는 관계도 아니거니와 특정 문제에 맞닥 뜨리면 찾아보게 된다고 한다..

