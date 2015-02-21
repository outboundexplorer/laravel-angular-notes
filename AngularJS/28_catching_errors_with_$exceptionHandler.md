##Catching errors with $exceptionHandler
* catching difficult to catch errors with $exceptionHandler
* getting `$rootScope` of the `$injector`
* using `$injector.get('$rootScope')`
* creating an errors array on `$rootScope`


https://egghead.io/lessons/angularjs-catching-errors-with-exceptionhandler

There are often some kinds of exception which are normally only available within
the console.  By using `$exceptionHandler`, we are able to catch these tricky 
exceptions and then either output them to the UI or do something else with them
such as log them somewhere or send a report email.


```html
<!DOCTYPE html>
<!-- angularjs/28/index.php -->
<html ng-app="app">
<head>
    <title>ui-router states and directives</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/28/app.js"></script>
</head>
<body>
    <ul ng-show="errors && errors.length">
        <li ng-repeat="error in errors">
            {{ error  }}
        </li>
    </ul>
</body>
</html>
```

In the $exceptionHandler we inject `$injector` instead of injecting `$rootScope` directly.
This is because we need to avoid a circle-referencing loop which would be caused by `$rootScope`
referencing `$exceptionHandler` and `$exceptionHandler` referencing `$rootScope`.

Once we have access to the `$rootScope`, we are able to push the `exception.message` to the 
`$rootScope.errors` array.


```javascript
// angularjs/28/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .run(run)
        .factory('$exceptionHandler', $exceptionHandlerFactory)
    ;

    function $exceptionHandlerFactory($injector){
        return function(exception, cause){
            var $rootScope = $injector.get('$rootScope');
            $rootScope.errors = $rootScope.errors || [];
            $rootScope.errors.push(exception.message);
            console.log($rootScope.errors);
        }
    }

    function run($http){

        function onSuccess(result){
            console.log('here is the data!');
            console.log(result.data.length, 'repos found');

            // this method does not exist and will create an error in the console
            result.count();
        }

        function onFailure(info){
            console.log('we got an error');
            console.log(info);
        }

        $http
            .get('https://api.github.com/users/bclinkinbeard/repos')
            .then(onSuccess,onFailure);
    }
})();
```