# routing
A routing use "symfony/routing" and like "laravel/routing" style
```php
    <?php
    use Moon\Routing\Router;
    
    require '../vendor/autoload.php';
      
    /**
     * define routes
     */
    
    $container = new \Moon\Routing\Container();
    
    $router = new Router($container);
    $router->get('/', function(){
        return 'index';
    });
    $router->get('/home/{name}', 'IndexController::home')->setRequirement('name', '([\w\s\x{4e00}-\x{9fa5}]+)?');
    $router->get('/login', 'IndexController::login')->name('login');
    $router->post('/login', 'IndexController::post_login');
    
    $res = $router->any('api', 'ApiController::index')->middleware(['api.auth', 'api.oauth']);
    //var_dump($res);
    
    $router->group(['prefix'=>'admin/', 'middleware'=>'auth'], function($router){
        $router->post('/login', 'AdminController::login');
    });
    
    $router->group(['prefix'=>'admin2/', 'middleware'=>'auth2'], function($router){
        $router->post('/login', 'Admin2Controller::login');
        $router->group(['prefix'=>'admin3/', 'middleware'=>'auth3'], function($router){
            $router->post('/login', 'Admin3Controller::login');
            $router->post('/logout', 'Admin3Controller::login');
        });
    });
    
    $routes = $container->get('routes');
    var_dump(count($routes));
    foreach($routes as $route){
        var_dump($route->getName().'|'.$route->getPath().'|'.json_encode($route->getMethods()).'|'.json_encode($route->getMiddleware()));
    }
    
    /**
     * match request
     */
    $request = \Symfony\Component\HttpFoundation\Request::createFromGlobals();
    
    $path_info = $request->getPathInfo();
    
    echo '<hr>'.$path_info.'<hr>';
    
    $context = new \Symfony\Component\Routing\RequestContext();
    $context->fromRequest($request);
    
    //match
    $matcher = new \Symfony\Component\Routing\Matcher\UrlMatcher($routes, $context);
    $parameters = $matcher->match($request->getPathInfo());
    
    var_dump($parameters);
    
    echo '<hr>';
```

Now use matched result to handle your controller method or Closure! ＼( ^▽^ )／