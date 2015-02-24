##using ng-true-value to assign a string to a checkbox
* using `ng-true-value` to return a `'YES'`
* using `ng-checked` to return `true` or `false`
* note that we must use `ng-true-value="'YES'"`

```html
<!DOCTYPE html>
<!-- angularjs/42/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>

<body ng-controller="MainController as mainVm">

    <div>
        <h2>What are your favourite sports?</h2>
        <div ng-repeat="sport in mainVm.sports">
            <label ng-bind="sport.label"></label>
            <div>
                With Binding:
                <input type="checkbox"
                       ng-model="sport.selected"
                       ng-true-value="'YES'"
                       ng-false-value="'NO'" />
            </div>
            <div>
                Using ng-checked:
                <input type="checkbox"
                       ng-model="sport.selected"
                       ng-checked="sport.selected == 'YES'" />
            </div>
            <div>
                Current state: {{ sport.selected }}
            </div>
            <hr/>
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/42/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/42/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.sports = [
            {label:'Basketball', selected: 'YES'},
            {label:'Cricket', selected: 'NO'},
            {label:'Football', selected: 'NO'},
            {label:'Tennis', selected: 'YES'}
        ]
    }
})();
```

