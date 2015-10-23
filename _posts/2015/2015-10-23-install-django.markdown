---
layout: post
title: "Django 개발 환경 만들기"
date: "2015-10-23 15:13"
---
Python의 대표적인 프레임워크 중 하나인 Django로 개발하기 위한 환경을 만들기 위한 기록이다.
ubuntu에 Django와 uwsgi, nginx 연동을 통해 웹 애플리케이션 개발을 할 수 있도록 한다.

# nginx 설치
아래 쉘 명령을 순서대로 실행한다.

{% highlight bash %}
sudo -s
aptitude install software-properties-common
# use nginx=development for latest development version
nginx=stable
add-apt-repository ppa:nginx/$nginx
apt-get update
apt-get install nginx
{% endhighlight %}
nginx 설치 후 기본 웹페이지 접속하기 위해 http://localhost 로 접속한다.
기본 페이지가 잘 보인다면 django 설치 과정으로 진행한다.

# nginx 참고 자료
* [http://nginx.org/en/linux_packages.html](http://nginx.org/en/linux_packages.html)
* [https://opentutorials.org/module/384/4298](https://opentutorials.org/module/384/4298)
* [https://www.nginx.com/resources/wiki/start/topics/tutorials/install/](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)


# django와 uwsgi 설치
ubuntu는 python이 기본으로 설치되어 있다. `python -V` 또는 `python --version` 으로 버전을 확인한다.
MAC OS X인 경우 기본 설치된 python을 이용해도 되지만 Homebrew를 통해 python을 설치한 후 진행하자.
아래 쉘 명령을 실행한다.

{% highlight bash %}
sudo apt-get install python-pip python-dev
sudo pip install virtualenv
mkdir myproject
cd myproject
virtualenv venv
. venv/bin/activate
{% endhighlight %}
지금까지 문제 없이 진행하였다면 쉘 프롬프트가 `(venv)$`로 변한다.
virtualenv를 종료하려면 `(venv)$ deactivate` 실행한다.
다시 `. venv/bin/activate`를 실행하여 가상 python 환경으로 진입한다.

{% highlight bash %}
(venv)$ pip install Django uwsgi MySQL-python
{% endhighlight %}
새 Django 프로젝트를 생성한 후 프로젝트 디렉토리로 이동한다.

{% highlight bash %}
(venv)$ django-admin.py startproject mysite
(venv)$ cd mysite
{% endhighlight %}

테스트 코드를 작성한 후 http 작동을 확인한다.
{% gist ziplus4/57f074eec5c54106a124 %}
`uwsgi --http :8000 --wsgi-file test.py` uwsgi를 실행한 후 `http://example.com:8000` 브라우저에서 접속하자.

이제 `mysite` django 프로젝트를 테스트해 보자.

{% highlight bash %}
python manage.py runserver 0.0.0.0:8000
{% endhighlight %}
잘 작동한다면 uwsgi를 통해 실행해 보자.

{% highlight bash %}
uwsgi --http :8000 --module mysite.wsgi
{% endhighlight %}
이번에도 `http://example.com:8000` 브라우저에서 접속하여 확인한다.

## nginx와 django 연동
{% gist ziplus4/b208a0241c4cb9a4649f %}

{% highlight bash %}
sudo ln -s ~/path/to/your/mysite/mysite_nginx.conf /etc/nginx/sites-enabled/
{% endhighlight %}

## static file 배포
`mysite/settings.py` 파일에 아래 내용을 추가한다.

{% highlight python %}
STATIC_ROOT = os.path.join(BASE_DIR, "static/")
{% endhighlight %}
그 후 다음을 실행한다.

{% highlight bash %}
python manage.py collectstatic
{% endhighlight %}

## 기본 nginx 테스트

{% highlight bash %}
sudo service nginx restart
{% endhighlight %}
`media.png` 파일(임의의 이미지 파일)을 `/path/to/your/project/project/media` 디렉토리에 넣은 후 `http://example.com:8000/media/media.png`
접속해서 잘 나온다면 설정이 올바른 것이다.

## nginx, uWSGI와 test.py
앞서 작성한 `test.py`를 nginx를 통해 실행해 보자.

{% highlight bash %}
uwsgi --socket :8001 --wsgi-file test.py
{% endhighlight %}
`socket :8001`은 8001포트로 uwsgi 프로토콜을 사용한다는 의미이다.
`http://example.com:8000` 접속 여부를 확인한다.

## port 대신 유닉스 소켓 사용
`mysite_nginx.conf` 파일 내용에서 아래와 같이 수정한다.

{% highlight nginx %}
server unix:///path/to/your/mysite/mysite.sock; # for a file socket
# server 127.0.0.1:8001; # for a web port socket (we'll use this first)
{% endhighlight %}
nginx를 재시작한다.
uWSGI를 다시 실행한다.

{% highlight bash %}
uwsgi --socket mysite.sock --wsgi-file test.py
{% endhighlight %}
`http://example.com:8000` 접속 여부를 확인한다.

## 실행이 되지 않을 경우
먼저 nginx error log(`/var/log/nginx/error.log`)를 확인한다. 만약 다음 내용이 log에 있다면
{% highlight bash %}
connect() to unix:///path/to/your/mysite/mysite.sock failed (13: Permission denied)
{% endhighlight %}
소켓 파일의 권한을 nginx가 사용 가능하도록 변경해야 한다.

{% highlight bash %}
# (very permissive)
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=666
{% endhighlight %}
또는

{% highlight bash %}
# (more sensible)
uwsgi --socket mysite.sock --wsgi-file test.py --chmod-socket=664
{% endhighlight %}
이 때 nginx가 소켓을 읽고 쓸 수 있도록 사용자 계정을 nginx 그룹(아마도 www-data)에 추가하거나 그 반대로 해야할지도 모른다.

{% highlight bash %}
# www-data 그룹에 사용자 계정을 추가.
sudo usermod -aG www-data <username>
# 나의 경우는 반대로 했을 때 잘 작동했다. 즉, www-data 사용자 계정을 현재 사용자의 그룹에 추가.
sudo usermod -aG <usergroup> www-data
{% endhighlight %}

## uwsgi, nginx와 함께 Django 애플리케이션 실행
{% highlight bash %}
uwsgi --socket mysite.sock --module mysite.wsgi --chmod-socket=664
{% endhighlight %}

## .ini 파일과 함께 uwsgi 실행
`mysite_uwsgi.ini`를 작성하자.
{% gist ziplus4/298f6f96b00d9413b9dd %}

이 파일을 가지고 uwsgi를 실행한다.
{% highlight bash %}
uwsgi --ini mysite_uwsgi.ini # the --ini option is used to specify a file
{% endhighlight %}

## 시스템 전체 uwsgi 설치
`deactivate` 명령으로 virtualenv를 종료한다.

{% highlight bash %}
sudo pip install uwsgi

# Or install LTS (long term support).
pip install http://projects.unbit.it/downloads/uwsgi-lts.tar.gz
{% endhighlight %}
uwsgi를 실행한다.
{% highlight bash %}
uwsgi --ini mysite_uwsgi.ini # the --ini option is used to specify a file
{% endhighlight %}

## Emperor 모드

{% highlight bash %}
# create a directory for the vassals
sudo mkdir /etc/uwsgi
sudo mkdir /etc/uwsgi/vassals
# symlink from the default config directory to your config file
sudo ln -s /path/to/your/mysite/mysite_uwsgi.ini /etc/uwsgi/vassals/
# run the emperor
uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data
{% endhighlight %}

{% highlight bash %}
# sudo가 필요할 수 있다.
sudo uwsgi --emperor /etc/uwsgi/vassals --uid www-data --gid www-data
{% endhighlight %}

## Upstart를 통해 uwsgi 서비스하기
Upstart는 Ubuntu 계열의 init 시스템이다.

### 간단한 스크립트

`/etc/init/uwsgi.conf` 파일을 작성한다.
{% highlight bash %}
# /etc/init/uwsgi.conf
# simple uWSGI script

description "uwsgi tiny instance"
start on runlevel [2345]
stop on runlevel [06]

respawn

exec uwsgi --master --processes 4 --die-on-term --socket :3031 --wsgi-file /var/www/myapp.wsgi
{% endhighlight %}

{% highlight bash %}
# 서버 시작 후 자동으로 시작. 다음 명령으로 상태 변경할 수 있다.
sudo service uwsgi [start|stop|restart]
{% endhighlight %}
### Emperor 모드
`/etc/init/uwsgi.conf` 파일을 작성한다.
{% highlight bash %}
# Emperor uWSGI script

description "uWSGI Emperor"
start on runlevel [2345]
stop on runlevel [06]

respawn

exec uwsgi --emperor /etc/uwsgi
{% endhighlight %}


# django 참고 자료
* [http://uwsgi-docs.readthedocs.org/en/latest/tutorials/Django_and_nginx.html](http://uwsgi-docs.readthedocs.org/en/latest/tutorials/Django_and_nginx.html)
* [http://uwsgi-docs.readthedocs.org/en/latest/Upstart.html](http://uwsgi-docs.readthedocs.org/en/latest/Upstart.html)
* [http://stackoverflow.com/questions/23073829/uwsgi-wont-reload-restart-or-let-me-run-service](http://stackoverflow.com/questions/23073829/uwsgi-wont-reload-restart-or-let-me-run-service)
