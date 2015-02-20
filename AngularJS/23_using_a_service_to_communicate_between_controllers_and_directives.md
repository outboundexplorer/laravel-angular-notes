##Using a service to communicate between controllers and directives
* injecting a service into a directive controller
* communicating between a directive and a controller
* building a simple `appAlert` directive

https://egghead.io/lessons/angularjs-using-services-in-angular-directives

```html
<!DOCTYPE html>
<!-- angularjs/23/index.php -->
<html ng-app="app">
<head>
    <title>Egghead.io Tutorials</title>
    <link rel="shortcut icon" href="favicon.ico">
    <link href="/bower_components/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet">
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/angular-ui-bootstrap-bower/ui-bootstrap.min.js"></script>
    <script src="/bower_components/angular-ui-bootstrap-bower/ui-bootstrap-tpls.min.js"></script>
    <script src="/angularjs/23/app.js"></script>
</head>
<body>
    <div ng-controller="AppController as appVm">
        <app-alert></app-alert>
        <button class="btn-primary" style="width: 20em; background-color: green;" 
                ng-click="appVm.success()">
            Success
        </button>
    </div>
    <div ng-controller="AnotherController as anotherVm">
        <button class="btn-primary" style="width: 20em; background-color: red;" 
                ng-click="anotherVm.failed()">
            Failed
        </button>
    </div>
    <div>
</body>
</html>
```

When the app initially runs, the `alertService` is injected into the `AppAlertController` of the 
`appAlert` directive.  This creates a two-way binding between the `alertService` and the `appAlert` 
directive.  Therefore, initially, the `This is a warning!!!` message is output.

As the `alertService` has also been injected into the `AppController`, this means that any modifications
to `alertService` that occur within `AppController` will also modify the output of the `appAlert`
directive.

The same is true for `AnotherController` even though this controller is only a sibling to the `AppController`


```javascript
// angularjs/23/app.js
(function(){
    'use strict';

    angular.module("app", ["ui.bootstrap"])
        .service("alertService", AlertService)
        .controller("AppController", AppController)
        .controller("AnotherController", AnotherController)
        .directive('appAlert', AppAlertDirective)
        .controller("AppAlertController", AppAlertController)
    ;

    function AlertService() {
        var alertService = this;

        alertService.alertMessage = "This is a warning!!! (invoked from the initial alertService)";
        alertService.alertType = "warning";
        alertService.showAlert = true;
    }

    function AppController(alertService) {
        var vm = this;
        vm.alertService = alertService;
        vm.success = function () {
            vm.alertService.alertMessage = "Success!!! (invoked from AppController which " +
                "wraps the directive)";
            vm.alertService.alertType = "success";
        };
    }

    function AnotherController(alertService) {
        var vm = this;
        vm.alertService = alertService;
        vm.failed = function () {
            vm.alertService.alertMessage = "Danger!!! (invoked from AnotherController which " +
                "is sibling to AppController)";
            vm.alertService.alertType = "danger";
        };

    }

    function AppAlertDirective() {
        return {
            controller: "AppAlertController as appAlertVm",
            templateUrl: '/angularjs/23/alert.html'
        };
    }

    function AppAlertController(alertService) {
        var vm = this;
        console.log('AppAlertController alertService :',alertService);

        // make the alertService available on the controller
        vm.alertService = alertService;
    }
})();
```


```html
<!-- angularjs/23/alert.html -->
<alert ng-if="appAlertVm.alertService.showAlert"
       type="{{ appAlertVm.alertService.alertType }}">
    {{ appAlertVm.alertService.alertMessage }}
</alert>
```

___

Related notes:
* [passing alert services into a controller](https://github.com/outboundexplorer/laravel-angular-notes/blob/master/AngularJS/22_passing_alert_services_into_a_controller.md)