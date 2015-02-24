##injecting a data service to ensure consistency between controllers
* using a `factory` to return data from a service
* persisting data by injecting a service into controllers


```html
<!DOCTYPE html>
<!-- angularjs/46/index.php -->
<html ng-app="app">
<head>
    <title>Service App</title>
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
                    <li ng-repeat="item in subVm.list()">
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
                    <li ng-repeat="item in subVm.list()">
                        {{ item.label }}
                    </li>
                </ul>
                <button ng-click="subVm.add()">Add More Items</button>
            </div>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/46/app.js"></script>
</body>
</html>
```


```javascript
// angularjs/46/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
        .controller('SubController',SubController)
        .factory('ItemService',ItemService)
    ;

    function MainController() {
        var vm = this;

        vm.tab = 'first';
        vm.open = function (tab) {
            vm.tab = tab;
        };
    }

    function SubController(ItemService){
        var vm = this;

        vm.list = function(){
            return ItemService.list();
        };

        vm.add = function(){
            ItemService.add({
                id: vm.list().length+1,
                label: 'Item ' + vm.list().length
            });
        };
    }

    function ItemService(){
        var items = [
            {id:1, label: 'Item 0'},
            {id:2, label: 'Item 1'}
        ];

        return {
            list: function(){
                return items;
            },
            add: function(item){
                items.push(item);
            }
        };
    }
})();
```