##using ng-switch to switch content
* using `ng-switch-when` to define target content
* using `ng-switch-default` to define default content



```html
<!DOCTYPE html>
<!-- File: angularjs/57/index.php -->
<html ng-app="app">
<head>
    <title>App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <h3>Conditional Elements in HTML</h3>
        <button ng-click="mainVm.currentTab = 'tab1'">
            Tab 1
        </button>
        <button ng-click="mainVm.currentTab = 'tab2'">
            Tab 2
        </button>
        <button ng-click="mainVm.currentTab = 'tab3'">
            Tab 3
        </button>
        <button ng-click="mainVm.currentTab = ''">
            Default
        </button>

        <div ng-switch on="mainVm.currentTab">
            <div ng-switch-default>
                Tab 1 is selected
            </div>
            <div ng-switch-when="tab2">
                Tab 2 is selected
            </div>
            <div ng-switch-when="tab3">
                Tab 3 is selected
            </div>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/57/app.js"></script>
</body>
</html>
```

```javascript
//  File: angularjs/57/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.currentTab = '';
    }
})();
```