##using ng-messages-include with error message template
* using script tags to create an error message template
* overriding default error message
* reusing ng-messages

https://egghead.io/lessons/angularjs-reusing-and-overriding-ng-messages

```html
<!DOCTYPE html>
<!-- angularjs/18/index.php -->
<html>
<head>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/angular-messages/angular-messages.min.js"></script>
    <script src="/angularjs/17/app.js"></script>
    <title>What's new in Angular 1.3</title>
</head>
<body ng-app="app" ng-controller="MainController as mainVm">
    <h1>Angular {{mainVm.angularVersion}}</h1>
    <hr />

    <form name="myForm">
        <input type="email" name="myField01" ng-model="mainVm.field01" required minlength="5" />
        <div ng-messages="myForm.myField01.$error" ng-messages-include="my-messages" ng-messages-multiple>
        </div>
        <hr/>
        <input type="email" name="myField02" ng-model="mainVm.field02" required minlength="5" />
        <div ng-messages="myForm.myField02.$error" ng-messages-include="my-messages">
            <div ng-message="required">Your email is required</div>
        </div>
        <button type="submit">Submit</button>
    </form>
        {{ mainVm.field01 }}
        <br/>
        {{ mainVm.field02 }}

    <script type="text/ng-template" id="my-messages">
        <div ng-message="required">This field is required</div>
        <div ng-message="minlength">Input too short</div>
        <div ng-message="email">This field must be an email</div>
    </script>
</body>
</html>
```

```javascript
// angularjs/18/app.js
(function(){
    'use strict';

    angular.module('app', ['ngMessages'])
        .controller('MainController',MainController)
    ;

    function MainController() {
        var vm = this;
        vm.angularVersion = angular.version.full;
    }
})();
```


___

related notes:
* [using ng-modules to display $error messages](https://github.com/outboundexplorer/laravel-angular-notes/blob/master/AngularJS/17_using_ng-messages_to_display_%24error_messages.md) 
