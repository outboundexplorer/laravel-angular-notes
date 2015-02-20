##using bindToController when using isolated scope
* passing attribute values to the directive controller
* using bindToController in directives

https://egghead.io/lessons/angularjs-new-in-angular-1-3-bindtocontroller 

```html
<!DOCTYPE html>
<!-- angularjs/24/index.php -->
<html ng-app="app">
<head>
    <title>bindToController</title>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/24/app.js"></script>
</head>
<body>
    <note message="hello"></note>
</body>
</html>
```

For this directive we are using a controller instead of a link function.  We are also using the 
`controller as` syntax without $scope. This means that the binding in the template must be on 
`{{noteVm.message}}` in order to have correct output.  The `hello` message is coming from the 
`scope.message` property.  As the `scope` object is part of our returned object from our directive,
this means that we have isolated scope which means that `scope.message != noteVm.message`.  In order
to make these equivalent, we use `bindToController: true`.

```javascript
// angularjs/24/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .directive('note', NoteDirective)
        .controller('NoteController', NoteController)
    ;

    function NoteDirective(){
        return {

            // need a scope in order to pass in the message from the HTML
            scope: {
                // passing in a string so we use the '@' symbol
                message:'@'
            },

            // bind the 'scope' properties to 'noteVm'
            bindToController: true,
            controller: 'NoteController as noteVm',
            template: '<div>{{noteVm.message}}</div>'
        }
    }

    function NoteController(){
        var vm = this;
    }
})();
```