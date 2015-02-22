##working with the helper functions in ng-repeat
* accessing the index of the element created in `ng-repeat`
* accessing the even/odd index positioned elements in `ng-repeat`
* accessing the first element of `ng-repeat`

ref: AngularJS Up & Running

```html
<!DOCTYPE html>
<!-- angularjs/33/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>
<body ng-controller="MainController as mainVm">
    <div ng-repeat="note in mainVm.notes">
        <div>First Element: {{ $first }} </div>
        <div>Middle Element: {{ $middle }} </div>
        <div>Last Element: {{ $last }} </div>
        <div>Index of Element: {{ $index }} </div>
        <div>At Even Index Position: {{ $even }} </div>
        <div>At Odd Index Position: {{ $odd }} </div>

        <span class="label" ng-cloak>
            <strong>{{ note.label }}{{ note.done }}</strong>
        </span>
        <hr/>
    </div>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/33/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/33/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;
        vm.notes = [
            {
                id: 1,
                label: 'First Note',
                done: false
            },
            {
                id: 2,
                label: 'Second Note',
                done: false
            },
            {
                id: 3,
                label: 'Third Note',
                done: false
            },
            {
                id: 4,
                label: 'Fourth Note',
                done: false
            }
        ];
    }
})();
```

```
// >>>

First Element: true
Middle Element: false
Last Element: false
Index of Element: 0
At Even Index Position: true
At Odd Index Position: false
First Note false

First Element: false
Middle Element: true
Last Element: false
Index of Element: 1
At Even Index Position: false
At Odd Index Position: true
Second Note false

First Element: false
Middle Element: true
Last Element: false
Index of Element: 2
At Even Index Position: true
At Odd Index Position: false
Third Note false

First Element: false
Middle Element: false
Last Element: true
Index of Element: 3
At Even Index Position: false
At Odd Index Position: true
Fourth Note false
```
