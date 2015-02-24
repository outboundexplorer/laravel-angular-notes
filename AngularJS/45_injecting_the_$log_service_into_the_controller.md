##injecting the $log service into the controller

```html
<!DOCTYPE html>
<!-- angularjs/45/index.php -->
<html ng-app="app">
<head>
    <title>Options App</title>
</head>

<body ng-controller="MainController as mainVm">

    <h1>Hello Services!</h1>

    <button ng-click="mainVm.logStuff()">Log something</button>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/45/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/45/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController($log) {
        var vm = this;

        vm.logStuff = function(){
            $log.log('The button was pressed');
        };
    }
})();
```