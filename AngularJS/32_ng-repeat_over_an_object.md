##ng-repeat over an object
* accessing key & value when iterating with ng-repeat

```html
<!DOCTYPE html>
<!-- angularjs/32/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
    <style>
        [ng\:cloak], [ng-cloak], [data-ng-cloak], [x-ng-cloak],
        .ng-cloak, .x-ng-cloak {
            display: none !important;
        }
        .done {
            background-color: green;
        }
        .pending {
            background-color: red;
        }
    </style>
</head>
<body ng-controller="MainController as mainVm">
    <div ng-repeat="(key,value) in mainVm.notes">
        <div>
            <span class="label" ng-cloak>
                {{ value.label }}
            </span>
            <span class="label" ng-bing="value.id">
            </span>
            <span class="label" ng-cloak>
                {{ key }}
            </span>
        </div>
        <div class="label" ng-cloak>
            <strong>{{ value }}</strong>
        </div>
        <hr/>
    </div>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/32/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/32/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;
        vm.notes = {
            albert: {
                id: 1,
                label: 'First Note',
                done: false
            },
            John: {
                id: 2,
                label: 'Second Note',
                done: false
            },
            barry: {
                id: 3,
                label: 'Third Note',
                done: false
            }
        };
    }
})();
```

Note that when output the objects are sorted into alphabetical order with the
uppercase letters taking precedence over lowercase letters.

```
OUTPUT >>> 

Second Note John
{"id":2,"label":"Second Note","done":false}
__________________________________________

First Note albert
{"id":1,"label":"First Note","done":false}
__________________________________________

Third Note barry
{"id":3,"label":"Third Note","done":false}
__________________________________________
```