##tracking ng-repeat elements with 'track by id'
* optimize DOM manipulation with `track by id`
* ensuring that AngularJS reuses objects that have the same id

ref: AngularJS Up & Running

We can use the `track by id` expression to optimize how we manipuulate the DOM.
We can use the id of objects returned form the database to ensure that AngularJS
reuses DOM elements even if we fetch the data multiple times from the server.


```
<!DOCTYPE html>
<!-- angularjs/34/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>
<body ng-controller="MainController as mainVm">
    <button ng-click="mainVm.changeNotes()">Change Notes</button>
    <br/>
    DOM Elements change with every click
    <div ng-repeat="note in mainVm.notes1">
        <strong>{{ note.$$hashKey }}</strong>
        <span class="label"> {{ note.label }}</span>
        <span class="label"> {{ note.done }}</span>
    </div>

    <hr/>
    DOM Elements are reused with every click
    <div ng-repeat="note in mainVm.notes2 track by note.id">
        <strong>{{ note.$$hashKey }}</strong>
        <span class="label">{{ note.label }}</span>
        <span class="label">{{ note.done }}</span>
    </div>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/34/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/34/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        var notes = [
            {
                id: 1,
                label: 'First Note',
                done: false,
                someRandom: 31232
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

        vm.notes1 = angular.copy(notes);
        vm.notes2 = angular.copy(notes);


        vm.changeNotes = function(){
            notes = [
                {
                    id: 1,
                    label: 'Changed Note',
                    done: false,
                    someRandom: 44444
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

            vm.notes1 = angular.copy(notes);
            vm.notes2 = angular.copy(notes);
        };
    }
})();
```

```
OUTPUT >>>

<Change Notes>

DOM Elements change with every click
object:3 First Note false
object:4 Second Note false
object:5 Third Note true

DOM Elements are reused with every click
First Note false
Second Note false
Third Note true
```