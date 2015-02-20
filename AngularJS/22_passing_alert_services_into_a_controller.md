##Passing alert services into a controller
* using `ng-if` to add an alert element to the DOM
* accessing different alert services within the same controller

https://egghead.io/lessons/angularjs-using-services-in-angular-directives


```html
<!DOCTYPE html>
<!-- angularjs/22/index.php -->
<html ng-app="app">
<head>
    <meta charset="utf-8">
    <title>Alert</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css">
    <script src="/bower_components/angular-ui-bootstrap-bower/ui-bootstrap.min.js"></script>
    <script src="/bower_components/angular-ui-bootstrap-bower/ui-bootstrap-tpls.min.js"></script>
    <script src="/bower_components/angular/angular.js"></script>
    <script src="/angularjs/22/app.js"></script>
</head>
<body  ng-controller="AppController as appVm">

    <alert ng-if="appVm.alertService.showAlert" type="{{ appVm.alertService.alertType }}">
        {{ appVm.alertService.alertMessage}}
    </alert>
    <button ng-click="appVm.failure()">Failure</button>
    <button ng-click="appVm.success()">Success</button>

</body>
</html>
```

```javascript
// angularjs/22/app.js
(function(){
    'use strict';

    angular.module('app',['ui.bootstrap'])
        .controller('AppController', AppController)
        .service('alertService01', AlertService01)
        .service('alertService02', AlertService02)
    ;

    function AlertService01(){
        var alertService = this;

        alertService.alertMessage = "Something failed!!";
        alertService.alertType = 'danger';
        alertService.showAlert = true;
    }

    function AlertService02(){
        var alertService = this;

        alertService.alertMessage = "We have success!!";
        alertService.alertType = 'success';
        alertService.showAlert = true;
    }

    function AppController(alertService01,alertService02){
        var vm = this;

        vm.failure = function(){
            // make the alertService01 available on the controller
            vm.alertService = alertService01;
        };

        vm.success = function(){
            // make the alertService02 available on the controller
            vm.alertService = alertService02;
        };
    }
})();
```

___

Related notes:
* [using a service to communicate between controllers and directives](https://github.com/outboundexplorer/laravel-angular-notes/blob/master/AngularJS/23_using_a_service_to_communicate_between_controllers_and_directives.md)