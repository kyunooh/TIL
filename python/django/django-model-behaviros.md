# 장고 모델 행동(Django Model Behaviors) By Kevin Stone
본 글은 http://blog.kevinastone.com/django-model-behaviors.html 의 글을 번역한 것입니다.

간단하고 깔끔한 튜토리얼 정도가 아닌, 복잡성이 큰 장고 프로젝트에선 어떻게 모델들을 잘 관리하도록 구성할까요? 10여개에서 100여개의 모델들, 수많은 뷰와 템플릿, 그리고 테스트들에 대해서 이야기 해봅시다.

> 역자주) 본문에서 상황에 맞게 behaviors(행동, 행위)를 번역하거나 영어 그대로 사용하였습니다.

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

### 각각의 행위들을 분리한다
Behaviors 패턴의 목적은 핵심에 있는 모델들을 재사용 가능한 mixin으로 분리합니다. 모델 필드보단, 의도된 비즈니스 로직을 캡슐화할 더 높은 수준의 추상화를 만듭니다.
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

## 필드(field, 멤버변수)보다 많은 모델들
우리가 처음으로 잘라낸 공통된 behaviors는 공통 필드 뿐이었습니다. 하지만 필드외에 다른 것들은 어떻게 추상화 할까요?
 - 프로퍼티
 - 커스텀 메소드
 - 메소드 오버로드 (save() 등)
 - Validation
 - 쿼리셋 (QuerySets)

### 모델 메소드 확인
이제 비즈니스 로직들이 캡슐화 된 거대모델(fat model)도 살펴봅시다.
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
사실, 같은 기능을 하는 메소드들은 일반화시켜서 behaviors 모델안으로 추출이 가능합니다.
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

### 구현체와 연결하기
이제 behaviors으로 추출했으니, 구현체에 상속시켜줌으로써 구현체가 완벽하게 동작할 수 있도록 해줍시다.
```python
from .behaviors import Authorable, Permalinkable, Timestampable, Publishable


class BlogPost(Authorable, Permalinkable, Timestampable, Publishable, models.Model):
    title = models.CharField(max_length=255)
    body = models.TextField()

    url_name = "blog-post"

    @property
    def slug_source(self):
        return self.title
```

## 이름짓는 방법
동사 + able 형태의 패턴을 사용하세요. 접미사로 able을 사용한다면 behaviors 임을 알아차릴 수 있습니다. 이 방법은 현재 이미 사용하고 있는 단어들과 섞이는 것도 막을 수 있습니다.(OptionallyGenericRelateable같은 일반적이지 않은 영어를 쓰면 어떡하지에 대한 걱정은 하지마세요.)

## 커스텀 쿼리셋 체이닝(Custom QuerySet Chaining)
우리 모두 체인 쿼리셋 메소드는 잘 알고 있습니다. 하지만 커스텀 매니저 메소드도 잘 알고 계신가요? Author(username1)와 Published(publish_date가 과거의 시간인) 포스트를 찾아봅시다.

### 캡슐화가 없는 쿼리셋
```python
from django.utils import timezone
from .models import BlogPost

>>> BlogPost.objects.filter(author__username='username1') \
.filter(publish_date__lte=timezone.now())
```

### 커스텀 매니저
Author와 Published 필터 메소드를 커스텀 매니저에 만들어 봅시다.
```python
class BlogPostManager(models.Manager):

    def published(self):
        from django.utils import timezone
        return self.filter(publish_date__lte=timezone.now())

    def authored_by(self, author):
        return self.filter(author__username=author)


class BlogPost(models.Model):
    ...

    objects = BlogPostManager()
```

```python
>>> published_posts = BlogPost.objects.published()
>>> posts_by_author = BlockPost.objects.authored_by('username1')
```

