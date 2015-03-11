##CSRF_TOKEN authentication with Laravel and AngularJS
* using `angular.constant` to inject `csrf_token`
* using `$sanitize` to strip HTML input
* using the `'before' => 'Auth'` filter to authenticate user
* using `sessionStorage()` to create a front-end authentication key`


src: https://www.youtube.com/watch?v=18ifoT-Id54
src: https://www.youtube.com/watch?v=hqAyiqUs93c

...\testlab70>php artisan migrate:make create_users_table --create=users
...\testlab70>php artisan migrate:make create_sessions_table --create=sessions

```php
<?php
// File: app/database/migrations/create_users_table.php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration {

	/**
	 * Run the migrations.
	 *
	 * @return void
	 */
	public function up()
	{
		Schema::create('users', function(Blueprint $table)
		{
			$table->increments('id');
            $table->string('email')->unique();
            $table->string('password');
            $table->string('remember_token',100)->nullable();
			$table->timestamps();
		});
	}

	/**
	 * Reverse the migrations.
	 *
	 * @return void
	 */
	public function down()
	{
		Schema::drop('users');
	}

}
```

```php
<?php
// File: app/database/migrations/create_sessions_table.php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateSessionsTable extends Migration {

	/**
	 * Run the migrations.
	 *
	 * @return void
	 */
	public function up()
	{
		Schema::create('sessions', function(Blueprint $table)
		{
			$table->increments('id');
            $table->text('payload');
            $table->integer('last_activity');
			$table->timestamps();
		});
	}

	/**
	 * Reverse the migrations.
	 *
	 * @return void
	 */
	public function down()
	{
		Schema::drop('sessions');
	}

}
```

```php
<?php
// File: app/config/view.php

return array(

	/*
	|--------------------------------------------------------------------------
	| View Storage Paths
	|--------------------------------------------------------------------------
	|
	| Most templating systems load templates from disk. Here you may specify
	| an array of paths that should be checked for your views. Of course
	| the usual Laravel view path has already been registered for you.
	|
	*/

	//'paths' => array(__DIR__.'/../views'),
	'paths' => array(__DIR__.'/../../public/angularjs/'),

	/*
	|--------------------------------------------------------------------------
	| Pagination View
	|--------------------------------------------------------------------------
	|
	| This view will be used to render the pagination link output, and can
	| be easily customized here to show any view you like. A clean view
	| compatible with Twitter's Bootstrap is given to you by default.
	|
	*/

	'pagination' => 'pagination::slider-3',

);
```

```php
<?php
// File: bootstrap/start.php

/*
|--------------------------------------------------------------------------
| Create The Application
|--------------------------------------------------------------------------
|
| The first thing we will do is create a new Laravel application instance
| which serves as the "glue" for all the components of Laravel, and is
| the IoC container for the system binding all of the various parts.
|
*/

$app = new Illuminate\Foundation\Application;

/*
|--------------------------------------------------------------------------
| Detect The Application Environment
|--------------------------------------------------------------------------
|
| Laravel takes a dead simple approach to your application environments
| so you can just specify a machine name for the host that matches a
| given environment, then we will automatically detect it for you.
|
*/

foreach (require __DIR__.'/../.environment' as $key => $value)
{
    putenv(sprintf('%s=%s', $key, $value));
}

$env = $app->detectEnvironment(function(){
    return getenv('APP_ENV');
});

/*
|--------------------------------------------------------------------------
| Bind Paths
|--------------------------------------------------------------------------
|
| Here we are binding the paths configured in paths.php to the app. You
| should not be changing these here. If you need to change these you
| may do so within the paths.php file and they will be bound here.
|
*/

$app->bindInstallPaths(require __DIR__.'/paths.php');

/*
|--------------------------------------------------------------------------
| Load The Application
|--------------------------------------------------------------------------
|
| Here we will load this Illuminate application. We will keep this in a
| separate location so we can isolate the creation of an application
| from the actual running of the application with a given request.
|
*/

$framework = $app['path.base'].
                 '/vendor/laravel/framework/src';

require $framework.'/Illuminate/Foundation/start.php';

