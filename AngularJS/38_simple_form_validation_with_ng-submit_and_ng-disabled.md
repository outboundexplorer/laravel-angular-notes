##simple form validation with ng-submit and ng-disabled
* using `ng-disabled` to prevent form submission
* setting up a form with `ng-submit`

Ref: AngularJS Up & Running

```html
<!DOCTYPE html>
<!-- angularjs/38/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>
<body ng-controller="MainController as mainVm">

    <form ng-submit="mainVm.submit()" name="myForm">
        <input type="text"
               ng-model="mainVm.user.username"
               required
               ng-minlength="4" />
        <input type="password"
               ng-model="mainVm.user.password"
               required />
        <input type="submit"
               value="Submit"
               ng-disabled="myForm.$invalid"
            />
    </form>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/38/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/38/app.js
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

