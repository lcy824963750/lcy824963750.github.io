---
layout: post
title: 'Apache��������֤�û�'
date: '2014-11-16'
header-img: "img/post-bg-unix.jpg"
tags:
     - server
author: 'Codeboy'
---


��ʱ��������Ҫ����apache���������ƶ���Ŀ¼�����û���֤������һЩ���û������ļ����������������:

1 �����û�
----
	htpasswd -c file_path user_name
	
�س�֮���������뼴�ɣ���ȷ�������е�file _path�������û�����Ȩ�ޡ�

2 ����apache
----
��/etc/apache2/apache2.conf��/etc/httpd/conf/httpd.conf�������������

	<Directory /var/www/html/picture>
		AuthName "Important Directory"  #��¼ʱ��ʾ������
		AuthType Basic  #��֤��ʽ
		IndexOptions Charset=GB2312  #��ҳ����
		Options Indexes FollowSymLinks MultiViews #��Ŀ¼��ʽչʾ
		AuthUserFile /opt/.apache_user #�û��ļ���1��file_path
		Require valid-user 
	</Directory>

��Ҫ���ط�������ʾ�����������ļ��м���������Ϣ��

	ServerSignature Off
	ServerTokens Prod

> �����κ�֪ʶ��Ȩ����Ȩ��������۴��󣬻���ָ����
>
> ת����ע��ԭ���߼�������Ϣ��