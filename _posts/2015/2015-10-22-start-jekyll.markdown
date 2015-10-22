---
layout: post
title: "Jekyll(지킬)을 이용하여 github pages에 블로그 만들기"
date: "2015-10-22 12:41"
#categories: python django
---
OS X 기준으로 설명한다.

# 설치하기
OS X은 ruby가 설치되어 있다. 버전을 확인하기 위해서는 `ruby -v` 명령어를 이용한다.
jekyll를 설치하기에 앞서 `sudo gem update --system` 으로 gem 업데이트한다.
`sudo gem install jekyll jekyll` 설치한다.

# github pages 설정
아래 명령으로 username.github.io 디렉토리 생성한다.
username은 github의 username을 사용한다.

~~~
jekyll new username.github.io
cd username.github.io
jekyll serve --watch
~~~

브라우저에서 http://127.0.0.1:4000/ 으로 접속하여 기본 페이지를 볼 수 있다.

# github에 push
~~~
git init
git remote add origin remote_url
git add .
git commit -m "init blog"
git push origin master
~~~

몇 분 후 브라우저를 통해 https://username.github.io 접속할 수 있다.

# 참고 자료
* [지킬로 깃허브에 무료 블로그 만들기][refer-1]

[refer-1]: https://nolboo.github.io/blog/2013/10/15/free-blog-with-github-jekyll/
