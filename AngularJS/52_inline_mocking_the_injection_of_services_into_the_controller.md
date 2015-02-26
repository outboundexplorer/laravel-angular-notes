##inline mocking the injection of services into the controller
* wrapping service functions for extra functionality within the controller
* writing a test for `listItems()` with jasmine
* writing a test for `addItem(item)` with jasmine
* creating an inline `mockService`


```html
<!DOCTYPE html>
<!-- angularjs/52/index.php -->
<html ng-app="app">
<head>
    <title>Options App</title>
</head>

<body ng-controller="MainController as mainVm">

    <div ng-repeat="item in mainVm.items">
        {{ item.id }} {{ item.label }}
    </div>

    <div>
        <h3>Add a new item</h3>
        <input type="text" ng-model="mainVm.item.label" />
        <button ng-click="mainVm.addItem(mainVm.item)">Add</button>
    </div>



    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/52/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/52/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
        .factory('DataService',DataServiceFactory)
    ;

    function MainController(DataService) {
        var vm = this;

        // Directly call DataService.listItems() function on initialization
        vm.items = DataService.listItems();

        // Wrap the DataService.addItem(newItem) function to allow for extra functionality
        vm.addItem = function(newItem){
            DataService.addItem(newItem);

            // Clear out object from the input field
            vm.item = {};
        }
    }

    function DataServiceFactory(){
        var items = [
            {id: 1, label: 'Item 0'},
            {id: 2, label: 'Item 1'}
        ];

        var service = {
            item: {},
            listItems: listItems,
            addItem: addItem
        };

        return service;

        function listItems(){
            return items;
        }

        function addItem(item){
            service.item = item;
            service.item.id = items.length + 1;
            items.push(service.item);
        }
    }
})();
```

```javascript
// angularjs/52/karma.conf.js
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
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/52/app.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/52/test/*.spec.js'
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
// angularjs/52/test/MainController.spec.js
describe('MainController with inline mock', function(){
    beforeEach(module('app'));

    var controller;
    var mockService;

    // override the DataService with mockService after 'app' module is loaded
    beforeEach(module(function($provide){
        mockService = {
            item: {},
            items: [{id: 1, label: 'Mock 1'}],
            listItems: function(){
                return mockService.items;
            },
            addItem: function(item) {
               mockService.items.push(item);
            }
        };

        //  when asking for DataService, give mockService
        $provide.value('DataService', mockService);
    }));

    beforeEach(inject(function($controller){
        controller = $controller('MainController');
    }));

    it('should load mocked items', function(){
        expect(controller.items).toEqual([
            {id: 1, label: 'Mock 1'}
        ]);
    });

    it('should add mocked item to data', function(){
        var newItem = {id: 2, label: 'Mock 2'};
        controller.addItem(newItem);
        expect(controller.items).toEqual([
            {id: 1, label: 'Mock 1'},
            {id: 2, label: 'Mock 2'}
        ]);
    })
});
```


