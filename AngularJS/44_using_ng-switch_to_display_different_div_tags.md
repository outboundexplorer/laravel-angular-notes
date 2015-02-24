##using ng-switch to display different <div> tags
* using `ng-switch-when` 

Source: AngularJS Up & Running Pg 70

This simple demo shows that by clicking on one controller or the other, we are
able to switch between the two tabs.  Each Tab has its own controller and is 
initialized with 2 items.  As `ng-switch` will actually create and destroy the
<div> elements, this means that there is no persistence of the controller instance.
Therefore, each time we toggle between the two different tabs, any changes
to the data that we make, will not be preserved.


```html
<!DOCTYPE html>
<!-- angularjs/44/index.php -->
<html ng-app="app">
<head>
    <title>Options App</title>
</head>

<body ng-controller="MainController as mainVm">

    <h1>Hello Controllers!</h1>

    <button ng-click="mainVm.open('first')">Open First Tab</button>
    <button ng-click="mainVm.open('second')">Open Second Tab</button>

    <div ng-switch on="mainVm.tab">
        <div ng-switch-when="first">
            <div ng-controller="SubController as subVm">
                <h3>First Tab</h3>
                <ul>
                    <li ng-repeat="item in subVm.list">
                        {{ item.label }}
                    </li>
                </ul>
                <button ng-click="subVm.add()">Add More Items</button>
            </div>
        </div>
        <div ng-switch-when="second">
            <div ng-controller="SubController as subVm">
                <h3>Second Tab</h3>
                <ul>
                    <li ng-repeat="item in subVm.list">
                        {{ item.label }}
                    </li>
                </ul>
                <button ng-click="subVm.add()">Add More Items</button>
            </div>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/44/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/44/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
        .controller('SubController',SubController)
    ;

    function MainController() {
        var vm = this;

        vm.tab = 'first';
        vm.open = function (tab) {
            vm.tab = tab;
        };
    }

    function SubController(){
        var vm = this;

        vm.list = [
            {id:1, label: 'Item 0'},
            {id:2, label: 'Item 1'}
        ];

        vm.add = function(){
            vm.list.push({
                id: vm.list.length+1,
                label: 'Item ' + vm.list.length
            });
        };
    }
})();
```