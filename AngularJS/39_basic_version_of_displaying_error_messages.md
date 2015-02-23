##basic version of displaying error messages
* using `ng-show` to show element `$error`


```html
<!DOCTYPE html>
<!-- angularjs/39/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>
<body ng-controller="MainController as mainVm">

    <form ng-submit="mainVm.submit()" name="myFormElement">
        <input type="text"
               name="usernameElement"
               ng-model="mainVm.user.username"
               required
               ng-minlength="4" />
        <span ng-show="myFormElement.usernameElement.$error.required">
            This is a required field
        </span>
        <span ng-show="myFormElement.usernameElement.$error.minlength">
            Minimum length required is 4
        </span>
        <span ng-show="myFormElement.usernameElement.$invalid">
            This field is invalid
        </span>
        <br/>
        <input type="password"
               name="passwordElement"
               ng-model="mainVm.user.password"
               required />
        <span ng-show="myFormElement.passwordElement.$error.required">
            This is a required field
        </span>
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
// angularjs/39/app.js
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