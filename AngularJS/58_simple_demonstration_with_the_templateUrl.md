##simple demonstration with the templateUrl
* note on importance of not declaring dependencies twice `angular.module('app',[])`

```html
<!DOCTYPE html>
<!-- File: angularjs/58/index.php -->
<html ng-app="app">
<head>
    <title>Stock Market App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <h3>List of Stocks</h3>
        <div ng-repeat="stock in mainVm.stocks">
            <div stock-widget>
            </div>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/58/app.js"></script>
    <script src="/angularjs/58/directive.js"></script>
</body>
</html>
```

```javascript
//  File: angularjs/58/app.js
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

        vm.getChange = function(stock){
            return Math.ceil(((stock.price - stock.previous) / stock.previous) * 100);
        };
    }
})();
```



```javascript
//  File: angularjs/58/directive.js
(function(){
    'use strict';

    angular.module('app')
        .directive('stockWidget',stockWidgetDirective)
    ;

    function stockWidgetDirective(){

        return {
            templateUrl: '/angularjs/58/stock.html'
        };
    }
})();
```


```html
<!-- File: angularjs/58/stock.html -->
<div class="stock-dash">
    Name:
    <span class="stock-name">
        {{ stock.name }}
    </span>
    Price:
    <span class="stock-price">
        {{ stock.price | currency }}
    </span>
    <span class="stock-change">
        {{ mainVm.getChange(stock) }}%
    </span>
</div>
```

####Important Note:

When using multiple files to declare the different parts of the same module, such as `app.js` and
`directives.js` are both describing parts of the `angular.module('app')`, only one of them can can include
the list of dependencies `angular.module('app',[])`
