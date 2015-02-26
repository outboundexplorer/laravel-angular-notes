##using a DataService to expose $http errors in the UI
* returning different kinds of `$http errors` to the controller
* testing `$http` with `mockBackend` and `expectGET()`

```html
<!DOCTYPE html>
<!-- angularjs/54a/index.php -->
<html ng-app="app">
<head>
    <title>Products!!</title>
    <style>
        .product {
            padding: 10px;
        }
    </style>
</head>

<body ng-controller="MainController as mainVm">

    <div ng-show="mainVm.message">This is the server message: {{ mainVm.message }}</div>

    <div>
        <h2>Add a new product</h2>
        <label>Name</label>
        <input type="text" ng-model="mainVm.newProduct.name">
        <label>Category</label>
        <input type="text" ng-model="mainVm.newProduct.category">
        <label>Price</label>
        <input type="text" ng-model="mainVm.newProduct.price">
        <button ng-click="mainVm.addProduct(mainVm.newProduct)">Add Product</button>
    </div>
    <hr/>
    <h1>Product List</h1>

    <div ng-repeat="product in mainVm.products" class="product">
        <div>
            {{ product.name }}
        </div>
    </div>


    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/54/app.js"></script>
</body>
</html>
```

```javascript
// angularjs/54/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .controller('MainController',MainController)
        .factory('MyLoggingInterceptor',MyLoggingInterceptor)
        .factory('DataService',DataServiceFactory)
        .config(config)
    ;

    function config($httpProvider){
        $httpProvider.interceptors.push('MyLoggingInterceptor');
    }

    function DataServiceFactory($http, $q){

        var service = {
            products: [],
            getProducts: getProducts,
            addProduct: addProduct,
            message: '',
            newProduct: {}
        };

        return service;


        // Version 2
        //
        function getProducts() {
            return $http
                .get('http://testlab70/products')
                .success(function(data, status, headers, config) {
                    service.products = data;
                })

                // Version 1 (handling error logic in DataService
                //
                .error(function(data, status, headers, config){
                    if (status === 404){
                        service.message = '404: Not Found';
                    }
                })

                /*
                // Version 2 (no action taken in DataService)
                //
                .error(function(){
                })
                */
            ;
        }

        // Version 2
        //
        function addProduct(){
            return $http
                .post('http://testlab70/products',service.newProduct)
                .success(function(data){
                    service.products.push(service.newProduct); // added
                })
                .error(function(data, status, headers, config){
                    if (status === 500){
                        service.message = '500: Internal Server Error';
                    }
                })
            ;
        }
    }

    function MainController(DataService) {
        var vm = this;
        vm.products = [];
        vm.newProduct = {};
        vm.message = '';

        // Version 2
        //
        vm.getProducts = function(){
            DataService.getProducts()
                .success(function(products){
                    vm.products = products;
                })

                // Version 1 (no logic carried out in controller)
                //
                .error(function(){
                    vm.message = DataService.message;
                })

                /*
                // Version 2 (logic carried out in controller)
                //
                .error(function(data, status, headers,config){
                    if (status === 404){
                        vm.message = '404: Not Found'
                    }
                })
                */
            ;
        };

        // Version 2
        //
        vm.addProduct = function(newProduct){
            DataService.newProduct = newProduct;
            DataService.addProduct()
                .success(function(data){
                    vm.message = data;
                    vm.newProduct = {};
                })
                .error(function(){
                    vm.message = DataService.message;
                })
            ;
        };

        vm.getProducts();
    }

    function MyLoggingInterceptor($q){
        return {
            request: function(config){
                console.log('Request made with :', config);
                return config;
            },
            requestError: function(rejection){
                console.log('Request error due to :', rejection);
                return $q.reject(rejection);
            },
            response: function(response){
                console.log('Response from server :', response);
                return response || $q.when(response);
            },
            responseError: function(rejection){
                console.log('Error in response :', rejection);
                return $q.reject(rejection);
            }
        };
    }
})();
```

```javascript
// angularjs/54/karma.conf.js
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
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/54/app.js',
        'C:/Users/Andy/CodeLab/Sync/Projects/testlab70/public/angularjs/54/test/*.spec.js',
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
// angularjs/54/test/MainController.spec.js
describe('MainController server calls', function(){

    // load our main module
    beforeEach(module('app'));

    var controller;
    var mockBackend;

    beforeEach(inject(function($controller, $httpBackend){

        mockBackend = $httpBackend;
        mockBackend.expectGET('http://testlab70/products')
            .respond([
                {   name: 'apple',
                    category: 'fruit',
                    id: 1,
                    price: 100,
                    created_at: "2008-05-02 00:00:00",
                    updated_at: "2011-02-01 00:00:00"
                }]
        );
        controller = $controller('MainController');
    }));

    it('should load items from server', function(){

        // initially before Server responds, products should be empty
        expect(controller.products).toEqual([]);

        // simulate a server response
        mockBackend.flush();

        expect(controller.products).toEqual([
            {   name: 'apple',
                category: 'fruit',
                id: 1,
                price: 100,
                created_at: "2008-05-02 00:00:00",
                updated_at: "2011-02-01 00:00:00"
            }])
        ;
    });

    afterEach(function(){
        // ensure that all expects on the $httpBackend were called
        mockBackend.verifyNoOutstandingExpectation();

        // ensure that all requests to the server have responded using flush()
        mockBackend.verifyNoOutstandingRequest();
    });
});
```



