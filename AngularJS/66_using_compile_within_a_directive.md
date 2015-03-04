##using compile within a directive
* dynamic input validators for a directive
* injecting DOM elements using jqLite
* matching attributes against an `allowedValidationRules array`
* using `require: '^form'` in a directive


```html
<!DOCTYPE html>
<!-- File: angularjs/66/index.php -->
<html ng-app="app">
<head>
    <title>Dynamic Form App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <form novalidate="" name="mainForm">
            <form-element type="text"
                          name="uName"
                          bind-to="mainVm.username"
                          label="Username"
                          required
                          ng-minlength="5">
                <validation key="required">
                    Please enter a username
                </validation>
                <validation key="minlength">
                    Username must be at least 5 characters
                </validation>
            </form-element>

            Username is {{ mainVm.username }}

            <form-element type="password"
                          name="pWord"
                          bind-to="mainVm.password"
                          label="Password"
                          required
                          ng-pattern="/^[a-zA-Z0-9]+$/">
                <validation key="required">
                    Please enter a password
                </validation>
                <validation key="pattern">
                    Password must only be alphanumeric characters
                </validation>
            </form-element>

            Password is {{ mainVm.password }}

            <button>Submit</button>

        </form>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/66/app.js"></script>
    <script src="/angularjs/66/directive.js"></script>
</body>
</html>
```

```javascript
//  File: angularjs/66/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.username = '';
        vm.password = '';
    }
})();
```

```javascript
//  File: angularjs/66/tabs.js
(function(){
    'use strict';

    angular.module('app')
        .directive('formElement',formElementDirective)
    ;

    function formElementDirective(){

        return {
            restrict: 'E',

            // use require to ensure that formElement directive must be used as a child of a <form>
            require: '^form',
            scope: true,

            // compile function executes before scope is available so it does not get injected
            compile: function (element, attributes) {

                // get the name that will be used for the input field from the directive attributes
                var inputName = attributes.name;

                // define our different possible validation options
                var allowedValidationRules = {
                    'required': 'required',
                    'ng-minlength': 'ngMinlength',
                    'ng-pattern': 'ngPattern'
                };

                // get the <validation> tags HTML and store it in validationRules variable
                var validationRules = element.find('validation');
                // console.log('validationKeys',validationKeys);
                // OUTPUT >>> array <validation> tag elements

                // create an object to store the presentValidationRules
                var availableValidationRules = {};


                // loop through each of the found validationRules
                angular.forEach(validationRules, function (validationRule) {

                    // wrap the validationKey element as an angular jq-lite (or jquery) element
                    validationRule = angular.element(validationRule);

                    // store the message and key in the availableValidationRules
                    availableValidationRules[validationRule.attr('key')] = validationRule.text();
                });

                // create 'elementHtml' variable to store the HTML that we want to insert into the DOM
                var elementHtml = '<div><label>' + attributes.label + '</label>';
                elementHtml += '<input type="' + attributes.type + '" name="' + inputName +
                '" ng-model="' + attributes.bindTo + '"';

                // remove 'type' & 'name' attributes from element '<form-element>' as no longer needed
                element.removeAttr('type');
                element.removeAttr('name');

                // loop through allowedValidationRules
                for (var i in allowedValidationRules) {

                    // if validationRule present in allowedValidationRules add to elementHtml
                    if (attributes[allowedValidationRules[i]] !== undefined) {
                        elementHtml += ' ' + i + '="' + attributes[allowedValidationRules[i]] + '"';
                    }

                    // remove 'validationRule' from element '<form-element' as no longer needed/invalid
                    element.removeAttr(i);
                }

                // append close input tag
                elementHtml += '/>';

                // append error messages
                elementHtml += '<span ng-repeat="(key, text) in validators" ' +
                ' ng-show="hasError(key)"' +
                'ng-bind="text"></span>';

                // append close </div> tag
                elementHtml += '</div>';

                // add the constructed 'elementHtml' element to the element '<form-element>'
                element.html(elementHtml);

                // return the postLink function to add validators array and hasError function
                return function (scope, element, attributes, controller) {
                    scope.validators = angular.copy(availableValidationRules);
                    scope.hasError = function (key) {
                        return !!controller[inputName]['$error'][key];
                    };
                };
            }
        };
    }
})();
```