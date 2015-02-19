##Delaying filter response when searching through results

https://egghead.io/lessons/angularjs-debounce-user-input-delay-in-angularjs-with-lodash

* using lodash.debounce() to delay changes on user actions
* showing a "loading..." info message on searching a field
* using angular.bind() with $scope.$watch
* injecting $scope into a controller for use with $scope.$watch


```html
<!DOCTYPE html>
<!-- angularjs/12/index.php -->
<html>
<head>
    <title>Egghead.io</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <script src="/bower_components/lodash/lodash.min.js"></script>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/12/app.js"></script>
</head>
<body ng-app="app" ng-controller="AppController as appVm" class="container">
    <input type="text" ng-model="appVm.searchTerm"/>
    <span>{{ appVm.infoMessage }}</span>

    <table class="table table-bordered">
        <thead>
        <tr>
            <th>#</th>
            <th>Name</th>
            <th>Category</th>
            <th>Price</th>
        </tr>
        </thead>
        <tbody>
        <tr ng-repeat="product in appVm.products | filter: appVm.debounceTerm track by product.id">
            <td>{{ $index + 1 }}</td>
            <td>{{ product.name }}</td>
            <td>{{ product.category }}</td>
            <td>{{ product.price }}</td>
        </tr>
        </tbody>
    </table>
</body>
</html>
```


```javascript
// angularjs/12/app.js
(function(){
    angular.module("app", [])
        .controller("AppController", AppController)
    ;

    function AppController ($http, $scope) {
        var model = this;
        model.infoMessage = "";
        model.searchTerm = "";
        model.debounceTerm = "";

        $http
            .get("//testlab70/products")
            .success(function (data) {
                model.products = data;
            })
        ;

        // This $scope.$watch method will mean that as soon as we start searching, the "Loading..." message
        // will be displayed.  In order to prevent this from being displayed the first time, we check to
        // compare the oldValue and the newValue
        $scope.$watch(angular.bind(this, function(searchTerm){
            return this.searchTerm;
        }), function (newValue, oldValue) {
            if(newValue == oldValue) return;
            model.infoMessage = "Loading...";
        });

        /* Note that in this first version, as we are watching the view, therefore we are watching for changes
         * in "app.searchTerm".  This means that the the controller and the view are tightly bound together.
         *
        $scope.$watch("appVm.searchTerm", function (newValue, oldValue) {
            if(newValue == oldValue) return;
            model.infoMessage = "Loading...";
        });
        */

        $scope.$watch(angular.bind(this, function(searchTerm) {
            return this.searchTerm;
        }), _.debounce(function (newValue, oldValue) {
            model.debounceTerm = newValue;
            model.infoMessage = "";
            $scope.$apply();
        }, 300));

        /*
         * This is the first version that has a tight coupling with the view.
         *
        $scope.$watch("appVm.searchTerm", _.debounce(function (newValue, oldValue) {
            model.debounceTerm = newValue;
            model.infoMessage = "";
            $scope.$apply();
        }, 300));
        */
    }
})();
```

