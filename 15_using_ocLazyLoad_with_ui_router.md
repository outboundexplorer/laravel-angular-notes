##Using ocLazyLoad with ui-router
* dynamically loading modules and templates with different ui-router states
* reducing initial page load using ocLazyLoad
* loading modules on demand

https://egghead.io/lessons/angularjs-lazy-loading-modules-with-ui-router-and-oclazyload

```html
<!DOCTYPE html>
<!-- angularjs/15/index.php -->
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title>ocLazyLoad</title>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/angular-ui-router/release/angular-ui-router.min.js"></script>
    <script src="/bower_components/ocLazyLoad/dist/ocLazyLoad.min.js"></script>
    <script src="/angularjs/15/app.js"></script>
</head>
<body ng-app="app" ng-controller="AppController as appVm">
    <button ng-click="appVm.click()">Click me!</button>
    <ui-view></ui-view>
</body>
</html>
```


```javascript
// angularjs/15/app.js
(function(){
    angular.module("app", ["ui.router", "oc.lazyLoad"])
        .config(config)
        .controller('AppController', AppController)
    ;

    function config ($stateProvider) {
        $stateProvider
            .state('store', {
                templateUrl: "/angularjs/15/store/store.html",
                controller: "StoreController as storeVm",
                resolve: {
                    store: function ($ocLazyLoad) {
                        return $ocLazyLoad.load(
                            {
                                name: "store",
                                files: ["/angularjs/15/store/store.js"]
                            }
                        )
                    }
                }
            })
        ;
    }

    function AppController ($state) {
        var vm = this;
        vm.click = function () {
            $state.go("store")
        }
    }
})();
```


```javascript
// angularjs/15/store/store.js
(function(){
    angular.module("store", [])
        .controller("StoreController", StoreController)
    ;

    function StoreController () {
        var vm = this;
        vm.message = "I'm a message from the controller";
    }
})();
```


```html
<!-- angularjs/15/store/store.html -->
<div>
    <h2>I'm the store template</h2>
    <h1>{{ storeVm.message }}</h1>
</div>
```
