# 쿼리와 데이터베이스 레이어
다른 ORM과 마찬가지로 장고는 여러 종류의 각기 다른 데이터를 데이터베이스 종류와는 독립적인 형태로 객체화한다. 대부분의 경우 원래 의도대로 역할을 잘하지만, 가끔씩 기이한 결과를 보여주기도 한다.

## 단일 객체에서 get_object_or_404() 이용하기
단일 객체를 가져와서 작업을 하는 세부 페이지 같은 뷰에서는 get() 대신에 get_object_or_404()를 이용하다록 한다.

주의사항 : get_object_or_404()는 뷰(view)에서만 사용하자!!

## 예외를 일으킬 수 있는 쿼리를 주의하자
단일 모델 인스턴스에서 get_object_or_404()를 이용할 때는 try-except 블록으로 예외 처리를 할 필요가 없다. 하지만 이를 제외한 경우는 예외처리를 해야한다.

### ObjectDoesNotExist와 DoesNotExist
ObjectDoesNotExist는 어떤 모델 객체에도 이용할 수 있지만 DoesNotExist는 특정 모델에서만 이용할 수 있다.

### 여러 개의 객체가 반환되었을 때
쿼리가 하나 이상의 객체를 반환할 수도 있다면 MultipleObjectsReturnend 예외를 참고하자. (책을 보자)

### 쿼리를 좀 더 명확하게 하기 위해 지연 연산 이용하기
장고는 데이터가 정말로 필요하기 전까지는 SQL을 호출하지 않는다. 따라서 ORM메서드와 함수를 얼마든지 원하는 만큼 연결해서 코드를 써내려 갈 수 있다. 결과를 실행하기 전까지 장고는 실제 데이터베이스에 연동되지 않는다. 
> 예제코드는 책을 참고하자

### 고급 쿼리 도구 이용하기
장고의 ORM은 배우기 쉽고 직관적이며 다양한 경우를 처리할 수 있다. 하지만 장고의 ORM이 모든 경우를 다 완벽히 처리할 수 있는 것은 아니다.

### 쿼리 표현식
읽기 작업이 수행될 때 쿼리 표현식은 해당 읽기가 실행되는 동안 값을 산출해 내거나 연산을 수행하는 데 이용될 수 있다.
> 상세 예제는 책과 메뉴얼을 참고하자.

## 필수불가결한 상황이 아니라면 로우 SQL은 지양하자
사실 우리가 쓰는 쿼리의 대부분은 단순한 것들이다. ORM이라는 관계형 매핑은 매우 높은 생산성을 제공하는데,단순 쿼리 생성 뿐만 아니라 모델에 대한 접근과 업데이트를 할 때 유효성 검사와 보안을 제공하기 때문이다. 따라서 이용하려는 쿼리를 ORM으로 표현할 수 있다면 반드시 ORM을 이용하자.
또한 개발하는 장고 앱이 서드 파티 패키지로 릴리스된다고 할 때, 로우 SQL을 이용하는 바람에 앱의 이식성이 떨어지는 경우가 생길 수 있다.
흔한 경우는 아니지만 다른 환경의 데이터베이스로 데이터를 마이그레이션 할때도 매우 복잡하다.
> 결론은 엥간해선 로우 SQL은 쓰지 말자.

## 필요에 따라 인덱스를 이용하자
모델에 그냥 db_index=True를 추가하는 것이 그리 어려운 일은 아니다. 하지만 언제 추가해야 하는지는 판단이 필요하다.
> 일반적으로 생각하는 경우여서 넘어간다.


## 트랜잭션
장고 1.8이 되면서 부터 기본적으로 ORM이 모든 쿼리를 호출할 때마다 자동으로 커밋을 하게 되었다. 이 말은 데이터를 수정할 때, 즉 매번 .create()나 .update()가 호출될 때마다 SQL 데이터베이스 안의 값들이 실제로 변한다는 의미다. 이로인한 장점은 초보 개발자들이 ORM을 이해하기가 한결 수월해졌다는 것이며, 단점은 뷰에서 둘 또는 그 이상의 데이터베이스 수정이 요구될 때 첫 번째 수정은 문제가 없었지만 두 번째 수정에서 문제가 발생해 데이터베이스상의 충돌이 일어날 위험이 존재하게 되었다는 것이다.
> 나머진 트랜잭션에 대한 설명으로 넘어간다.

### 각각의 HTTP 요청을 트랜잭션으로 처리하라
장고에서는 ATOMIC_REQUESTS 설정을 통해 모든 웹 요청을 트랜잭션으로 쉽게 처리할 수 있다. 설정값을 True로 설정함으로써 읽기 데이터를 포함한 모든 요청이 트랜잭션으로 처리되게 할 수 있다. 이는 서비스 초기에 트랜잭션 처리를 쉽게 해준다.
> transaction.non_atomic_requests() 데코레이터와 함께 쓰자.

### 명시적인 트랜잭션 선언
명시적인 트랜잭션 선언은 사이트 성능을 개선하는 방법 중 하나다. 트랜잭션에서 어떤 뷰와 비즈니스 로직이 하나로 엮여있고, 어떤 것이 그렇지 않은지 명시해 주는 것이다. 개발할 때 더 많은 시간을 요구하는 것이 단점이다.

트랜잭션 가이드 라인
- 변경이 생기지 않는다면 트랜잭션으로 처리하지 않는다.
- 변경이 생긴다면 반드시 트랜잭션으로 처리한다
- 읽기 작업을 하는중 변경 또는 성능에 관련된 특별한 경우에는 앞의 두 가이드라인을 모두 고려한다.


### django.http.StermingHttpResponse와 트랜잭션
뷰가 django.http.SteramingHttpResponse를 반환한다면 일단 응답이 시작된 이상 트랜잭션 에러를 중간에 처리하기란 불가능하다. 프로젝트에서 이 응답 메서드가 쓰이고 있다면 ATOMIC_REQUESTS가 다음 중 하나를 따라야 한다.
1. Atomic_Requests의 장고 기본값을 false로 설정한다.
2. 뷰를 django.db.transaction.non_atomic_requests 데코레이터로 감싸 본다.
> StreamingHttpResponse가 뭔지 모르겠다.. ㅠㅠ 나중에 찾아봐야지

### MySQL에서의 트랜잭션
MySQL은 엔진에따라 트랜잭션 지원여부가 다르다. 트랜잭션이 지원이 안된다면 장고는 늘 오토커밋모드로 동작한다.
> 아... 역시 맘 편히 postgresql 장고 조합을 쓰자.
