---
layout: post
title: bitbucket에 ssh key를 이용하여 접속하기
date: '2015-11-03 15:38'
---

# SSH 클라이언트 설치 여부 확인
{% highlight bash %}
$ ssh -v
OpenSSH_5.9p1 Debian-5ubuntu1.7, OpenSSL 1.0.1 14 Mar 2012
usage: ssh [-1246AaCfgKkMNnqsTtVvXxYy] [-b bind_address] [-c cipher_spec]
           [-D [bind_address:]port] [-e escape_char] [-F configfile]
           [-I pkcs11] [-i identity_file]
           [-L [bind_address:]port:host:hostport]
           [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port]
           [-R [bind_address:]port:host:hostport] [-S ctl_path]
           [-W host:port] [-w local_tun[:remote_tun]]
           [user@]hostname [command]
{% endhighlight %}
SSH가 설치되어 있다면 위와 같은 버전 정보가 보여진다. 만약 설치되어 있지 않다면 설치한다.

`~/.ssh` 디렉토리를 보면 대부분의 경우 다음과 같다. 만약 `.ssh`디렉토리가 없다면 다음 과정 진행 중에 생성되므로 염려할 필요는 없다.
{% highlight bash %}
$ ls -a ~/.ssh
known_hosts
{% endhighlight %}

기본 identity를 만들었다면 다음과 같이 보인다.
{% highlight bash %}
$ ls -a ~/.ssh
.        ..        id_rsa        id_rsa.pub    known_hosts
{% endhighlight %}

기본 identity 파일 `id_rsa.pub`가 있다면 다음 과정을 건너 뛰고 진행한다.

# identity 파일 만들기
{% highlight bash %}
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/emmap1/.ssh/id_rsa):
Created directory '/Users/emmap1/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/emmap1/.ssh/id_rsa.
Your public key has been saved in /Users/emmap1/.ssh/id_rsa.pub.
The key fingerprint is:
4c:80:61:2c:00:3f:9d:dc:08:41:2e:c0:cf:b9:17:69 emmap1@myhost.local
The key's randomart image is:
+--[ RSA 2048]----+
|*o+ooo.          |
|.+.=o+ .         |
|. *.* o .        |
| . = E o         |
|    o . S        |
|   . .           |
|     .           |
|                 |
|                 |
+-----------------+
{% endhighlight %}

# bitbucket 계정에 public key 등록
* bitbucket에 로그인한 후 **avatar > Manage account** 메뉴로 이동한다.
* **SSH keys** 를 클릭한다.
* 터미널에서 위의 단계에서 생성한 identity의 public key를 복사한다.
Linux에서 다음 명령으로 출력되는 key를 복사한다.
{% highlight bash %}
$ cat ~/.ssh/id_rsa.pub
{% endhighlight %}
Mac OS X에서는 다음 명령으로 클립보드에 복사한다.
{% highlight bash %}
$ pbcopy < ~/.ssh/id_rsa.pub
{% endhighlight %}
*  복사한 내용을 SSH keys에 등록한다.

# ssh key를 이용하기
git을 clone할 때 ssh protocol을 이용한다.

# 참고 자료
* [https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html)
