##testing controllers with jasmine
* testing `css class` values

```html
<!DOCTYPE html>
<!-- angularjs/37/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
    <style>
        [ng\:cloak], [ng-cloak], [data-ng-cloak], [x-ng-cloak],
        .ng-cloak, .x-ng-cloak {
            display: none !important;
        }
        .finished {
            background-color: green;
        }
        .unfinished {
            background-color: red;
        }
    </style>
</head>
<body ng-controller="MainController as mainVm">

    <div ng-repeat="note in mainVm.notes"
         ng-class="mainVm.getDoneClass(note.done)">
        <span class="label" ng-cloak>
            {{ note.label }}
        </span>
    </div>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/37/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/37/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;
        vm.notes = [
            {label: 'First Note', done: false},
            {label: 'Second Note', done: false},
            {label: 'Third Note', done: true}
        ];

        vm.getDoneClass = function(status){
            return {
                finished: status,
                unfinished: !status
            };
        };
    }
})();
```

```javascript
// angularjs/37/MainController.spec.js
describe('MainController', function(){

    // Instantiate a new version of my module before each test
    beforeEach(module('app'));

    // create a variable to hold the controller instance
    var controller;

    // Inject our controller
    beforeEach(inject(function($controller){
        controller = $controller('MainController');
    }));

    // First Unit Test
    it('should have notes available on load', function(){
        expect(controller.notes).toEqual([
            {label: 'First Note', done: false},
            {label: 'Second Note', done: false},
            {label: 'Third Note', done: true}
        ]);
    });

    // Second Unit Test
    it('should highlight the background depending on status', function(){
        var note = {label: 'Test Note', done: true};

        var actualClass = controller.getDoneClass(note.done);
        expect(actualClass.finished).toBeTruthy();
        expect(actualClass.unfinished).toBeFalsy();

        note.done = false;
        actualClass = controller.getDoneClass(note.done);
        expect(actualClass.finished).toBeFalsy();
        expect(actualClass.unfinished).toBeTruthy();
    });
});
```