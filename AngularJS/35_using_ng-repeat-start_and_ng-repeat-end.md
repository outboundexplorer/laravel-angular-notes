##Using ng-repeat-start and ng-repeat-end
* using `ng-repeat` when we can't wrap around multiple elements
* using `ng-repeat-start` and `ng-repeat-end` in a `table` element

Ref: AngularJS Up & Running Pg32

Normally, when we use `ng-repeat` we are able to wrap multiple elements within
a `<div>` element (or other element) and this way create a template of the
elements that are repeated with the `ng-repeat` directive.  However, in certain
elements like `<table>`, we are not able to wrap the elements.  Instead we use 
specify the start of the repeat template using `ng-repeat-start` and `ng-repeat-end`.

```html
<!DOCTYPE html>
<!-- angularjs/35/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>
<body ng-controller="MainController as mainVm">

    <table>
        <tr ng-repeat-start="note in mainVm.notes">
            <td>{{ note.label }}</td>
        </tr>
        <tr ng-repeat-end>
            <td>Done: {{ note.done }}</td>
        </tr>
    </table>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/35/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/35/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController() {
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
                done: true
            }
        ];
    }
})();
```



```
>>>

First Note
Done: false
Second Note
Done: false
Third Note
Done: true
```