##more complex directive using transclude
* using `transclude: 'element` to replace the entire element
* passing `transclude` into the link function
* creating a new `container` element to replace the removed element
* appending `child` elements to the `container` element


```html
<!DOCTYPE html>
<!-- File: angularjs/64/index.php -->
<html ng-app="app">
<head>
    <title>Stock Market App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <h3>List of Stocks</h3>
        <div simple-stock-repeat="mainVm.stocks">
            We found {{ stock.name }} at {{ currentIndex }}
        </div>
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/64/app.js"></script>
    <script src="/angularjs/64/directive.js"></script>
</body>
</html>
```

```javascript
//  File: angularjs/64/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController(){
        var vm = this;

        vm.stocks = [
            {name:'First Stock', price: 100, previous: 220},
            {name:'Second Stock', price: 140, previous: 120},
            {name:'Third Stock', price: 110, previous: 110},
            {name:'Fourth Stock', price: 400, previous: 420}
        ];
    }
})();
```


```javascript
//  File: angularjs/64/directive.js
(function(){
    'use strict';

    angular.module('app')
        .directive('simpleStockRepeat',simpleStockRepeatDirective)
    ;

    function simpleStockRepeatDirective(){

        return {
            restrict: 'A',

            // capture and replace the entire element instead of just its content
            transclude: 'element',

            // transclude is passed in as the fifth argument to the link function
            link: function(scope, element, attributes, controller, transclude) {
                var myArray = scope.$eval(attributes.simpleStockRepeat);

                // create a new 'container' element to replace the element that we have cloned
                var container = angular.element('<div class="container"></div>');

                for (var i = 0; i < myArray.length; i++){

                    // create an element 'instance' with a new child
                    var instance = transclude(scope.$new(),
                    function(clonedElement, newScope){

                        // expose custom variables for the `instance`
                        newScope.currentIndex = i;
                        newScope.stock = myArray[i];
                    });

                    // add the instance to the container
                    container.append(instance);
                }

                // element is replaced with a comment so we add our generated content after the comment
                element.after(container);
            }
        };
    }
})();
```