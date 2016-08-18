#어떻게 장고 프로젝트를 구성할 것인가
프로젝트 레이아웃은 코어 장고 개발자들 사이에서도 여러 의견이 분분하ㄷ.

## 장고 1.8의 기본 프로젝트 구성
```
django-admin.py startproject mysite
cd mysite
django-admin.py startapp my_app
```
앞의 명령어를 통해 생성된 프로젝트 구성은 다음과 같다.

- mysite/
- - my_app/
- - - __init__.py
- - - admin.py
- - - models.py
- - - tests.py
- - - views.py
- - mysite/
- - - __init__.py
- - - settings.py
- - - urls.py
- - - wsgi.py

기본 프로젝트 구성은, 튜토리얼용으로는 유용하지만 실제 프로젝트에 적용해보면 그다지 유용하지 않은 부분들이 발견된다.

## 우리(투스쿱)가 선호하는 프로젝트 구성
django-admin.py startproject 명령을 이용하면 삼단 방식에 기반을 둔 구조가 생성되며 우리 또한 이를 기반으로 프로젝트를 구성한다. 우선 앞서 내린 명령을 통해 생성된 내용을 깃 저장소의 루트로 이용되는 디렉터리 안으로 모두 올김단. 우리가 만든 구성의 최상단부는 다음과 같다.
