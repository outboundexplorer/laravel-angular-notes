##simple navigation testing with jasmine
* checking that the `$location.path` is correct

```
...angularjs/51> npm install karma karma-jasmine karma-chrome-launcher
...angularjs/51> karma init
```

`karma init` will create the following `karma.conf.js` file

```javascript
// angularjs/51/karma.conf.js
// Karma configuration
// Generated on Wed Feb 25 2015 10:36:50 GMT+0000 (GMT Standard Time)

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
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/51/app.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/51/test/*.spec.js'
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

Here is the App:

```html
<!DOCTYPE html>
<!-- angularjs/51/index.php -->
<html ng-app="app">
<head>
    <title>Options App</title>
</head>

<body ng-controller="MainController as mainVm">

    <div>
        <h1>HOME</h1>
        <button class="btn-primary" ng-click="mainVm.navigate01()">Navigate to ONE</button>
    </div>
    <div>
        <h1>Page ONE</h1>
    </div>
    <button class="btn-primary" ng-click="mainVm.navigate02()">Navigate From ONE to NEXT</button>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/51/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/51/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
    ;

    function MainController($location) {
        var vm = this;

        vm.navigate01 = function(){
            $location.path('/one');
        }

        vm.navigate02 = function(){
            $location.path('/one/next');
        }
    }
})();
```

Here is the test suite

```javascript
// angularjs/51/test/MainController.spec.js
describe('MainController', function(){
    beforeEach(module('app'));

    var controller;
    var $loc;

    beforeEach(inject(function($controller,$location){
        controller = $controller('MainController');
        $loc = $location;
    }));

    it('should navigate away from the current page', function(){

        // reset $location path for test
        expect($loc.path()).toEqual('');

        // setup starting position for test
        $loc.path('/');

        controller.navigate01();
        expect($loc.path()).toEqual('/one');
    });

    it('should navigate away from the current page', function(){

        // reset $location path for test
        expect($loc.path()).toEqual('');

        // setup starting position for test
        $loc.path('/');

        controller.navigate02();
        expect($loc.path()).toEqual('/one/next');
    });
});
```


```
...angularjs/51> karma start
```

