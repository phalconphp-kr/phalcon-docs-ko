XAMPP에 설치
=====================
XAMPP_는 Apache, MySQL, PHP, Perl을 쉽게 설치할 수 있습니다.
먼저 XAMPP를 다운로드하고 압축하여 시작합니다.
다음은 Windows 용 XAMPP를 사용하는 경우의 Phalcon의 설치 방법을 설명합니다.
XAMPP는 최신 버전을 사용하도록 하십시오.

Phalcon 다운로드
-------------------------------------
XAMPP는 32 bit 버전의 Apache, PHP를 사용하고 있습니다.
Windows 용 Phalcon의 x86 버전을 다운로드해야합니다.

Phalcon 라이브러리를 다운로드하면 다음과 같은 zip 파일이 있어야 합니다.

.. figure::../_static/img/xampp-1.png
    :align: center

압축파일을 해제하여 Phalcon DLL이 있는지 확인하십시오.

.. figure::../_static/img/xampp-2.png
    :align: center

PHP 확장에 php_phalcon.dll 를 복사하십시오.
c:\\xampp folder 에 XAMPP가 설치되어있는 경우 확장의 경로가 c:\\xampp\\php\\ext 로 되어 있을 것입니다.

.. figure::../_static/img/xampp-3.png
    :align: center

c:\\xampp\\php\\php.ini 에있는 php.ini 파일을 텍스트 편집기로 편집합니다.
파일의 마지막에 extension=php_phalcon.dll을 추가 저장합니다.

.. figure::../_static/img/xampp-4.png
    :align: center

XAMPP 컨트롤 센터에서 Apache 를 다시 시작합니다.
새로운 PHP 설정이 로드됩니다.

.. figure::../_static/img/xampp-5.png
    :align: center

브라우저에서 http://localhost를 열면 XAMPP의 시작 페이지가 나타날 것입니다.
그리고 phpinfo() 링크를 클릭하여 엽니다.

.. figure::../_static/img/xampp-6.png
    :align: center

phpinfo() 에서 phalcon 확장이 로드되어 있는지 확인합니다.

.. figure::../_static/img/xampp-7.png
    :align: center

phpinfo()에서 phalcon 버전이 표시되면 설치 성공입니다!

스크린 캐스트
----------
다음은 Windows 용 Phalcon 설치 단계를 설명합니다.

.. raw:: html

   <div align="center"><iframe src="http://player.vimeo.com/video/40265988" width="500" height="266" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe></div>

관련 가이드
--------------
* :doc:`General Installation </reference/install>`
* :doc:`Detailed Installation on WAMP for Windows </reference/wamp>`

.. _XAMPP: http://www.apachefriends.org/en/xampp-windows.html
