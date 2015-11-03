---
layout: post
title: Apache HTTPD에 SSL Certificate 설치
date: '2015-11-03 15:37'
---

{% highlight bash %}
# virtual host config
SSLEngine on
SSLCertificateKeyFile /etc/apache2/ssl/cert.key
SSLCertificateFile /etc/apache2/ssl/STAR_example_com.crt
SSLCertificateChainFile /etc/apache2/ssl/STAR_example_com.ca-bundle
{% endhighlight %}


{% highlight bash %}
$ sudo a2enmod ssl
$ sudo service apache2 restart
{% endhighlight %}

# 참고 자료
* [https://support.comodo.com/index.php?/Default/Knowledgebase/Article/View/637/37/certificate-installation-apache--mod_ssl](https://support.comodo.com/index.php?/Default/Knowledgebase/Article/View/637/37/certificate-installation-apache--mod_ssl)
* [https://www.comodossl.co.kr/certificate/ssl-installation-guides/Apache-csr-crt.aspx](https://www.comodossl.co.kr/certificate/ssl-installation-guides/Apache-csr-crt.aspx)
