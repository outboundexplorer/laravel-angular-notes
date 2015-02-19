##using ng-model-options to control binding speed
* using debounce to delay input bindings
* using blur to update bindings on change of element

https://egghead.io/lessons/angularjs-new-in-angular-1-3-ng-model-options-updateon-and-debounce


```html
<!DOCTYPE html>
<!-- angularjs/19/index.php -->
<html>
<head>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/19/app.js"></script>
    <title>What's new in Angular 1.3</title>
</head>
<body ng-app="app" ng-controller="MainController as mainVm">
    <h1>Angular {{mainVm.angularVersion}}</h1>
    <h2>
        ngModelOptions
    </h2>
    <hr />

    <form name="myForm" novalidate>
        <label>
            Some input:
            <input
                type="text"
                name="myField"
                ng-model="mainVm.inputValue"
                ng-model-options="mainVm.modelOptions"
                required />

        </label>
        <button type="submit">Submit</button>
    </form>
    <hr/>
    Bound value: <span ng-bind="mainVm.inputValue"></span> <br />
    Field Error State: <pre>{{myForm.myField.$error | json}}</pre> <br />
    Form Error State: <pre>{{myForm.$error | json}}</pre>
    myForm.$submitted: {{myForm.$submitted}}
</body>
</html>
```

```javascript
// angularjs/19/app.js
(function() {
    'use strict';

    angular.module('app', [])
        .controller('MainController',MainController)
    ;

    function MainController() {
        var vm = this;
        vm.angularVersion = angular.version.full;
        vm.modelOptions = {
            updateOn: 'default blur',
            debounce: {
                default: 1500,
                blur: 0
            }
        };
    }
})();
```