/*
|--------------------------------------------------------------------------
| Return The Application
|--------------------------------------------------------------------------
|
| This script returns the application instance. The instance is given to
| the calling script so we can separate the building of the instances
| from the actual running of the application and sending responses.
|
*/

return $app;
```


```php
<?php
// File: .environment

return array(
    'APP_ENV' => 'local'
);
```


```php
// File: app/routes.php

// 69
Route::get('/69', function()
{
	return View::make('/69/index');
});
Route::get('/69/create-admin', function()
{
    $user = new User;
    $user->email = 'andybendyman@hotmail.com';
    $user->password = Hash::make('password');
    $user->save();
});
Route::post('/auth/login', array('before' => 'csrf_json', 'uses' => 'AuthController@login'));
Route::get('/auth/logout', 'AuthController@logout');
Route::get('/api/books', array('before' => 'auth',function() {
    return Response::json(array(
        array('title' => 'Great Expectations', 'author' => 'Dickens'),
        array('title' => 'Foundation', 'author' => 'Asimov'),
        array('title' => 'Treasure Island', 'author' => 'Stephenson')
    ));
}));
```

```php
<?php
// File: app/models/User.php

use Illuminate\Auth\UserTrait;
use Illuminate\Auth\UserInterface;
use Illuminate\Auth\Reminders\RemindableTrait;
use Illuminate\Auth\Reminders\RemindableInterface;

class User extends Eloquent implements UserInterface, RemindableInterface {

	use UserTrait, RemindableTrait;

	/**
	 * The database table used by the model.
	 *
	 * @var string
	 */
	protected $table = 'users';

	/**
	 * The attributes excluded from the model's JSON form.
	 *
	 * @var array
	 */
	protected $hidden = array('password', 'remember_token');

}
```

```php
<?php
// File: app/controllers/AuthController.php

class AuthController extends BaseController {

    public function login(){
        if(Auth::attempt([
            'email' => Input::json('email'),
            'password' => Input::json('password')
        ])){
            return Response::json(Auth::user());
        }
        else
        {
            return Response::json(array('message' => 'Invalid username or password'), 500);
        }
    }

    public function logout(){
        Auth::logout();
        return Response::json(array('message' => 'Logged Out'));
    }
}
```








```html
// excerptFromFile: angularjs/69/index.php
<script>
    angular.module('app')
        .constant('CSRF_TOKEN', {
            csrf_token: '<?php echo csrf_token(); ?>'
        })
    ;
</script>
```

```javascript
// excerptFromFile: angularjs/69/app.js 
login: function(credentials){
     return $http
           .post('/auth/login', angular.extend(credentials, CSRF_TOKEN))
           .success(cacheSession)
           .success(FlashService.clear)
           .error(loginError)
    ;
}
```

Alternatively, we could have done it as follows when we decide to santize the input.  Note that in 
this version, we did not need to create an object literal for the `CSRF_TOKEN`, instead we simply gave it
a value.

```html
// excerptFromFile: angularjs/69/index.php
<script>
    angular.module('app')
        .constant('CSRF_TOKEN', '<?php echo csrf_token(); ?>')
    ;
</script>
```
            
```javascript
// excerptFromFile: angularjs/69/app.js 

...
var sanitizeCredentials = function(credentials){
    return {
        email: $sanitize(credentials.email),
        password: $sanitize(credentials.password),
        csrf_token: CSRF_TOKEN
    }
}

...
login: function(credentials){
    return $http
        .post('/auth/login', sanitizeCredentials(credentials))
        .success(cacheSession)
        .success(FlashService.clear)
        .error(loginError)
    ;
}
```


```html
<!DOCTYPE html>
<!-- angularjs/69/index.php -->
<html ng-app="app">
<head>
    <title>Laravel & AngularJS</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <link rel="stylesheet" href="/angularjs/69/style.css"/>

