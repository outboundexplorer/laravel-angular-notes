##using directives within ui-view
* similarities between ui-router and directives
* displaying controller information in ui-view
* displaying controller information in directive

https://egghead.io/lessons/angularjs-consistency-between-ui-router-states-and-angular-directives


```html
<!DOCTYPE html>
<!-- angularjs/26/index.php -->
<html ng-app="app">
<head>
    <title>ui-router states and directives</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/angular-ui-router/release/angular-ui-router.min.js"></script>
    <script src="/angularjs/26/app.js"></script>
</head>
<body>

    <div class="container" style="border: solid; border-color: red;">
        <h4>This is the 'container'</h4>
        <app-header></app-header>

        <div style="border: solid; border-color: #0000FF">
            <h4>This is the 'ui-view' which loads the 'home.html' template</h4>
            <ui-view></ui-view>
        </div>
    </div>
</body>
</html>
```

```javascript
// angularjs/26/app.js
(function(){
    'use strict';

    angular.module('app',['ui.router'])
        .config(config)
        .controller('HomeController',HomeController)
        .directive('appHeader',AppHeaderDirective)
        .controller('AppHeaderController',AppHeaderController)
    ;

    function config($stateProvider){
        $stateProvider
            .state('home', {
                url: '',
                controller: 'HomeController as homeVm',
                templateUrl: '/angularjs/26/templates/home.html'
            })
        ;
    }

    function AppHeaderDirective(){
        return {
            controller: 'AppHeaderController as appHeaderVm',
            templateUrl: '/angularjs/26/templates/appHeader.html'
        }
    }

    function HomeController(){
        var vm = this;
        vm.content = 'This content is from the HomeController';
    }

    function AppHeaderController(){
        var vm = this;
        vm.content = 'Header content from the AppHeaderController';
    }
})();
```

```html
<!-- angularjs/26/templates/home.html -->
<app-header></app-header>
<div>{{ homeVm.content }}</div>
```

```html
<!-- angularjs/26/templates/appHeader.html -->
<h2>{{ appHeaderVm.content }}</h2>
```
