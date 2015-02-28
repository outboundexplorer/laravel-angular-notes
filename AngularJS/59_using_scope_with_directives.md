##using scope with directives
* passing the parent scope changes into the directive with `scope: {stockData: '='}`



```html
<!DOCTYPE html>
<!-- File: angularjs/59/index.php -->
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
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/59/app.js"></script>
    <script src="/angularjs/59/directive.js"></script>
</body>
</html>
```

```javascript
//  File: angularjs/59/app.js
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
    }
})();
```


```javascript
//  File: angularjs/59/directive.js
(function(){
    'use strict';

    angular.module('app')
        .directive('stockWidget',stockWidgetDirective)
    ;

    function stockWidgetDirective(){

        return {
            templateUrl: '/angularjs/59/stock.html',
            restrict: 'A',

            // make changes in the parent scope available in the directive
            scope: {
                stockData:'='
            },
            link: function($scope, $element, $attributes){
                $scope.getChange = function(stock){
                    return Math.ceil(((stock.price - stock.previous)/ stock.previous)* 100);
                };
            }
        };
    }
})();
```

```html
<!-- File: angularjs/59/stock.html -->
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
</div>
```