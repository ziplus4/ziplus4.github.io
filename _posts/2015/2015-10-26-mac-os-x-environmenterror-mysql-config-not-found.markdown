---
layout: post
title: "Mac OS X EnvironmentError: mysql_config not found"
date: "2015-10-26 11:59"
---
Mac OS X에서 `pip install MySQL-python`으로 MySQL-python을 설치할 때 아래 오류가 발생할 때가 있다.

{% highlight bash %}
Collecting MySQL-python
  Downloading MySQL-python-1.2.5.zip (108kB)
    100% |████████████████████████████████| 110kB 906kB/s
    Complete output from command python setup.py egg_info:
    sh: mysql_config: command not found
    Traceback (most recent call last):
      File "<string>", line 20, in <module>
      File "/private/var/folders/mn/j3dcpdwx689602btjnjy1s680000gn/T/pip-build-1V1Did/MySQL-python/setup.py", line 17, in <module>
        metadata, options = get_config()
      File "setup_posix.py", line 43, in get_config
        libs = mysql_config("libs_r")
      File "setup_posix.py", line 25, in mysql_config
        raise EnvironmentError("%s not found" % (mysql_config.path,))
    EnvironmentError: mysql_config not found

    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /private/var/folders/mn/j3dcpdwx689602btjnjy1s680000gn/T/pip-build-1V1Did/MySQL-python
{% endhighlight %}

아래 방법으로 해결할 수 있다.

* `brew install mysql` 설치
* `pip install MySQL-python`

# Ubuntu의 경우
libmysqlclient-dev를 설치한다.
~~~
sudo apt-get install libmysqlclient-dev
~~~

# 참고 자료
* [http://stackoverflow.com/questions/25459386/mac-os-x-environmenterror-mysql-config-not-found](http://stackoverflow.com/questions/25459386/mac-os-x-environmenterror-mysql-config-not-found)
* [http://stackoverflow.com/questions/5178292/pip-install-mysql-python-fails-with-environmenterror-mysql-config-not-found](http://stackoverflow.com/questions/5178292/pip-install-mysql-python-fails-with-environmenterror-mysql-config-not-found)
