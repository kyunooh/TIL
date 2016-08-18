#어떻게 장고 프로젝트를 구성할 것인가
프로젝트 레이아웃은 코어 장고 개발자들 사이에서도 여러 의견이 분분하다.

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
django-admin.py startproject 명령을 이용하면 삼단 방식에 기반을 둔 구조가 생성되며 우리 또한 이를 기반으로 프로젝트를 구성한다.

### 최상위 레벨: 저장소 루트
최상위 <repository-root>/ 디렉터리는 프로젝트의 최상위 절대 루트다. <django_project_root> 이외에 README.rst, docs/ 디렉터리, .gitignore, requirements.txt, 그리고 배포에 필요한 다른 파일등 중요한 내용이 위치한다.

### 두 번째 레벨: 프로젝트 루트
두 번째 레벨은 장고 프로젝트 소스들이 위치하는 디렉터리다. 모든 파이썬 코드는 <django_project_root>/ 디렉터리 아래와 그 하부 디렉터리들에 위치한다. django-admin.py startproject 명령어를 이용할 때 명령어를 저장소 루트 디렉터리 안에서 실행하면 생성된 장고 프로젝트가 프로젝트 루트가 된다.

### 세 번째 레벨: 설정 루트
<configuration_root> 디렉터리는 settings 모듈과 기본 URLConf(urls.py)가 저장되는 장소다. 이 디렉터리는 유효한 파이썬 패키지 형태여야 한다(__init__.py 모듈이 존재해야 한다는 의미이기도 하다).
설정 루트 안의 파일은 django-admin.py start-project 명령으로 생성된 파일의 일부다.

## 예제 프로젝트 구성
// TODO
