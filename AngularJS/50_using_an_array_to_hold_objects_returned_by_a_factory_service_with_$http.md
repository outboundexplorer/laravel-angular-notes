##using an array to hold objects returned by a factory service with $http
* comparing the use of `$http.****.then()` and `$http.****.success()`
* using a `factory service` to retrieve data from the server
* displaying a message returned from the server
* wrapping `$http` within a `$q` service
* using `$httpProvider.interceptors.push()` to monitor $http requests and responses


source: http://weblog.west-wind.com/posts/2014/Oct/24/AngularJs-and-Promises-with-the-http-Service
source: AngularJS Up & Running Pg101

```html
<!DOCTYPE html>
<!-- angularjs/50/index.php -->
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
    <script src="/angularjs/50/app.js"></script>
</body>
</html>
```


```javascript
// angularjs/50/app.js
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

        /*
        //Version 1
        //
        function getProducts() {
            var deferred = $q.defer();
            $http
                .get('http://testlab70/products')
                .success(function(data) {
                    service.products = data;
                    deferred.resolve(data);
                })
                .error(function(){
                    deferred.reject('Failed to get products');
                });
            return deferred.promise;
        }

        // Version 1
        //
        function addProduct(){
            var deferred = $q.defer();
            $http
                .post('http://testlab70/products',service.newProduct)
                .success(function(data){
                    service.message = data;
                    service.products.push(service.newProduct);
                    deferred.resolve(data);
                    //console.log('service: data message from server', data);
                })
                .error(function(){
                    deferred.reject('Failed to save to database');
                });
            return deferred.promise;
        }
        */

        // Version 2
        //
        function getProducts() {
            return $http
                .get('http://testlab70/products')
                .success(function(data) {
                    service.products = data;
                })
                .error(function(){
                    console.log('Failed to get products');
                })
            ;
        }

        // Version 2
        //
        function addProduct(){
            return $http
                .post('http://testlab70/products',service.newProduct)
                .success(function(data){
                    service.products.push(service.newProduct); // added
                    //console.log('service: data message from server',data);
                })
                .error(function(){
                    //console.log('Failed to add product');
                })
            ;
        }
    }

    function MainController(DataService) {
        var vm = this;
        vm.products = [];
        vm.newProduct = {};
        vm.message = '';

        /*
        // Version 1
        //
        vm.getProducts = function(){
            DataService.getProducts()
                .then(function(products){
                    vm.products = products;
                    //console.log('products returned to controller');
                },
                function(data){
                    //console.log('products retrieval failed')
            })
        };

        // Version 1
        //
        vm.addProduct = function(newProduct){
            DataService.newProduct = newProduct;
            DataService.addProduct()
                .then(function(message){
                    vm.message = message;
                    vm.newProduct = {};
                    //console.log('controller: success data message from server',vm.message);
                },
                function(response){
                    //console.log('products retrieval failed')
                })
            ;
        };
        */

        // Version 2
        //
        vm.getProducts = function(){
            DataService.getProducts()
                .success(function(products){
                    vm.products = products;
                })
                .error(function(){
                    //console.log('products retrieval failed');
                })
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
                    //console.log('controller: success data message from server', data)
                })
                .error(function(){
                    //console.log('product addition failed');
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

```php
// app/routes.php

Route::get('/products', function(){

	$products = DB::table('products')->get();
	return $products;

});


Route::post('/products', function(){

	$time = \Carbon\Carbon::now()->toDateTimeString();

	/*
	 * In this situation we are requiring that the json data must be in the following format
	 *
	 * {"name":"Apples","category":"Fruit","price":22}
	 *
	 */
	$angularData = file_get_contents('php://input');

	$phpObject = json_decode($angularData);

	$data = [
		'name' 		=> 	$phpObject->name,
		'category' 	=>	$phpObject->category,
		'price'		=>	$phpObject->price,
		'created_at' => $time,
		'updated_at' => $time
	];

	DB::table('products')->insert($data);

	return 'hello there you little beauty';

});
```