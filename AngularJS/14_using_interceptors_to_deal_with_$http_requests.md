##Using interceptors to deal with $http requests
* checking for rejection.status and redirecting with $location.url()
* simple demo for intercepting a $http request
* checking for a 403 error
* using $http response to modify the data returned from the server
* using $http request to modify the request sent to the server

https://egghead.io/lessons/angularjs-using-angularjs-interceptors-with-http


```html
<!DOCTYPE html>
<!-- angularjs/14/index.php -->
<html ng-app="app">
<head lang="en">
    <meta charset="UTF-8">
    <title>interceptors</title>
    <script src="/bower_components/angular/angular.min.js"></script>
</head>
<body>
    <script src="/angularjs/14/app.js"></script>
</body>
</html>
```

When our application runs, the config function will be processed prior to the run function.  In the example
below, we have replaced the $http request with a different user.  Note that within each of the object keys,
the function must return the modified/unmodified data.  The response key could be used for modifying the data
that is returned from the server.  In the example we have simply spliced the first 10 objects.

A very practical use is outlined in the responseError key.  In this example, we are effectively listening for
the response from the server and if we receive an error status of 403, then we will redirect the user to the
login page.


```javascript
// angularjs/14/app.js
(function(){
    angular.module('app', [])
        .config(config)
        .run(run)
    ;

    function config ($httpProvider) {
        $httpProvider.interceptors.push(interceptor);
    }

    function run ($http) {
        $http.get('https://api.github.com/users/bclinkinbeard/repos');
    }


    var interceptor = function ($q, $location) {
        return {
            request: function (config) {
                console.log(config);

                // modify the $http request
                config.url =  config.url.replace('bclinkinbeard','joelhooks');
                return config;
            },

            response: function (result) {
                console.log('Repos:');

                // modify the $http response data
                result.data.splice(0, 10).forEach(function (repo) {
                    console.log(repo.name);
                })
                return result;
            },

            responseError: function (rejection) {
                console.log('Failed with', rejection.status, 'status');

                // respond to error status
                if (rejection.status == 403) {
                    $location.url('/login');
                }

                return $q.reject(rejection);
            }
        }
    }
})();
```