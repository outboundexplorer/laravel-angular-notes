##dynamically creating drop-down box options with ng-options
* using the `value` to represent the object in options
* `ng-options="country.id for country in mainVm.countries"`



```html
<!DOCTYPE html>
<!-- angularjs/43/index.php -->
<html ng-app="app">
<head>
    <title>Options App</title>
</head>

<body ng-controller="MainController as mainVm">
    <form ng-submit="mainVm.submit()">
        <div>
            <select ng-model="mainVm.selectedCountry"
                    ng-options="country.id for country in mainVm.countries">
                <option value="">-- choose id --</option>
            </select>
            Selected Country ID: {{ mainVm.selectedCountry }}
        </div>

        <div>
            <select ng-model="mainVm.selectedCountry"
                    ng-options="country.label for country in mainVm.countries">
            </select>
            Selected Country: {{ mainVm.selectedCountry }}
        </div>
        <input type="submit" value="Submit">
    </form>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/43/app.js"></script>
</body>
</html>
```


```javascript
// angularjs/43/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.countries = [
            {label: 'USA', id: 1},
            {label: 'India', id: 2},
            {label: 'UK', id: 3}
        ];

        //vm.selectedCountry.id = 2;
        vm.selectedCountry = vm.countries[1];

        vm.submit = function(){
            console.log('selected country ID: ',vm.selectedCountry.id);
            console.log('selected country: ',vm.selectedCountry);
        }
    }
})();
```
