##simple directive and transclusion demo
* using `<span ng-transclude></span>` to define position


```html
<!DOCTYPE html>
<!-- File: angularjs/63/index.php -->
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
                (recommended stock)
            </div>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/63/app.js"></script>
    <script src="/angularjs/63/directive.js"></script>
</body>
</html>
```


```javascript
//  File: angularjs/63/app.js
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
//  File: angularjs/63/directive.js
(function(){
    'use strict';

    angular.module('app')
        .directive('stockWidget',stockWidgetDirective)
    ;

    function stockWidgetDirective(){

        return {
            templateUrl: '/angularjs/63/stock.html',
            restrict: 'A',
            transclude: true,
            scope: {
                stockData: '='
            },
            link: function(scope, element, attributes) {
                scope.getChange = function (stock) {
                    return Math.ceil(((stock.price - stock.previous) / stock.previous) * 100);
                };
            }
        };
    }
})();
```

```html
<!-- File: angularjs/63/stock.html -->
<div class="stock-dash">
    <span ng-transclude></span>
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
</div>
```


```
OUTPUT >>>

List of Stocks

(recommended stock) Name: First Stock Price: $100.00 -54%
(recommended stock) Name: Second Stock Price: $140.00 17%
(recommended stock) Name: Third Stock Price: $110.00 0%
(recommended stock) Name: Fourth Stock Price: $400.00 -4%
```