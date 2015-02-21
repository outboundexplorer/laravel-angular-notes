##data binding with {{data.model}} or ng-bind
* using `ng-cloak` on a single element
* include `.ng-cloak` style within `styles.css`
* dynamically switch class using controller function

`ng-cloak` styling is not applied by angular.js until angular.js has finished loading.
Therefore, if we load the angular.js at the very end of our HTML, the style will not be 
immediately applied.  Therefore it is often a good idea to include the `.ng-cloak` styles
as part of our initial css before the HTMl elements have loaded.

```html
<!DOCTYPE html>
<!-- angularjs/31/index.php -->
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

    <div ng-repeat="note in mainVm.notes"
         ng-class="mainVm.getNoteClass(note.done)">
        <span class="label" ng-cloak> {{ note.label }}</span>
        <span class="assignee"
              ng-show="note.assignee"
              ng-bind="note.assignee">
        </span>
    </div>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/31/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/31/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;
        vm.notes = [
            {label: 'First Note', done: false, assignee: 'Pete'},
            {label: 'Second Note', done: false},
            {label: 'Third Note', done: true},
            {label: 'Fourth Note', done: false, assignee: 'John'}
        ];

        vm.getNoteClass = function(status){
            return {
                done: status,
                pending: !status
            };
        };
    }
})();
```