</head>
<body>
    <div class="container">
        <h1>Introduction to Laravel and AngularJS</h1>
        <div class="alert-danger" ng-show="errorMessage">
            {{ errorMessage }}
        </div>
        <ng-view></ng-view>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>

    <!-- when angular-sanitize script was placed before angular -- app module load errors -->
    <script src="/bower_components/angular-sanitize/angular-sanitize.min.js"></script>
    <script src="/bower_components/angular-route/angular-route.min.js"></script>
    <script src="/bower_components/lodash/lodash.min.js"></script>
    <script src="/angularjs/69/app.js"></script>
    <script>
        angular.module('app')
            .constant('CSRF_TOKEN', '<?php echo csrf_token(); ?>')
        ;
    </script>
</body>
</html>
```

```javascript
(function(){
    // File: angularjs/69/app.js
    
    'use strict';

    angular.module('app',['ngRoute','ngSanitize'])
        .config(config1)
        .config(config1)
        .run(run)
        .controller('LoginController', LoginController)
        .controller('HomeController', HomeController)
        .directive('showsMessageWhenHovered',showsMessageWhenHoveredDirective)
        .factory('AuthService', AuthServiceFactory)
        .factory('SessionService', SessionServiceFactory)
        .factory('FlashService', FlashServiceFactory)
        .factory('BookService', BookServiceFactory)
        .controller('BooksController', BooksController)
    ;


    function config1($routeProvider) {
        $routeProvider
            .when('/login', {
                templateUrl: '/angularjs/69/login.html',
                controller: 'LoginController as loginVm'
            })
            .when('/home', {
                templateUrl: '/angularjs/69/home.html',
                controller: 'HomeController as homeVm'
            })
            .when('/books', {
                templateUrl: '/angularjs/69/books.html',
                controller: 'BooksController as booksVm',
                resolve: {
                    booksResponse: function(BookService){
                        return BookService.get();
                    }
                }
            })
            .otherwise({
                redirectTo: '/login'
            })
        ;
    }

    function config2($httpProvider, $provide) {

        // Method 1: register the interceptor as a service
        //
        $provide.factory('myHttpInterceptor', function($q, $location, SessionService, FlashService){
            return {
                'response': function(response){
                    return response;
                },
                'responseError':function(response){
                    if(response.status === 401) {
                        SessionService.unset('authenticated');
                        $location.path('/login');
                        FlashService.show(response.data.message);
                    }
                    return $q.reject(response);
                }
            };
        });

        $httpProvider.interceptors.push('myHttpInterceptor');



        /*
        // Method 2: register the interceptor via an anonymous factory
        //
        $httpProvider.interceptors.push(function($q, $location, SessionService, FlashService){
            return {
                  'response': function(response){
                      return response;
                  },
                  'responseError':function(response){
                      if(response.status === 401) {
                          SessionService.unset('authenticated');
                          $location.path('/login');
                          FlashService.show(response.data.flash);
                      }
                      return $q.reject(response);
                  }
            }
        });
        */

        /*
        // Method 3: register the interceptor via an anonymous factory
        //
        var myHttpInterceptor = function($location, $q, SessionService, FlashService) {
            var success = function(response) {
                return response;
            };
            var error = function(response) {
                if(response.status === 401) {
                    SessionService.unset('authenticated');
                    $location.path('/login');
                    FlashService.show(response.data.flash);
                }
                return $q.reject(response);
            };
            return function(promise) {
                return promise.then(success, error);
            };
        };
        $httpProvider.interceptors.push(myHttpInterceptor);
        */
    }

    function run($rootScope, $location, AuthService, FlashService){
            var routesThatRequireAuth = ['/home'];

        $rootScope.$on('$routeChangeStart', function(event, next, current){
            if(_(routesThatRequireAuth).contains($location.path()) && !AuthService.isLoggedIn()){
                $location.path('/login');
                FlashService.show('Please log in to continue');
            }
        });
    }

    function LoginController(AuthService, $location){
        var vm = this;
        vm.credentials = {
            email: "",
            password: ""
        };

        vm.login = function(){
            AuthService.login(vm.credentials)
                .success(function(){
                    $location.path('/home');
                });
        };

    }

    function HomeController(AuthService, $location){
        var vm = this;
        vm.title = 'Home';
        vm.message = 'Mouse over these images to see a directive ar work';

        vm.logout = function(){
            AuthService.logout()
                .success(function(){
                    $location.path('/login');
                });
        }
    }

    function showsMessageWhenHoveredDirective(){
        return {
            restrict: 'A',
            link: function(scope, element, attributes){
                var originalMessage = scope.homeVm.message;
                element.bind('mouseover', function(){
                    scope.homeVm.message = attributes.message;
                    scope.$apply();
                });
                element.bind('mouseout', function(){
                    scope.homeVm.message = originalMessage;
                    scope.$apply();
                });
            }
        }
    }

    function SessionServiceFactory(){
        return {
            get: function(key){
                return sessionStorage.getItem(key);
            },
            set: function(key, value){
                return sessionStorage.setItem(key,value);
            },
            unset: function(key){
                return sessionStorage.removeItem(key);
            }

        }
    }

    function AuthServiceFactory($location, $http, $sanitize, SessionService, FlashService,CSRF_TOKEN){

        var cacheSession = function(){
            SessionService.set('auth', true);
        };

        var uncacheSession = function(){
            SessionService.unset('auth');
        };

        var loginError = function(response){
            FlashService.show(response.message)
        };

        var sanitizeCredentials = function(credentials){

            // We can add a debugger to interact with the script via console and type '> credentials'
            // debugger;
            return {
                email: $sanitize(credentials.email),
                password: $sanitize(credentials.password),
                csrf_token: CSRF_TOKEN
            }
        }

        return {
            login: function(credentials){
                return $http
                    .post('/auth/login', sanitizeCredentials(credentials))
                    .success(cacheSession)
                    .success(FlashService.clear)
                    .error(loginError)
                ;
            },
            logout: function(){
                return $http
                    .get('/auth/logout')
                    .success(uncacheSession)
                ;
            },
            isLoggedIn: function(){
                return SessionService.get('auth');
            }
        };
    }

    function FlashServiceFactory($rootScope){
        return {
            show: function(message){
                $rootScope.errorMessage = message;
            },
            clear: function(){
                $rootScope.errorMessage = '';
            }
        };
    }

    function BookServiceFactory($http){
        return {
            get: function(){
                return $http
                    .get('api/books');
            }
        }
    }

    function BooksController(booksResponse){
        var vm = this;
        vm.books = booksResponse.data;
    }
})();
```

```html
<!-- File: angularjs/69/login.html -->
<div id="login" class="row">
    <h2>Welcome to the Login Page</h2>
    <div class="panel">
        Login
    </div>
    <form  ng-submit="loginVm.login()">
        <fieldset>
            <div>
                <input class="col-lg-12" type="email" name="email"
                       placeholder="email"
                       ng-model="loginVm.credentials.email"
                       required="" />
            </div>
            <div>
                <input class="col-lg-12" type="password" name="password"
                       placeholder="password"
                       ng-model="loginVm.credentials.password"
                       required />
            </div>
            <div>
                <button class="col-lg-12 btn-primary" type="submit">
                    Login
                </button>
            </div>
        </fieldset>
    </form>
</div>
```

```html
<!-- File: angularjs/69/home.html -->
<div id="home">
    <h2>Welcome to the {{ homeVm.title }} Page</h2>
    <h2>The email addres is {{ homeVm.email}} </h2>

    <div class="alert-info">
        {{ homeVm.message }}
    </div>
    <div>
        <img class="col-lg-6" src="/angularjs/69/images/Camping-Tent.jpg"
             shows-message-when-hovered
             message="I'm the first message">
    </div>
    <div>
         <img class="col-lg-6" src="/angularjs/69/images/Camping-site.jpg"
             shows-message-when-hovered
             message="I'm the second message">
    </div>
    <div>
        <button  class="col-lg-12 btn-primary" ng-click="homeVm.logout()">
            Log Out
        </button>
    </div>
</div>
```


```html
<!-- File: angularjs/69/books.html -->
<ul class="books">
    <li class="panel" ng-repeat="book in booksVm.books">
        {{ book.title }} by {{ book.author }}
    </li>
</ul>
```
