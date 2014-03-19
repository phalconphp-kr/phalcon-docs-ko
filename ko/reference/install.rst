설치
============
PHP 확장 모듈은 기존의 PHP 기반 라이브러리와 프레임 워크와는 약간 다른 설치 방법을 취합니다.
당신의 시스템용 바이너리 패키지를 다운로드하거나 소스 코드에서 빌드하는 두 가지 방법이 있습니다.

.. highlights::
    Phalcon 은 PHP 5.3.1 이상으로 컴파일 할 수 있지만 오래된 PHP는 메모리 누수를 일으키는 버그가 있기 때문에 적어도 PHP 5.3.11 이상을 사용하는 것을 권장하고 있습니다.

.. highlights::
   PHP 5.3.9 이전에는 몇 가지 보안적인 결함이 있기에 상용 환경의 WEB 사이트에서의 사용은 권장하고 있지 않습니다. `자세한 것은 이쪽 <http://www.infoworld.com/d/security/php-539-fixes-hash-collision-dos-vulnerability-183947>`_

Windows
-------
Windows에서 Phalcon 을 사용하려면 DLL 라이브러리를 다운로드합니다. 그리고 php.ini 파일을 편집한 다음에 다음 줄을 추가합니다.

    extension=php_phalcon.dll

마지막으로 WEB 서버를 다시 시작합니다.

다음 스크린 캐스트는 Windows에 Phalcon 를 설치하는 단계별 가이드 입니다.

.. raw:: html

    <div align="center"><iframe src="http://player.vimeo.com/video/40265988" width="500" height="266" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe></div>

관련 가이드
^^^^^^^^^^^^^^
.. toctree::
    :maxdepth: 1

    xampp
    wamp

Linux/Solaris/Mac
-----------------
Linux/Solaris/Mac 환경에서는 쉽게 확장 모듈을 소스 코드에서 컴파일하여 설치할 수 있습니다.

요구 사항
^^^^^^^^^^^^
필요한 패키지는 다음과 같습니다:

* PHP 5.3.x/5.4.x/5.5.x development resources
* GCC compiler (Linux/Solaris) or Xcode (Mac)
* Git (if not already installed in your system - unless you download the package from GitHub and upload it on your server via FTP/SFTP)

일반적인 플랫폼의 구체적인 패키지:

.. code-block:: bash

    #Ubuntu
    sudo apt-get install git-core gcc autoconf
    sudo apt-get install php5-dev php5-mysql

    #Suse
    sudo yast -i gcc make autoconf2.13
    sudo yast -i php5-devel php5-pear php5-mysql

    #CentOS/RedHat
    sudo yum install gcc make
    sudo yum install php-devel

    #Solaris
    pkg install gcc-45
    pkg install php-53 apache-php53

컴파일
^^^^^^^^^^^
확장 모듈 만들기:

.. code-block:: bash

    git clone git://github.com/phalcon/cphalcon.git
    cd cphalcon/build
    sudo ./install

php.ini 에 확장 모듈을 추가합니다.

.. code-block:: bash

    extension=phalcon.so

마지막으로 WEB 서버를 다시 시작 합니다.

Phalcon 은 자동으로 시스템의 아키텍처를 판정하지만 지정된 아키텍처용으로 컴파일 하는 것을 강제 할 수 있습니다.

.. code-block:: bash

    sudo ./install 32bits
    sudo ./install 64bits
    sudo ./install safe

FreeBSD
-------
FreeBSD 에서 port 를 이용하실 수 있습니다. 설치 하려면 다음의 간단한 명령을 치는 것만으로 됩니다.

.. code-block:: bash

    pkg_add -r phalcon

or

.. code-block:: bash

    export CFLAGS="-O2 -fno-delete-null-pointer-checks"
    cd /usr/ports/www/phalcon && make install clean

설치 노트
------------------
각 WEB 서버의 설치 노트

.. toctree::
    :maxdepth: 1

    apache
    nginx
    cherokee
    built-in
