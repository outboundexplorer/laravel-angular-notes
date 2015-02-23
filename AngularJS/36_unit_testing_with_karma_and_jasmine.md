##unit testing with karma and jasmine
* globally installing `karma`
* installing `karma`, `karma-jasmine` & `karma-chrome-launcher` locally
* configuring the `karma.conf.js` file
* writing a simple controller test
* testing `controller as` with jasmine

Source: AngularJS Up & Running

####Setup procedure
```
...\testlab70>npm install -g karma-cli

...\testlab70\public\angularjs\36>npm install karma

...\testlab70\public\angularjs\36>npm install karma-jasmine karma-chrome-launcher

...\testlab70\public\angularjs\36>karma init
```

Notes for karma.conf.js
1) The angular.min.js and angular-mocks.js files should be included
before the other files
2) The file path must be the full path and using linux style directory seperators `/`

```javascript
// angularjs/36/karma.conf.js
// Karma configuration
// Generated on Mon Feb 23 2015 05:26:27 GMT+0000 (GMT Standard Time)

module.exports = function(config) {
  config.set({

    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',


    // frameworks to use
    // available frameworks: https://npmjs.org/browse/keyword/karma-adapter
    frameworks: ['jasmine'],


    // list of files / patterns to load in the browser
    files: [
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/bower_components/angular/angular.min.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/bower_components/angular-mocks/angular-mocks.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/36/app.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/36/test/*.spec.js'
    ],


    // list of files to exclude
    exclude: [
    ],


    // preprocess matching files before serving them to the browser
    // available preprocessors: https://npmjs.org/browse/keyword/karma-preprocessor
    preprocessors: {
    },


    // test results reporter to use
    // possible values: 'dots', 'progress'
    // available reporters: https://npmjs.org/browse/keyword/karma-reporter
    reporters: ['progress'],


    // web server port
    port: 9876,


    // enable / disable colors in the output (reporters and logs)
    colors: true,


    // level of logging
    // possible values: config.LOG_DISABLE || config.LOG_ERROR || config.LOG_WARN || config.LOG_INFO || config.LOG_DEBUG
    logLevel: config.LOG_INFO,


    // enable / disable watching file and executing tests whenever any file changes
    autoWatch: true,


    // start these browsers
    // available browser launchers: https://npmjs.org/browse/keyword/karma-launcher
    browsers: ['Chrome'],


    // Continuous Integration mode
    // if true, Karma captures browsers, runs the tests and exits
    singleRun: false
  });
};
```



```html
<!DOCTYPE html>
<!-- angularjs/36/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>
<body ng-controller="MainController">

    <div>{{ value }}</div>
    <button ng-click="incrementValue()">Increment</button>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/36/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/36/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController($scope) {
        $scope.value = 0;
        $scope.maxValue = 3;

        $scope.incrementValue = function(){
            if ($scope.value < $scope.maxValue){
                $scope.value++;
            } else {
                $scope.value = 0;
            }
        };
    }
})();
```

```javascript
// angularjs/36/test/MainController.spec.js
describe('Controller: MainController', function(){

    var scope;
    var controller;

    // Instantiate a new version of my module before each test
    beforeEach(module('app'));

    // Before each test, instantiate a new instance of the controller
    beforeEach(inject(function($controller,$rootScope){
        scope = $rootScope.$new();
        controller = $controller('MainController',{
            $scope: scope
        });
    }));

    // First Unit Test
    it('should have values available on load', function(){
        expect(scope.value).toBe(0);
        expect(scope.maxValue).toBe(3);
    });

    // Second Unit Test
    it('increments as expected', function(){
        scope.incrementValue();
        expect(scope.value).toBe(1);
        scope.incrementValue();
        expect(scope.value).toBe(2);
        scope.incrementValue();
        expect(scope.value).toBe(3);
        scope.incrementValue();
        expect(scope.value).toBe(0);
    });
});
```


___

If we now modify the above `index.php` and `app.js` files to use the `controller as` syntax.

```html
<!DOCTYPE html>
<!-- angularjs/36/index.php -->
<html ng-app="app">
<head>
    <title>Notes App</title>
</head>
<body ng-controller="MainController as mainVm">

    <div>{{ mainVm.value }}</div>
    <button ng-click="mainVm.incrementValue()">Increment</button>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/36/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/36/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController() {
        var vm = this;

        vm.value = 0;
        vm.maxValue = 3;

        vm.incrementValue = function(){
            if (vm.value < vm.maxValue){
                vm.value++;
            } else {
                vm.value = 0;
            }
        };
    }
})();
```

We now also must adjust the `MainController.spec.js` file.  This first version, requires $rootScope to be
injected.  See the second version below for a much cleaner version.

Version 1:

// angularjs/36/test/MainController.spec.js
describe('Controller: MainController', function(){

    var controller;
    var scope;

    // Instantiate a new version of my module before each test
    beforeEach(module('app'));

    // Before each test, instantiate a new instance of the controller
    beforeEach(inject(function($controller, $rootScope){
        scope = $rootScope.$new();
        controller = $controller('MainController as mainVm',{
            $scope: scope
        });
    }));

    // First Unit Test
    it('should have values available on load', function(){
        expect(scope.mainVm.value).toBe(0);
        expect(scope.mainVm.maxValue).toBe(3);
    });

    // Second Unit Test
    it('increments as expected', function(){
        scope.mainVm.incrementValue();
        expect(scope.mainVm.value).toBe(1);
        scope.mainVm.incrementValue();
        expect(scope.mainVm.value).toBe(2);
        scope.mainVm.incrementValue();
        expect(scope.mainVm.value).toBe(3);
        scope.mainVm.incrementValue();
        expect(scope.mainVm.value).toBe(0);
    });
});
```

Version 2:

```javascript
// angularjs/36/test/MainController.spec.js
describe('Controller: MainController', function(){

    var controller;

    // Instantiate a new version of my module before each test
    beforeEach(module('app'));

    // Before each test, instantiate a new instance of the controller
    beforeEach(inject(function($controller){
        controller = $controller('MainController');
    }));

    // First Unit Test
    it('should have values available on load', function(){
        expect(controller.value).toBe(0);
        expect(controller.maxValue).toBe(3);
    });

    // Second Unit Test
    it('increments as expected', function(){
        controller.incrementValue();
        expect(controller.value).toBe(1);
        controller.incrementValue();
        expect(controller.value).toBe(2);
        controller.incrementValue();
        expect(controller.value).toBe(3);
        controller.incrementValue();
        expect(controller.value).toBe(0);
    });
});
```


