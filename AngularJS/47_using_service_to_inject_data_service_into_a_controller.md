##Using service to inject data service into a controller
* The service definition is a JavaScript class function
* The service does not return anything

```html
<!DOCTYPE html>
<!-- angularjs/47/index.php -->
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
    <script src="/angularjs/47/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/47/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
        .controller('SubController',SubController)
        .service('ItemService',ItemService)
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

        this.list = function(){
            return items;
        };

        this.add = function(item){
            items.push(item);
        };
    }
})();
```