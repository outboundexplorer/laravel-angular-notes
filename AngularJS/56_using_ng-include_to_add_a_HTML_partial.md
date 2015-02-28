##using ng-include to add a HTML partial
* use single quotes when referencing file name directly in HTML
* create a variable when referencing controller variable in HTML



```html
<!DOCTYPE html>
<!-- File: angularjs/56/index.php -->
<html ng-app="app">
<head>
    <title>Stock Market App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <h3>List of Stocks</h3>
        <div ng-repeat="stock in mainVm.stocks">
            <div ng-include="mainVm.stockTemplate">
            </div>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/56/app.js"></script>
</body>
</html>
```

Note that we are using `<div ng-include="mainVm.stockTemplate">` in the `index.php`
file.  We therefore also needed to define the `mainVm.stockTemplate` variable within the
`app.js` script.  Alternatively, we could have included the file location directly within
the HTML by using `<div ng-include="'/anuglarjs/56/stock.html'"></div>`.  Note the 
use of single quotes `''`.

```javascript
//  File: angularjs/56/app.js
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

        vm.stockTemplate = '/angularjs/56/stock.html';

        vm.getChange = function(stock){
            return Math.ceil(((stock.price - stock.previous) / stock.previous) * 100);
        };
    }
})();
```

```html
<!-- File: angularjs/56/stock.html -->
<div class="stock-dash">
    Name:
    <span class="stock-name">
        {{ stock.name }}
    </span>
    Price:
    <span class="stock-price">
        {{ stock.price }}
    </span>
    <span class="stock-change">
        {{ mainVm.getChange(stock) }}%
    </span>
</div>
```


```
OUTPUT >>>

List of Stocks

Name: First Stock Price: 100 -54%
Name: Second Stock Price: 140 17%
Name: Third Stock Price: 110 0%
Name: Fourth Stock Price: 400 -4%
```