MVC 응용프로그램
==================
Phalcon에서 MVC기능을 뒤에서 통제하는 큰 작업은 보통 :doc:`Phalcon\\Mvc\\Application <../api/Phalcon_Mvc_Application>` 에서 실행됩니다.

이 컴포넌트에는 MVC 패턴이 원하는대로 작동하는데 필요한 모든
컴포넌트를 인스턴스화하고 프로젝트와 통합하여 백그라운드에서 필수적인 모든 복잡한 작업이
캡슐화되어있습니다.

단일 또는 다중 모듈 응용프로그램
-----------------------------------
이 컴포넌트는 MVC 구조의 여러 유형을 실행할 수 있습니다:

단일모듈
^^^^^^^^^^^^^
단일 MVC 응용프로그램은 하나의 모듈로 구성되어 있습니다. 네임스페이스를 사용하지만 필요하진 않습니다.
이런 응용프로그램은 다음과 같은 구조를 따라야합니다:

.. code-block:: php

    single/
        app/
            controllers/
            models/
            views/
        public/
            css/
            img/
            js/

네임스페이스를 사용하지 않는다면, 부트스트랩 파일은 다음과 같은 MVC 흐름으로
사용될 수 있습니다:

.. code-block:: php

    <?php

    use Phalcon\Loader,
        Phalcon\DI\FactoryDefault,
        Phalcon\Mvc\Application,
        Phalcon\Mvc\View;

    $loader = new Loader();

    $loader->registerDirs(
        array(
            '../apps/controllers/',
            '../apps/models/'
        )
    )->register();

    $di = new FactoryDefault();

    // Registering the view component
    $di->set('view', function() {
        $view = new View();
        $view->setViewsDir('../apps/views/');
        return $view;
    });

    try {

        $application = new Application($di);

        echo $application->handle()->getContent();

    } catch (\Exception $e) {
        echo $e->getMessage();
    }

네임스페이스를 사용한다면, 부트스트랩 파일은 다음과 같이 사용될 수 있습니다:

.. code-block:: php

    <?php

    use Phalcon\Loader,
        Phalcon\Mvc\View,
        Phalcon\DI\FactoryDefault,
        Phalcon\Mvc\Dispatcher,
        Phalcon\Mvc\Application;

    $loader = new Loader();

    // Use autoloading with namespaces prefixes
    $loader->registerNamespaces(
        array(
            'Single\Controllers' => '../apps/controllers/',
            'Single\Models'      => '../apps/models/',
        )
    )->register();

    $di = new FactoryDefault();

    // Register the dispatcher setting a Namespace for controllers
    $di->set('dispatcher', function() {
        $dispatcher = new Dispatcher();
        $dispatcher->setDefaultNamespace('Single\Controllers');
        return $dispatcher;
    });

    // Registering the view component
    $di->set('view', function() {
        $view = new View();
        $view->setViewsDir('../apps/views/');
        return $view;
    });

    try {

        $application = new Application($di);

        echo $application->handle()->getContent();

    } catch(\Exception $e){
        echo $e->getMessage();
    }


다중 모듈
^^^^^^^^^^^^
다중 모듈 응용프로그램은 하나 이상의 모듈이 동일한 루트에 존재합니다. 이 경우 다음과
같은 파일구조를 사용할 수 있습니다:

.. code-block:: php

    multiple/
      apps/
        frontend/
           controllers/
           models/
           views/
           Module.php
        backend/
           controllers/
           models/
           views/
           Module.php
      public/
        css/
        img/
        js/

app/ 디렉토리내에 있는 각 디렉토리는 자신만의 MVC구조를
가지고 있습니다. Module.php는 자동로더나 사용자정의 서비스와 같은 각 모듈만의 특정 설정을 구성할 수 있습니다:

