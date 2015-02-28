##more advanced linking between parent controller and directive



```html
<!DOCTYPE html>
<!-- File: angularjs/60/index.php -->
<html ng-app="app">
<head>
    <title>Stock Market App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <h3>List of Stocks</h3>
        <div ng-repeat="stock in mainVm.stocks">

            <!-- pass the stock item into the child $scope -->
            <div stock-widget stock-data="stock">
            </div>
        </div>

        <button ng-click="mainVm.changeAllStocks()">
            Change All Stocks From Controller
        </button>
        <button ng-click="mainVm.changeFirstStock()">
            Change First Stock From Controller
        </button>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/60/app.js"></script>
    <script src="/angularjs/60/directive.js"></script>
</body>
</html>
```

```javascript
//  File: angularjs/60/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.stocks = [
            {name:'First Stock', price: 100, previous: 220},
            {name:'Second Stock', price: 140, previous: 120},
            {name:'Third Stock', price: 110, previous: 110},
            {name:'Fourth Stock', price: 400, previous: 420}
        ];

        vm.changeAllStocks = function(){
            for (var i = 0; i < 4; i++){
                vm.stocks[i] = {
                    name: 'Controller Stock',
                    price: 200,
                    previous: 250
                };
            }
        };

        vm.changeFirstStock = function(){
            vm.stocks[0].name = 'Changed First Stock';
        };
    }
})();
```



```javascript
//  File: angularjs/60/directive.js
(function(){
    'use strict';

    angular.module('app')
        .directive('stockWidget',stockWidgetDirective)
    ;

    function stockWidgetDirective(){

        return {
            templateUrl: '/angularjs/60/stock.html',
            restrict: 'A',
            scope: {
                stockData:'='
            },
            link: function($scope, $element, $attributes){
                $scope.getChange = function(stock){
                    return Math.ceil(((stock.price - stock.previous)/ stock.previous)* 100);
                };

                $scope.changeStock = function(){
                    $scope.stockData = {
                        name: 'Directive Stock',
                        price: 500,
                        previous: 200
                    };
                };
            }
        };
    }
})();
```


```html
<!-- File: angularjs/60/stock.html -->
<div class="stock-dash">
    Name:
    <span class="stock-name">
        {{ stockData.name }}
    </span>
    Price:
    <span class="stock-price">
        {{ stockData.price | currency }}
    </span>
    <span class="stock-change">
        {{ getChange(stockData) }}%
    </span>

    <button ng-click="changeStock()">
        Change Stock in Directive
    </button>
</div>
```