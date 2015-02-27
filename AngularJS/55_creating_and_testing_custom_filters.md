##creating and testing custom filters
* injecting filters into unit tests
* Display for time since event using `new Date().getTime()`
* checking time `diff` and conditionally displaing `minutes/hours/days...`


source: AngularJs Up & Running Ch.9

```html
<!DOCTYPE html>
<!-- angularjs/55/index.php -->
<html ng-app="app">
<head>
    <title>Custom Filters in Action</title>
</head>

<body ng-controller="MainController as mainVm">

    <div>
        Start Time (Timestamp): {{ mainVm.startTime }}
    </div>
    <div>
        Start Time (DateTime): {{ mainVm.startTime | date:'medium' }}
    </div>
    <div>
        Start Time (Our Filter): {{ mainVm.startTime | timeAgo }}
    </div>
    <div>
        someTimeAgo: {{ mainVm.someTimeAgo | date:'medium' }}
    </div>
    <div>
        someTimeAgo (Our Filter): {{ mainVm.someTimeAgo | timeAgo }}
    </div>

    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/55/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/55/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
        .filter('timeAgo',timeAgoFilter)
    ;

    function MainController(){
        var vm = this;

        vm.startTime = new Date().getTime();
        vm.someTimeAgo = new Date().getTime() - (1000*60*60*24);
    }

    function timeAgoFilter(){
        var ONE_MINUTE = 1000 * 60;
        var ONE_HOUR = ONE_MINUTE * 60;
        var ONE_DAY = ONE_HOUR * 24;
        var ONE_MONTH = ONE_DAY * 30;

        return function(ts)
        {
            var currentTime = new Date().getTime();
            var diff = currentTime - ts;
            if (diff < ONE_MINUTE) {
                return 'just now';
            } else if (diff < ONE_HOUR){
                return Math.round(diff/(1000*60))+ ' minute(s) ago';
            } else if (diff < ONE_DAY){
                return Math.round(diff/(1000*60*60)) + ' hour(s) ago';
            } else if (diff < ONE_MONTH) {
                return Math.round(diff/(1000*60*60*24)) + ' day(s) ago';
            } else {
                return Math.round(diff/(1000*60*60*24*30)) + ' month(s) ago';
            }
        }
    }
})();
```


```javascript
// Karma configuration
// Generated on Fri Feb 27 2015 09:18:38 GMT+0000 (GMT Standard Time)

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
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/55/app.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/55/test/*.spec.js',

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


```javascript
// angularjs/55/test/timeAgoFilter.spec.js
describe('timeAgo Filter', function(){
    beforeEach(module('app'));

    var filter;

    beforeEach(inject(function(timeAgoFilter){
        filter = timeAgoFilter;
    }));

    it('should respond based on timestamp', function(){

        // We should really have injected dateProvider into the timeAgo filter
        var currentTime= new Date().getTime();

        currentTime -=10000;
        expect(filter(currentTime)).toEqual('just now');

        var fewMinutesAgo = currentTime - 1000 * 60;
        expect(filter(fewMinutesAgo))
            .toEqual(Math.round((1000*60)/(1000*60)) + ' minute(s) ago');

        var fewHoursAgo = currentTime - 1000 * 60 * 60 * 10;
        expect(filter(fewHoursAgo))
            .toEqual(Math.round((1000*60*60*10)/(1000*60*60)) + ' hour(s) ago');

        var fewDaysAgo = currentTime - 1000 * 60 * 60 * 24 * 4;
        expect(filter(fewDaysAgo))
            .toEqual(Math.round((1000*60*60*24*4)/(1000*60*60*24)) + ' day(s) ago');

        var fewMonthsAgo = currentTime - 1000 * 60 * 60 * 24 * 30 * 15;
        expect(filter(fewMonthsAgo))
            .toEqual(Math.round((1000*60*60*24*30*15)/(1000*60*60*24*30)) + ' month(s) ago');
    })
});
```