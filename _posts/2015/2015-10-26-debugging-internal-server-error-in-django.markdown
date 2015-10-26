---
layout: post
title: debugging INTERNAL SERVER ERROR in Django
date: '2015-10-26 12:37'
---

Django에서 Internal Server Error가 발생하였을 때 log파일에서 원인을 찾으려고 했지만 log파일에 관련 로그가 남아 있지 않아서 구글링을 통해 찾은 방법이다.

~~~
python manage.py shell --traceback
~~~

settings.py에 오류가 있어서 log파일에는 미처 로그가 남지 않은 모양이다.

# 참고 자료
* [https://snakeycode.wordpress.com/2014/05/27/tip-for-debugging-internal-server-error-in-django/](https://snakeycode.wordpress.com/2014/05/27/tip-for-debugging-internal-server-error-in-django/)
