##Adding a text-danger class when textarea limit is close
* showing the number of remaining characters allowed
* disabling the submit button when too many characters have been entered
* returning true from a comparison expression

Source: Web Application Development with AngularJS pg.23

```html
<!DOCTYPE html>
<!-- angularjs/29/index.php -->
<html ng-app="app">
<head>
    <title>Text Area</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/29/app.js"></script>
</head>
<body>
    <div class="container" ng-controller="TextAreaController as textAreaVm">
        <span ng-class="{'text-danger' : textAreaVm.closeToLimitWarning()}">
            Remaining: {{ textAreaVm.remaining() }}
        </span>
        <div class="row">
            <textarea ng-model="textAreaVm.message">
                {{ textAreaVm.message }}
            </textarea>
        </div>
        <div class="row">
            <button ng-disabled="!textAreaVm.hasValidLength()" ng-click="textAreaVm.send()">Send</button>
            <button ng-click="textAreaVm.clear()">Clear</button>
        </div>
    </div>
</body>
</html>
```

```javascript
// angularjs/29/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('TextAreaController', TextAreaController)
    ;

    function TextAreaController(){
        var vm = this;
        vm.message = '';

        // constants
        var MAX_LEN = 90;
        var WARN_THRESHOLD = 20;

        vm.remaining = function(){
            return MAX_LEN - vm.message.length;
        };

        //This code can be refactored as shown below
        /*
        vm.hasValidLength = function(){
            if (vm.remaining() >= 0){
                return true;
            }
        };
        */

        // return true when vm.remaining() >= 0
        vm.hasValidLength = function(){
            return vm.remaining() >= 0;
        };

        // return true when vm.remaining() < WARN_THRESHOLD
        vm.closeToLimitWarning = function(){
            return vm.remaining() < WARN_THRESHOLD;
        }
    }
})();
```