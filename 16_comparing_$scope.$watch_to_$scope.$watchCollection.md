##Comparing $scope.$watch to $scope.$watchCollection
* watching properties and objects with $scope.$watch
* watching objects with $scope.$watchCollection
* use $scope.$watchCollection() over $scope.$watch() for better performance with objects

https://egghead.io/lessons/angularjs-using-the-angularjs-scope-s-watchcollection-method

```html
<!DOCTYPE html>
<!-- anuglarjs/16/index.php -->
<html ng-app="app">
    <meta charset="UTF-8">
    <title>$watch</title>
    <script src="/bower_components/angular/angular.min.js"></script>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <style type="text/css">
        .panel {
            width: 70%;
            margin: 30px auto;
        }
    </style>
</head>
<body ng-controller="WatchController as watchVm">
    <div class="panel panel-primary">
        <div class="panel-heading">
            <h3 class="panel-title">Create Account</h3>
        </div>
        <div class="panel-body">

            <form role="form">
                <div class="form-group">
                    <label for="eml">Email address</label>
                    <input id="eml"
                           type="text"
                           class="form-control"
                           placeholder="Email address"
                           ng-model="watchVm.user.email">
                </div>
                <div class="form-group">
                    <label for="pwd">Password</label>
                    <input id="pwd"
                           type="password"
                           class="form-control"
                           placeholder="Password"
                           ng-model="watchVm.user.password">
                </div>
                <button type="submit" class="btn btn-default">Submit</button>
            </form>

        </div>
    </div>
    <div class="panel panel-danger" ng-show="showReqs">
        <div class="panel-heading">
            <h3 class="panel-title">Password Requirements</h3>
        </div>
        <div class="panel-body">
            <ul>
                <li ng-repeat="req in reqs">{{ req }}</li>
            </ul>
        </div>
    </div>
    <script src="/angularjs/16/app.js"></script>
</body>
</html>
```


Version 1 uses $scope.$watch to monitor for changes to the 'watchVm.user.password' property.

Version 2 watches for the creation of a new 'watchVm.user' object.  This will mean that it will only
watch the initial creation of the 'watchVm.user' object, but will not be able to watch any changes on
the properties of that object.

Version 3 has set the object equality property of the $scope.$watch method to true.  This will now watch
the 'watchVm.user' object for changes on its properties.  It will do this by serializing the object and
comparing the object as a complete serialized value.  In order to do this, angularJs needs to copy the
object and compare the serialized values of the original object with the original property values and the
current object with the new modified property values.  This is an expensive process.

Version 4 uses the $scope.$watchCollection() method and this watches the properties on the object and
does not require angularjs to copy the object.


```javascript
// angularjs/16/app.js
(function(){
    'use strict';

    angular.module('app', [])
        .controller('WatchController', WatchController);


    /* Version 1: watches the 'password' property on the 'watchVm.user' object
     *
     function WatchController ($scope) {
        $scope.$watch('watchVm.user.password', function (newVal, oldVal) {
            console.log(newVal, oldVal);
        });
     }
     */


    /* Version 2: watches for the creation of a new 'watchVm.user' object
     *
     function WatchController ($scope) {
        $scope.$watch('watchVm.user', function (newVal, oldVal) {
            console.log(newVal, oldVal);
        });
     }
     */

    /* Version 3: watches for changes on the 'watchVm.user' object
     *
    function WatchController ($scope) {
        $scope.$watch('watchVm.user', function (newVal, oldVal) {
            console.log(newVal, oldVal);
        },true);
    }
    */

    //Version 4: watches for changes on the properties of the 'watchVm.user' object
    function WatchController ($scope) {
        $scope.$watchCollection('watchVm.user', function (newVal, oldVal) {
            console.log(newVal, oldVal);
        });
    }
})();
```