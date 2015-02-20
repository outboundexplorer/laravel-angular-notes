##Using ng-aria to improve accessibilty
* directive to show element attributes
* ng-aria module for assisted device readiness

https://egghead.io/lessons/angularjs-using-ng-aria-to-automatically-improve-your-angularjs-accessibility

```html
<!DOCTYPE html>
<!-- angularjs/20/index.php -->
<html>
<head>
    <link rel="stylesheet" href="/angularjs/20/styles.css"/>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/angular-aria/angular-aria.min.js"></script>
    <script src="/angularjs/20/app.js"></script>
    <title>angular-aria</title>
</head>
<body ng-app="app" ng-controller="MainController as mainVm">
    <h1>Angular version: {{mainVm.angularVersion}}</h1>
    <h2>{{mainVm.lessonTitle || 'Angular Lesson'}}</h2>
    <hr />

    <h2>Demo</h2>

    <form ng-init="mainVm.radio='1'">
        Input:<br /><input show-attrs ng-model="mainVm.input" />
        Textarea:<br /><textarea show-attrs ng-model="mainVm.textarea"></textarea>

        Radio:<br/>
        <input name="myRadio" show-attributes type="radio" ng-model="mainVm.radio" value="1" />
        <input name="myRadio" show-attributes type="radio" ng-model="mainVm.radio" value="2" />

        Checkbox:<br/>
        <input show-attributes type="checkbox" ng-model="mainVm.checkbox" />

        Range:<br/>
        <input show-attributes type="range" ng-model="mainVm.range" min="0" max="25" />

        Hide/Show:<br/>
        <input type="checkbox" ng-model="mainVm.show" />
        <input show-attributes ng-show="mainVm.show" ng-model="hidden" />

        Invalid:<br/>
        <input show-attributes type="email" ng-model="mainVm.email" />

        Required:<br/>
        <input show-attributes ng-model="mainVm.required" required />

        Disabled:
        <input type="checkbox" ng-model="mainVm.disable" />
        <br/>
        <button show-attributes type="submit" ng-disabled="mainVm.disable">Submit</button>
    </form>
</body>
</html>
```

```css
/* angularjs/20/styles.css */
form {
    font-weight: bold;
}
pre {
    font-weight: normal;
}
```

```javascript
// angularjs/20/app.js
(function() {
    'use strict';

    angular.module('app', ['ngAria'])
        .controller('MainController',MainController)
        .directive('showAttributes',showAttributesDirective)
    ;

    function MainController() {
        var vm = this;
        vm.angularVersion = angular.version.full;
        vm.lessonTitle = 'How to use the ngAria module';
    }

    function showAttributesDirective() {
        return function (scope, element, attributes) {
            var pre = document.createElement('pre');
            element.after(pre);
            scope.$watch(function () {
                var attributes = {};
                Array.prototype.slice.call(element[0].attributes, 0).forEach(function (item) {
                    if (item.name !== 'show-attributes') {
                        attributes[item.name] = item.value;
                    }
                });
                return attributes;
            }, function (newAttributes, oldAttributes) {
                pre.innerText = JSON.stringify(newAttributes, null, 2);
            }, true);
        }
    }
})();
```
