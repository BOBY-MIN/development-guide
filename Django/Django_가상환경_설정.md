
> 참고 사이트

```
https://www.djangoproject.com/

```


> 가상환경 생성(window 기준 / python3 venv)

```
-- devenv 가상환경 만들기
-- 명령어를 수행한 디렉토리 하위로 생성
D:\devPy> python -m venv devenv

-- 가상환경 실행하기
D:\devPy> devenv\Scripts\activate

-- 가상환경 종료하기
D:\devPy> devenv\Scripts\deactivate


-- 가상환경 내 pip upgrade
(devenv) D:\devPy> python -m pip install --upgrade pip

-- 가상환경 내 django 설치(이후 필요한 패키지도 같은 방식으로 설치)
(devenv) D:\devPy> pip install django==3.2

-- mysql databaser connector 설치
(devenv) D:\devPy> pip install mysqlclient


-- django 프로젝트 생성
(devenv) D:\devPy> django-admin startproject devmysite



-- 패키지 목록 확인
(devenv) D:\devPy> pip list
-- python 버전 확인
(devenv) D:\devPy> python


```

* 가상환경 실행 시 가상환경 명이 접두어로 붙게 된다.
  + (devenv) D:\devPy>

<br>

> 이클립스 pyDev 프로젝트 생성 후 사용한 가상환경 내 명령어 모음

*  이클립스 pyDev 프로젝트 생성 시 가상환경으로 interpreter 설정한 경우 해당 가상환경 내에서 아래 명령어 수행해야 정상 작동함.

```
-- db 재연동
(devenv) D:\devMin\git\django_dev\devmysite\src> python manage.py inspectdb

-- app 생성
(devenv) D:\devMin\git\django_dev\devmysite\src> python manage.py startapp user

-- model 초기화
(devenv) D:\devMin\git\django_dev\devmysite\src> python manage.py migrate
(devenv) D:\devMin\git\django_dev\devmysite\src> python manage.py makemigrations

```


> 이클립스 pyDev 로 프로젝트 에러 모음

* 최초 프로젝트 생성 시 설정파일 미생성(manage.py, urls.py 등)  
  => 프로젝트명에 '-' 기재 시 설정파일 생성되지 않음
