# 장고 모델 행동(Django Model Behaviors) By Kevin Stone
본 글은 http://blog.kevinastone.com/django-model-behaviors.html 의 글을 번역했습니다.

간단하고 깔끔한 튜토리얼 정도가 아닌, 복잡성이 큰 장고 프로젝트에선 어떻게 모델들을 잘 관리하도록 구성할까요? 10여개에서 100여개의 모델들, 수많은 뷰와 템플릿, 그리고 테스트들에 대해서 얘기해보려 합니다.

## 구성모델 행동 (Compositional Model Behaviors)
구성모델 패턴은 각 기능별로 구성요소를 쪼개서 여러분이 모델의 복잡성을 관리 할 수 있게 해줍니다.

### 거대 모델(Fat Models)의 장점
- 캡슐화(Encapsulation)
- 단일 경로(Single Path)
- 관심사 분리 (MVC)

### 유지보수 비용을 고려한다면
- 중복 제거(DRY)
- 가독성(Readability)
- 재사용성(Reusability)
- 단일 책임 원칙(Single Responsibility Principle)
- 테스트 용이성(Testability)

## 모델 행동 예제
### 기존의 모델
```python
class BlogPost(models.Model):
    title = models.CharField(max_length=255)
    body = models.TextField()
    slug = models.SlugField()
    author = models.ForeignKey(User, related_name='posts')
    create_date = models.DateTimeField(auto_now_add=True)
    modified_date = models.DateTimeField(auto_now=True)
    publish_date = models.DateTimeField(null=True)
```

### 각각의 행동들은 분해한다
행동 패턴의 목적은 핵심에 있는 모델들을 재사용 가능한 mixin으로 분리하는 겁니다. 모델 필드보단, 의도된 비즈니스 로직을 캡슐화할 더 높은 수준의 추상화를 만듭니다.
```python
from .behaviors import Authorable, Permalinkable, Timestampable, Publishable


class BlogPost(Authorable, Permalinkable, Timestampable, Publishable, models.Model):
    title = models.CharField(max_length=255)
    body = models.TextField()
```

### 재사용 가능한 행동
```python
class Authorable(models.Model):
    author = models.ForeignKey(User)

    class Meta:
        abstract = True


class Permalinkable(models.Model):
    slug = models.SlugField()

    class Meta:
        abstract = True


class Publishable(models.Model):
    publish_date = models.DateTimeField(null=True)

    class Meta:
        abstract = True


class Timestampable(models.Model):
    create_date = models.DateTimeField(auto_now_add=True)
    modified_date = models.DateTimeField(auto_now=True)

    class Meta:
        abstract = True
```

### 모델이 필드보다 많다
우리가 처음으로 잘라낸 공통된 행동은 공통된 필드 뿐이었습니다. 하지만 필드외에 다른 것들은 어떻게 추상화 할까요?
 - 프로퍼티
 - 

