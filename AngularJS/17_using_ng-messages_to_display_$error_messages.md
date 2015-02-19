##using ng-messages to display $error messages
* using ng-messages to display a single $error message
* using ng-messages to display mulitple $error messages
* displaying form $error messages


https://egghead.io/lessons/angularjs-introduction-to-ng-messages-for-angularjs

```html
<!DOCTYPE html>
<!-- angularjs/17/index.php -->
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

    <!-- The Standard version -->
    <!--
    <form name="myForm">
        <input type="email" name="myField" ng-model="field" required minlength="5" />
        <div ng-show="myForm.myField.$error">
            <div ng-show="myForm.myField.$error.required">This field is required</div>
            <div ng-show="myForm.myField.$error.minlength">Input is too short</div>
            <div ng-show="myForm.myField.$error.email">This field must be an email</div>
        </div>
    </form>
    -->

    <!-- Using ng-messages -->
    <form name="myForm">
        <input type="email" name="myField" ng-model="field" required minlength="5" />
        <div ng-messages="myForm.myField.$error" ng-messages-multiple>
            <div ng-message="required">This field is required</div>
            <div ng-message="minlength">Input too short</div>
            <div ng-message="email">This field must be an email</div>
        </div>
    </form>
</body>
</html>
```

```javascript
// angularjs/17/app.js
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


In the standard version, multiple error messages will always be returned without any further logic.  In the
version that uses ng-messages, only one message will be shown at any one time.  If multiple messages are
required, then the div can be changed as follows `<div ng-messages="myForm.myField.$error" ng-messages-multiple>`
