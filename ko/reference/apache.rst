Apache 설치 노트 
========================= 
Apache_ 는 많은 플랫폼에서 사용 가능한 인기있는 Web 서버입니다. 

Phalcon을 위한 Apache 구성 
------------------------------ 
다음 설정은 Phalcon과 Apache 를 사용할 때의 설정 예입니다. 여기에서는 주로 사용하기 쉬운 URL과 :doc:`router component <routing>`. 을 사용할 수 있도록 mod-rewrite 모듈을 설정하는 방법에 대해 초점을 맞추고 있습니다. 일반적으로 애플리케이션은 다음과 같은 구조입니다.

.. code-block:: php

    test/
      app/
        controllers/
        models/
        views/
      public/
        css/
        img/
        js/
        index.php

기본 문서 루트 아래의 디렉토리 
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
이것은 가장 일반적인 경우 응용 프로그램은 문서 루트 아래의 모든 디렉토리에 설치되어 있습니다. 이 경우 두개의 .htaccess 파일을 사용합니다. 첫 번째는 응용 프로그램 코드를 숨기기 위해 모든 요청을 응용 프로그램의 문서 루트 (public/)로 리디렉션합니다.

.. code-block:: apacheconf

    # test/.htaccess

    <IfModule mod_rewrite.c>
        RewriteEngine on
        RewriteRule  ^$ public/    [L]
        RewriteRule  (.*) public/$1 [L]
    </IfModule>

두 번째 .htaccess 파일은 public/ 디렉토리에 배치하고 모든 URI를 public/index.php 파일에 다시 작성합니다.

.. code-block:: apacheconf

    # test/public/.htaccess

    <IfModule mod_rewrite.c>
        RewriteEngine On
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteRule ^(.*)$ index.php?_url=/$1 [QSA,L]
    </IfModule>

만약 .htaccess 파일을 사용하고 싶지 않으면 이 설정을 Apache의 기본 설정 파일로 이동시킬 수 있습니다.

.. code-block:: apacheconf

    <IfModule mod_rewrite.c>

        <Directory "/var/www/test">
            RewriteEngine on
            RewriteRule  ^$ public/    [L]
            RewriteRule  (.*) public/$1 [L]
        </Directory>

        <Directory "/var/www/test/public">
            RewriteEngine On
            RewriteCond %{REQUEST_FILENAME} !-d
            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteRule ^(.*)$ index.php?_url=/$1 [QSA,L]
        </Directory>

    </IfModule>

가상 호스트 
^^^^^^^^^^^^^
그리고 이 두 번째 설정은 Virtual Host에 Phalcon 응용 프로그램을 설치할 수 있습니다.

.. code-block:: apacheconf

    <VirtualHost *:80>

        ServerAdmin admin@example.host
        DocumentRoot "/var/vhosts/test/public"
        DirectoryIndex index.php
        ServerName example.host
        ServerAlias www.example.host

        <Directory "/var/vhosts/test/public">
            Options All
            AllowOverride All
            Allow from all
        </Directory>

    </VirtualHost>

.. _Apache: http://httpd.apache.org/