.. code-block:: php

    <?php

    namespace Multiple\Backend;

    use Phalcon\Loader,
        Phalcon\Mvc\Dispatcher,
        Phalcon\Mvc\View,
        Phalcon\Mvc\ModuleDefinitionInterface;

    class Module implements ModuleDefinitionInterface
    {

        /**
         * Register a specific autoloader for the module
         */
        public function registerAutoloaders()
        {

            $loader = new Loader();

            $loader->registerNamespaces(
                array(
                    'Multiple\Backend\Controllers' => '../apps/backend/controllers/',
                    'Multiple\Backend\Models'      => '../apps/backend/models/',
                )
            );

            $loader->register();
        }

        /**
         * Register specific services for the module
         */
        public function registerServices($di)
        {

            //Registering a dispatcher
            $di->set('dispatcher', function() {
                $dispatcher = new Dispatcher();
                $dispatcher->setDefaultNamespace("Multiple\Backend\Controllers");
                return $dispatcher;
            });

            //Registering the view component
            $di->set('view', function() {
                $view = new View();
                $view->setViewsDir('../apps/backend/views/');
                return $view;
            });
        }

    }

특정 부트스트랩 파일은 다중 모듈 MVC 아키텍쳐를 불러올 필요가 있습니다:

.. code-block:: php

    <?php

    use Phalcon\Mvc\Router,
        Phalcon\Mvc\Application,
        Phalcon\DI\FactoryDefault;

    $di = new FactoryDefault();

    //Specify routes for modules
    $di->set('router', function () {

        $router = new Router();

        $router->setDefaultModule("frontend");

        $router->add("/login", array(
            'module'     => 'backend',
            'controller' => 'login',
            'action'     => 'index',
        ));

        $router->add("/admin/products/:action", array(
            'module'     => 'backend',
            'controller' => 'products',
            'action'     => 1,
        ));

        $router->add("/products/:action", array(
            'controller' => 'products',
            'action'     => 1,
        ));

        return $router;
    });

    try {

        //Create an application
        $application = new Application($di);

        // Register the installed modules
        $application->registerModules(
            array(
                'frontend' => array(
                    'className' => 'Multiple\Frontend\Module',
                    'path'      => '../apps/frontend/Module.php',
                ),
                'backend'  => array(
                    'className' => 'Multiple\Backend\Module',
                    'path'      => '../apps/backend/Module.php',
                )
            )
        );

        //Handle the request
        echo $application->handle()->getContent();

    } catch(\Exception $e){
        echo $e->getMessage();
    }

부트스트랩 파일의 모듈설정을 유지하고 싶은 경우에는 모듈을 등록하는 익명의
함수를 사용할 수 있습니다:

.. code-block:: php

    <?php

    //Creating a view component
    $view = new \Phalcon\Mvc\View();

    //Set options to view component
    //...

    // Register the installed modules
    $application->registerModules(
        array(
            'frontend' => function($di) use ($view) {
                $di->setShared('view', function() use ($view) {
                    $view->setViewsDir('../apps/frontend/views/');
                    return $view;
                });
            },
            'backend' => function($di) use ($view) {
                $di->setShared('view', function() use ($view) {
                    $view->setViewsDir('../apps/backend/views/');
                    return $view;
                });
            }
        )
    );

When :doc:`Phalcon\\Mvc\\Application <../api/Phalcon_Mvc_Application>` have modules registered, always is
necessary that every matched route returns a valid module. Each registered module has an associated class
offering functions to set the module itself up. Each module class definition must implement two
methods: registerAutoloaders() and registerServices(), they will be called by
:doc:`Phalcon\\Mvc\\Application <../api/Phalcon_Mvc_Application>` according to the module to be executed.

기본 동작의 이해
----------------------------------
:doc:`tutorial <tutorial>` 을 사용하거나 :doc:`Phalcon Devtools <tools>` 로 코드를 생성하면, 다음과 같은 부트스트랩 파일을 볼 수 있습니다:

.. code-block:: php

    <?php

    try {

        // Register autoloaders
        //...

        // Register services
        //...

        // Handle the request
        $application = new \Phalcon\Mvc\Application($di);

        echo $application->handle()->getContent();

    } catch (\Exception $e) {
        echo "Exception: ", $e->getMessage();
    }

handle()가 호출될 때 컨트롤러의 모든 핵심작업이 실행됩니다:

.. code-block:: php

    <?php

    echo $application->handle()->getContent();

Manual bootstraping
-------------------
:doc:`Phalcon\\Mvc\\Application <../api/Phalcon_Mvc_Application>` 을 사용하지 않으려면, 다음과 같은 코드를 사용할 수 있습니다.

