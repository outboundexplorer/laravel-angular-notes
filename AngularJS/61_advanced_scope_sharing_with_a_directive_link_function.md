##advanced scope sharing with a directive link function
* using `'='`, `'@'` and `'&'` within isolate scopes



```html
<!DOCTYPE html>
<!-- File: angularjs/61/index.php -->
<html ng-app="app">
<head>
    <title>Stock Market App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <h3>List of Stocks</h3>
        <div ng-repeat="stock in mainVm.stocks">

            <!-- pass the stock item into the child $scope -->
            <div stock-widget
                 stock-data="stock"
                 stock-title="Stock {{$index}}.{{stock.name}}"
                 when-select="mainVm.onStockSelect(stockPrice, stockName)">
            </div>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/61/app.js"></script>
    <script src="/angularjs/61/directive.js"></script>
</body>
</html>
```


```javascript
//  File: angularjs/61/app.js
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

        vm.onStockSelect = function(price,name){
            console.log('Selected Price', price, 'Name', name);
        };
    }
})();
```

```javascript
//  File: angularjs/61/directive.js
(function(){
    'use strict';

    angular.module('app')
        .directive('stockWidget',stockWidgetDirective)
    ;

    function stockWidgetDirective(){

        return {
            templateUrl: '/angularjs/61/stock.html',
            restrict: 'A',
            scope: {
                stockData:'=',
                stockTitle: '@',
                whenSelect: '&'
            },
            link: function(scope, element, attributes){
                scope.getChange = function(stock){
                    return Math.ceil(((stock.price - stock.previous)/ stock.previous)* 100);
                };

                scope.changeStock = function(){
                    scope.stockData = {
                        name: 'Directive Stock',
                        price: 500,
                        previous: 200
                    };
                };

                scope.onSelect = function(){
                    scope.whenSelect({
                        stockName: scope.stockData.name,
                        stockPrice: scope.stockData.price,
                        stockPrevious: scope.stockData.previous
                    });
                };
            }
        };
    }
})();
```

```html
<!-- File: angularjs/61/stock.html -->
<div class="stock-dash">
    Name:
    <span class="stock-name">
        {{ stockTitle }}
    </span>
    Price:
    <span class="stock-price">
        {{ stockData.price | currency }}
    </span>
    <span class="stock-change">
        {{ getChange(stockData) }}%
    </span>

    <button ng-click="onSelect()">
        Select Me
    </button>
</div>
```

