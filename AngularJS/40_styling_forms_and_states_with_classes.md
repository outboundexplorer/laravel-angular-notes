##styling forms and states with classes
* using `class="username"` to style css properties


```html
<!DOCTYPE html>
<!-- angularjs/40/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
    <style>
        .username.ng-valid{
            background-color: green;
        }
        .username.ng-dirty.ng-invalid-required {
            background-color: red;
        }
        .username.ng-dirty.ng-invalid-minlength {
            background-color: lightpink;
        }
    </style>
</head>

<body ng-controller="MainController as mainVm">

    <form ng-submit="mainVm.submit()" name="myFormElement">
        <input type="text"
               class="username"
               ng-model="mainVm.user.username"
               required
               ng-minlength="4" />
        <br/>
        <input type="submit"
               value="Submit"
               ng-disabled="myFormElement.$invalid" />
    </form>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/39/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/40/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.submit = function(){
            console.log('User clicked submit with ', vm.user);
        };
    }
})();
```
