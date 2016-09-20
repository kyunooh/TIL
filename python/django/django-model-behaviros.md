# 장고 모델 행동(Django Model Behaviors) By Kevin Stone
본 글은 http://blog.kevinastone.com/django-model-behaviors.html 의 글을 번역했습니다.

간단하고 깔끔한 튜토리얼 정도가 아닌, 복잡성이 큰 장고 프로젝트에선 어떻게 모델들을 잘 관리하도록 구성할까요? 10여개에서 100여개의 모델들, 수많은 뷰와 템플릿, 그리고 테스트들에 대해서 얘기해보려 합니다.

## 구성모델 행위 (Compositional Model Behaviors)
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

## 모델 behaviors 예제
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

### 각각의 행위들을 분해한다
Behaviors 패턴의 목적은 핵심에 있는 모델들을 재사용 가능한 mixin으로 분리하는 겁니다. 모델 필드보단, 의도된 비즈니스 로직을 캡슐화할 더 높은 수준의 추상화를 만듭니다.
```python
from .behaviors import Authorable, Permalinkable, Timestampable, Publishable


class BlogPost(Authorable, Permalinkable, Timestampable, Publishable, models.Model):
    title = models.CharField(max_length=255)
    body = models.TextField()
```

### 재사용 가능한 behaviors
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
    modified_date = models.DateTimeField(auto_now=True)ㅏ

    class Meta:
        abstract = True
```

## 필드(field)보다 많은 모델
우리가 처음으로 잘라낸 공통된 behaviors는 공통 필드 뿐이었습니다. 하지만 필드외에 다른 것들은 어떻게 추상화 할까요?
 - 프로퍼티
 - 커스텀 메소드
 - 메소드 오버로드 (save() 등)
 - Validation
 - 쿼리셋 (QuerySets)

### 모델 메소드 확인
이제 비즈니스 로직들이 캡슐화 된 거대모델도 살펴봅시다.
```python

class BlogPost(models.Model):
    ...

    @property
    def is_published(self):
        from django.utils import timezone
        return self.publish_date < timezone.now()

    @models.permalink
    def get_absolute_url(self):
        return ('blog-post', (), {
            "slug": self.slug,
        })

    def pre_save(self, instance, add):
        from django.utils.text import slugify
        if not instance.slug:
            instance.slug = slugify(self.title)

```

### 메소드가 있는 Behaviors
일반적으로 같은 기능을 하는 메소드들은 behaviors 모델안으로 추출이 가능합니다.
```python
class Permalinkable(models.Model):
    slug = models.SlugField()

    class Meta:
        abstract = True

    def get_url_kwargs(self, **kwargs):
        kwargs.update(getattr(self, 'url_kwargs', {}))
        return kwargs

    @models.permalink
    def get_absolute_url(self):
        url_kwargs = self.get_url_kwargs(slug=self.slug)
        return (self.url_name, (), url_kwargs)

    def pre_save(self, instance, add):
        from django.utils.text import slugify
        if not instance.slug:
            instance.slug = slugify(self.slug_source)


class Publishable(models.Model):
    publish_date = models.DateTimeField(null=True)

    class Meta:
        abstract = True

    objects = PassThroughManager.for_queryset_class(PublishableQuerySet)()

    def publish_on(self, date=None):
        from django.utils import timezone
        if not date:
            date = timezone.now()
        self.publish_date = date
        self.save()

    @property
    def is_published(self):
        from django.utils import timezone
        return self.publish_date < timezone.now()
```
