##ui-router abstract states

https://egghead.io/lessons/angularjs-ui-router-abstract-states



```html
<!DOCTYPE html>
<!-- angularjs/11/index.php -->
<html ng-app="app">
<head>
  <title>Bug Squasher</title>
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css">
  <link rel="stylesheet" href="/angularjs/11/app.css"/>
</head>
<body>

    <div ng-include="'/angularjs/09/navbar/navbar.html'"></div>

    <div ui-view class="container"></div>

    <!-- Vendor JS -->
    <script src="/bower_components/jquery/dist/jquery.min.js"></script>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/bootstrap/dist/js/bootstrap.min.js"></script>
    <script src="/bower_components/angular-ui-router/release/angular-ui-router.min.js"></script>

    <!-- App JS -->
    <script src="/angularjs/11/app.js"></script>
    <script src="/angularjs/11/auth/auth.js"></script>
</body>
</html>
```

```html
<!-- angularjs/11/navbar/navbar.html -->
<nav class="navbar navbar-default container" role="navigation">
    <a class="navbar-brand" href="#">Bug Squasher</a>
    <a ui-sref="sign.in" class="btn btn-default navbar-btn pull-right">Sign in</a>
</nav>
```

```javascript
// angularjs/11/app.js
(function(){
    angular.module('app', ["ui.router","auth"])
        .config(config)
        .run(run)
    ;

    function config($urlRouterProvider){
        $urlRouterProvider.otherwise("/");
    }

    function run($rootScope, $state) {
        $rootScope.$state = $state;
        $rootScope.$on('$stateChangeError', function (event, toState, toParams, fromState, fromParams, error) {
            console.log("StateChangeError:", error)
        });
    }
})();
```


```javascript
// angularjs/11/auth/auth.js
(function(){
    angular.module("auth", [])
        .config(config)
        .service('authService',AuthService)
    ;

    function config ($stateProvider) {
        $stateProvider
            .state('sign', {
                abstract: true,
                url: '/sign',
                template: '<a ui-sref=".in">Sign In</a>' +
                            '<a ui-sref=".up">Sign Up!</a>' +
                            '<ui-view/>',
                controller: function(authService){
                    var vm = this;
                    vm.signIn = function(){
                        authService.signIn();
                    }
                },
                controllerAs: 'authVm',
                resolve: {},
                data: {},
                onEnter: function(){},
                onExit: function(){}
            })
            .state('sign.in', {
                url: '/in',
                template: '<h1>Sign In</h1>' +
                '<button class="btn btn-primary" ng-click="authVm.signIn()">Sign In Now</button>'
            })
            .state('sign.up', {
                url: '/up',
                template: '<h1>Sign Up for a Free Account.</h1>'
            })
        ;
    }

    function AuthService(){

        var model = this;

        model.signIn = function(){

        }


    }
})();
```