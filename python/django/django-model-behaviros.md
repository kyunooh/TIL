# 장고 모델 행동(Django Model Behaviors) By Kevin Stone
본 글은 http://blog.kevinastone.com/django-model-behaviors.html 의 글을 번역했습니다.

간단하고 깔끔한 튜토리얼 정도가 아닌, 복잡성이 큰 장고 프로젝트에선 어떻게 모델들을 잘 관리하도록 구성할까요? 10여개에서 100여개의 모델들, 수많은 뷰와 템플릿, 그리고 테스트들에 대해서 얘기해보려 합니다.

## 구성모델 행위 (Compositional Model Behaviors)
구성모델 패턴은 각 기능별로 구성요소를 쪼개서 여러분이 모델의 복잡성을 관리 할 수 있게 해줍니다.
> 역자 주) 본문에서 상황에 맞게 behaviors(행동, 행위)를 번역하거나 영어 그대로 사용하였습니다.

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
이제 behaviors으로 추출했으니, 구현체에 상속시켜줌으로써 구현체가 완벽하게 동작할 수 있도록 해줘야 합니다.
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
우리 모두 체인 쿼리셋 메소드는 잘 알고 있습니다. 하지만 커스텀 매니저 메소드도 잘 알고 계신가요? Author(username1)과 Published(publish_date가 과거의 시간인) 포스트를 찾아봅시다.

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
만들었던 필터들을 체이닝 하고 싶으면 어떻게 하죠?
```python
>>> BlogPost.objects.authored_by('username1').published()
AttributeError: 'QuerySet' object has no attribute 'published'

>>> type(Blogpost.objects.authored_by('username1'))
<class 'django.db.models.query.QuerySet'>
```

#### 해결방법 > 커스텀 쿼리셋
[django-model-utils](https://github.com/carljm/django-model-utils "django-model-utils")의 PassthroughManager를 이용해서 커스텀 매니저의 메소드를 체이닝할 수 있습니다.
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

이제 여러개의 behaviors를 상속받아서 커스텀 메소드 체인이 가능합니다..

```python
>>> author_public_posts = BlogPost.objects.authored_by('username1').published()

>>> type(Blogpost.objects.authored_by('username1'))
<class 'example.queryset.BlogPostQuerySet'>
```

### 분리된 비즈니스 로직
다음 중 어떤게 읽기 쉽고 유지보수 하기에 쉬워 보이시나요?
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
URL 이름 공간은 앱 레벨 또는 인스턴스 레벨에서의 구분자를 제공한다. URL 이름공간은  필요 없어보이지만 이용하기 시작하면 왜 그동안 쓰지 않았을까 생각이 드는 것 중 하나다

### URL이를음  짧고, 명환하고, 반복되는 작업을 피해서 작성하는 방법
예제에서 tastings_detail이나 tastings_results처럼 모델이나 앱의 이름을 복사한 URL 이름들은 더 이상 볼 수 없다. 대신 detail이나 results 같은 좀더 명확한 이름이 보인다. 이는 장고에 처음 입문하는 개발자들이 앱을 좀 더 쉽게 이해하는 데 도움이 된다. 

### 서드 파티 라이브러리와 상호 운영성을 높이기 
URL 이름을 "(myapp)\_detail" 등의  방법으로 부를 때 생기는 또 다른 문제는 이  (myapp)부분이 서로 겹칠 때 벌어진다. 우리의 tastings 앱과 같은 경우에 문제가 되지는 않지만 제작자의 블로그나 주소록 애플리케이션의 경우 충분히 문제가 될 수 있다. 이런 굥우 URL 이름공간을 통해 이를 간단히 해결할 수 있다. 예제는 책을 참고하자

