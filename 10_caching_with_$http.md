##caching with $http

https://egghead.io/lessons/angularjs-caching-with-http

* using $cacheFactory to build a custom cache



```html
<!DOCTYPE html>
<!-- anuglarjs/10/index.php -->
<html>
<head>
    <title>Egghead.io</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>

    <script src="/bower_components/lodash/lodash.min.js"></script>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/10/app.js"></script>
    <style>body{padding: 10px}</style>
</head>
<body ng-app="egghead" ng-controller="AppController as appVm">
    <button class="pure-button" ng-click="appVm.loadProducts()">Load People</button>
    <button class="pure-button" ng-click="appVm.clearCache()">Clear Cache</button>
    <ul>
        <li ng-repeat="product in appVm.products">{{ product.name }}</li>
    </ul>
</body>
</html>
```



```javascript
// angularjs/10/app.js
(function(){
    angular.module("egghead", [])
        .factory("productsCache", ProductCacheFactory)
        .controller("AppController", AppController)
    ;

    function ProductCacheFactory ($cacheFactory) {
        return $cacheFactory("products");
    }

    function AppController ($http, productsCache) {
        var vm = this;

        vm.loadProducts = function() {
            $http.get("http://testlab70/products", {cache:productsCache})
                .success(function (data) {
                    vm.products = data;
                })
            ;
        };

        vm.clearCache = function () {
            productsCache.remove("http://testlab70/products")
        }
    }
})();
```