.. code-block:: php

    <?php

    // Get the 'router' service
    $router = $di['router'];

    $router->handle();

    $view = $di['view'];

    $dispatcher = $di['dispatcher'];

    // Pass the processed router parameters to the dispatcher
    $dispatcher->setControllerName($router->getControllerName());
    $dispatcher->setActionName($router->getActionName());
    $dispatcher->setParams($router->getParams());

    // Start the view
    $view->start();

    // Dispatch the request
    $dispatcher->dispatch();

    // Render the related views
    $view->render(
        $dispatcher->getControllerName(),
        $dispatcher->getActionName(),
        $dispatcher->getParams()
    );

    // Finish the view
    $view->finish();

    $response = $di['response'];

    // Pass the output of the view to the response
    $response->setContent($view->getContent());

    // Send the request headers
    $response->sendHeaders();

    // Print the response
    echo $response->getContent();

뷰가 없는 Rest APIs 컴포넌트를 만들기 위해 :doc:`Phalcon\\Mvc\\Application <../api/Phalcon_Mvc_Application>` 를 다음과 같이 대체할 수 있습니다:

.. code-block:: php

    <?php

    // Get the 'router' service
    $router = $di['router'];

    $router->handle();

    $dispatcher = $di['dispatcher'];

    // Pass the processed router parameters to the dispatcher
    $dispatcher->setControllerName($router->getControllerName());
    $dispatcher->setActionName($router->getActionName());
    $dispatcher->setParams($router->getParams());

    // Dispatch the request
    $dispatcher->dispatch();

    //Get the returned value by the lastest executed action
    $response = $dispatcher->getReturnedValue();

    //Check if the action returned is a 'response' object
    if ($response instanceof Phalcon\Http\ResponseInterface) {

        //Send the request
        $response->send();
    }

Yet another alternative that catch exceptions produced in the dispatcher forwarding to other actions consequently:

.. code-block:: php

    <?php

    // Get the 'router' service
    $router = $di['router'];

    $router->handle();

    $dispatcher = $di['dispatcher'];

    // Pass the processed router parameters to the dispatcher
    $dispatcher->setControllerName($router->getControllerName());
    $dispatcher->setActionName($router->getActionName());
    $dispatcher->setParams($router->getParams());

    try {

        // Dispatch the request
        $dispatcher->dispatch();

    } catch (Exception $e) {

        //An exception has ocurred, dispatch some controller/action aimed for that

        // Pass the processed router parameters to the dispatcher
        $dispatcher->setControllerName('errors');
        $dispatcher->setActionName('action503');

        // Dispatch the request
        $dispatcher->dispatch();

    }

    //Get the returned value by the lastest executed action
    $response = $dispatcher->getReturnedValue();

    //Check if the action returned is a 'response' object
    if ($response instanceof Phalcon\Http\ResponseInterface) {

        //Send the request
        $response->send();
    }

Although the above implementations are a lot more verbose than the code needed while using :doc:`Phalcon\\Mvc\\Application <../api/Phalcon_Mvc_Application>`,
it offers an alternative in boostraping your application. Depending on your needs, you might want to have full control of what
should be instantiated or not, or replace certain components with those of your own to extend the default functionality.

Application Events
------------------
:doc:`Phalcon\\Mvc\\Application <../api/Phalcon_Mvc_Application>` is able to send events to the :doc:`EventsManager <events>`
(if it is present). Events are triggered using the type "application". The following events are supported:

+---------------------+--------------------------------------------------------------+
| Event Name          | Triggered                                                    |
+=====================+==============================================================+
| boot                | Executed when the application handles its first request      |
+---------------------+--------------------------------------------------------------+
| beforeStartModule   | Before initialize a module, only when modules are registered |
+---------------------+--------------------------------------------------------------+
| afterStartModule    | After initialize a module, only when modules are registered  |
+---------------------+--------------------------------------------------------------+
| beforeHandleRequest | Before execute the dispatch loop                             |
+---------------------+--------------------------------------------------------------+
| afterHandleRequest  | After execute the dispatch loop                              |
+---------------------+--------------------------------------------------------------+

The following example demonstrates how to attach listeners to this component:

.. code-block:: php

    <?php

    use Phalcon\Events\Manager as EventsManager;

    $eventsManager = new EventsManager();

    $application->setEventsManager($eventsManager);

    $eventsManager->attach(
        "application",
        function($event, $application) {
            // ...
        }
    );

External Resources
------------------
* `MVC examples on Github <https://github.com/phalcon/mvc>`_