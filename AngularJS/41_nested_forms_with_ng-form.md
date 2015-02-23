##Nested forms with ng-form
* carrying out validation on a group of inputs with `ng-form`
* placing `<ng-form>` element within `<form>` element

```html
<!DOCTYPE html>
<!-- angularjs/41/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>

<body ng-controller="MainController as mainVm">

    <form ng-submit="mainVm.submit()" novalidate="" name="myForm">
        <h3>Login Details</h3>
        <input type="text"
               class="username"
               name="username"
               ng-model="mainVm.user.username"
               required
               placeholder="Username"
               ng-minlength="4" /><br/>
        <input type="password"
               class="password"
               name="password"
               ng-model="mainVm.user.password"
               required
               placeholder="Password" /><br/>
        <hr/>
        <h3>Profile Details</h3>
        <ng-form name="profile">
            <input type="text"
                   name="firstName"
                   ng-model="mainVm.user.profile.firstName"
                   placeholder="First Name"
                   required="" /><br/>
            <input type="text"
                   name="lastName"
                   ng-model="mainVm.user.profile.lastName"
                   placeholder="Last Name"
                   required="" /><br/>
            <input type="date"
                   name="dateOfBirth"
                   ng-model="mainVm.user.profile.dateOfBirth"
                   placeholder="Date Of Birth" /><br/>
        </ng-form>
        <hr/>

        <span ng-show="myForm.profile.$invalid">
            Please fill out the profile information
        </span>

        <input type="submit"
               value="Submit"
               ng-disabled="myForm.$invalid" />
    </form>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/41/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/41/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.submit = function(){
            console.log(vm.user);
        };
    }
})();
```


