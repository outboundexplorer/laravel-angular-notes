##injecting a global mock service into the controller
* creating an `appMock` module in order to mock a service
* overriding the `DataService` of `app.js` with `app.mocks.js`
* useing `spyOn()` to check how many times the service functions are called


```html
<!DOCTYPE html>
<!-- angularjs/53/index.php -->
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
    <script src="/angularjs/53/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/53/app.js
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
// angularjs/53/karma.conf.js
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
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/53/app.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/53/test/*.spec.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/53/test/*.mocks.js'
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
// angularjs/53/tests/app.mocks.js
(function(){
    'use strict';

    angular.module('appMocks',[])
        .factory('DataService',DataServiceFactory)
    ;

    function DataServiceFactory(){

        var items = [{id: 1, label: 'Mock 1'}];

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
            items.push(item);
        }
    }
})();
```

```javascript
// angularjs/53/test/MainController.spec.js
describe('MainController with global mock', function(){

    // load our main module
    beforeEach(module('app'));

    //override the DataService for testing
    beforeEach(module('appMocks'));

    var controller;

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

describe('MainController with spies', function(){

    // load our main module
    beforeEach(module('app'));

    var controller;
    var dataService;

    // setup the DataService functions so that we can spy on them
    beforeEach(inject(function($controller,DataService){
        spyOn(DataService,'listItems').and.callThrough();
        spyOn(DataService,'addItem').and.callThrough();
        dataService = DataService;
        controller = $controller('MainController');
    }));

    it('should called listItems() only once on load ', function(){
        expect(dataService.listItems).toHaveBeenCalled();
        expect(dataService.listItems.calls.count()).toEqual(1);
    });

    it('should called addItem(item) only once on click', function(){
        var newItem = {id: 2, label: 'Mock 2'};
        controller.addItem(newItem);
        expect(dataService.addItem).toHaveBeenCalled();
        expect(dataService.addItem.calls.count()).toEqual(1);
    });
});
```