### 커스텀 매니저의 필터는 어떻게 체이닝하죠?
만들었던 필터들을 체이닝 하고 싶으면 어떻게 할까요?
```python
>>> BlogPost.objects.authored_by('username1').published()
AttributeError: 'QuerySet' object has no attribute 'published'

>>> type(Blogpost.objects.authored_by('username1'))
<class 'django.db.models.query.QuerySet'>
```

#### 해결방법: 커스텀 쿼리셋
[django-model-utils](https://github.com/carljm/django-model-utils)의 PassthroughManager를 이용해서 커스텀 매니저의 메소드를 체이닝할 수 있습니다.
```python
from model_utils.managers import PassThroughManager

class PublishableQuerySet(models.query.QuerySet):
    def published(self):
        from django.utils import timezone
        return self.filter(publish_date__lte=timezone.now())


class AuthorableQuerySet(models.query.QuerySet):
    def authored_by(self, author):
        return self.filter(author__username=author)

class BlogPostQuerySet(AuthorableQuerySet, PublishableQuerySet):
    pass


class BlogPost(Authorable, Permalinkable, Timestampable, Publishable, models.Model):
    ...

    objects = PassThroughManager.for_queryset_class(BlogPostQuerySet)()
```

이제 여러개의 behaviors를 상속받아서 커스텀 메소드 체인이 가능합니다.

```python
>>> author_public_posts = BlogPost.objects.authored_by('username1').published()

>>> type(Blogpost.objects.authored_by('username1'))
<class 'example.queryset.BlogPostQuerySet'>
```

### 분리된 비즈니스 로직
다음 중 어떤게 읽기 쉽고 유지보수 하기도 쉬워 보이시나요?
```python
BlogPost.objects.filter(author__username='username1').filter(publish_date__lte=timezone.now())
```
```python
BlogPost.objects.authored_by('username1').published()
```

## Behaviors를 테스트하기
모델에 적합한 Behaivors 테스트를 만들어봅시다.

#### 모델에 적용했을 때와 동일한 장점들
- 중복 제거(DRY)
- 가독성(Readability)
- 재사용성(Reusability)
- 단일 책임 원칙(Single Responsibility Principle)

### 유닛테스트 예제
우리는 behaviors를 검증할 재사용 가능한 테스트 컴포넌트들을 만들 수 있습니다. 테스트 믹스인 목록은 각 모델이 맡은 역할에 대한 문서가 됩니다.

### 기존의 테스트
```python
from django.test import TestCase

from .models import BlogPost


class BlogPostTestCase(TestCase):
    def test_published_blogpost(self):
        from django.utils import timezone
        blogpost = BlogPost.objects.create(publish_date=timezone.now())
        self.assertTrue(blogpost.is_published)
        self.assertIn(blogpost, BlogPost.objects.published())
```

### Behavior 테스트 믹스인으로 변환
```python
class BehaviorTestCaseMixin(object):
    def get_model(self):
            return getattr(self, 'model')

    def create_instance(self, **kwargs):
        raise NotImplementedError("Implement me")


class PublishableTests(BehaviorTestCaseMixin):
    def test_published_blogpost(self):
        from django.utils import timezone
        obj = self.create_instance(publish_date=timezone.now())
        self.assertTrue(obj.is_published)
        self.assertIn(obj, self.model.objects.published())
```

### 변경된 유닛 테스트
```python
from django.test import TestCase

from .models import BlogPost
from .behaviors.tests import PublishableTests


class BlogPostTestCase(PublishableTests, TestCase):
    model = BlogPost

    def create_instance(self, **kwargs):
        return BlogPost.objects.create(**kwargs)
```

### 모델을 명시된 테스트들 조합함
```python
class BlogPostTestCase(PublishableTests, AuthorableTests, PermalinkableTests, TimestampableTests, TestCase):
    model = BlogPost

    def create_instance(self, **kwargs):
        return BlogPost.objects.create(**kwargs)

    def test_blog_specific_functionality(self):
        ...
```

## 추가적인 모델 테스팅 팁
- 인스턴스나 픽스쳐를 테스트 하기위해 [https://github.com/dnerdy/factory_boy](https://github.com/dnerdy/factory_boy)를 사용하세요.
- 테스트 케이스 상속을 사용해서 다른 시나리오를 검증하세요.
```python

class StaffBlogPostTestCase(PublishableTests, AuthorableTests, PermalinkableTests, TimestampableTests, BaseBlogPostTestCase):
    def setUp(self):
        self.user = StaffUser()

class AuthorizedUserBlogPostTestCase(PublishableTests, AuthorableTests, PermalinkableTests, TimestampableTests, BaseBlogPostTestCase):
    def setUp(self):
        self.user = AuthorizedUser()
```
(Staff이든 Authorized User이든 예상되는 행동이 같을 때)

## 재사용성
## 결국엔 Behavrois 라이브러리를 구축하게 됨
- Permalinkable
- Publishable
- Authorable
- Timestampable

## 앱들 간 재사용 가능하며 커뮤니티를 통해서도 공유가 가능
더 많은 예시
- Moderatable - BooleanField('approved')
- Scheduleable - (range 쿼리를 사용할 start_date 와 end_date)
- GenericRelatable (실과 바늘 같은 content_type, object_id, GenericForeignKey)
- Orderable - PositiveSmallIntegerField('position')
> 역자주) 본문에서는 triplet(세쌍둥이)를 사용하여서 의역했습니다. GenericRelatable (the triplet of content_type, object_id and GenericForeignKey) 

## 추천하는 앱 레이아웃
- querysets.py
- behaviors.py (querysets을 이용)
- models.py (querysets과 behaviors로 구성됨)
- factories.py (models을 이용함)
- tests.py (모든 것을 이용, 큰 앱들을 위해 모듈로 나눔)

전 공유되는 behaviors, model, behavior test 믹스인을 `common` 앱에 담아서 사용할 때가 많습니다.

## 한계와 위험
기본적으로 장고 모델 상속에 대해 도전

### 얕은 추상화
- 메타 옵션들을 명시적이지 않게 상속하지 마세요(정렬 등)
- Manager vs Queryset vs Model (약간의 로직 중복)
- ModelField 옵션들 (default=True vs default=False 을 오가면서 변경)
종종 커스텀 쿼리셋을 합치거나, 메타 옵션들을 조합하는 등 구성요소들을 다뤄야 합니다.

## 서드 파티 헬퍼
꼭 해야할 필요가 없다면 `바퀴의 재발명`을 하지 마세요!
> 역자주) 바퀴의 재발명이란, 프로그래밍에서 존재하는 기술이 있다면 사용하고 새로 만들지 말라는 격언입니다.

- [Django Extensions](https://github.com/django-extensions/django-extensions)(UUIDField, AutoSlugField 등)
- [Django Model Utils](https://github.com/carljm/django-model-utils)(이미 위에서 언급)
- Filters ([django-filter](https://github.com/carltongibson/django-filter))

### 테스터 헬퍼
- Factories ([factory boy](https://github.com/dnerdy/factory_boy)) Mocking ([mock](http://www.voidspace.org.uk/python/mock/))

## 결론
여기에 사용된 모든 예제 코드들은 [Github 프로젝트](https://github.com/kevinastone/django-model-behaviors-example)에서 확인 가능합니다.

#### 번역을 마치며
- 오역 또는 오탈자에 대해선 댓글 혹은 메일을 남겨주세요.
- 본 번역은 원저자의 허락을 받지는 못했습니다.(현재 연락을 시도해보고는 있습니다…)
- 원글에서도 임포트를 함수 내에서 하는 건 안티패턴이 아니냐는 댓글이 달려있습니다.. 저자의 정확한 의도는 잘 모르겠지만 개인적으론 일반적인 코딩 컨벤션을 준수하는 것이 좋다고 생각하는 바이며, 번역한 글에서는 모든 코드에 대해서 전혀 수정하지 않았습니다.
