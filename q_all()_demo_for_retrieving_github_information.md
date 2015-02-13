
##$q.all() demo for retrieving all github information
https://egghead.io/lessons/multiple-http-requests-with-q 

* creating a Github service to carry out multiple $http requests

* using $http to retrieve information from Github

* using $q.all with multiple $http requests

* using lodash _.extend() to append an array of objects to an object property

* using lodash _.map() to modify the object properties for a collection of objects



```html
<!DOCTYPE html>
<!-- /angularjs/sandbox/test01.php -->
<html ng-app="multiple-requests">
<head lang="en">
    <meta charset="UTF-8">
    <title>AngularJS Multiple HTTP Requests</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
</head>
<body ng-controller="AppController as appVm">

<div class="container">
    <h2>{{appVm.angularInfo.name}}</h2>
    <div class="row">
        <div class="col-md-2">
            <img ng-src="{{appVm.angularInfo.avatarUrl}}" class="img-responsive"/>
        </div>
        <div class="col-md-10">
            <div class="col-md-6">
                <h3>Repos</h3>
                <div class="list-group">
                    <a ng-repeat="repo in appVm.angularInfo.repos" class="list-group-item">
                        {{repo.name}}
                    </a>
                </div>
            </div>
            <div class="col-md-6">
                <h3>Events</h3>
                <div class="list-group">
                    <a ng-repeat="event in appVm.angularInfo.events" class="list-group-item">
                        {{event.type}}
                    </a>
                </div>
            </div>
        </div>
    </div>
</div>

<script src="/bower_components/lodash/lodash.min.js"></script>
<script src="/bower_components/angular/angular.js"></script>
<script src="/angularjs/07_q_all()_demo_for_retrieving_github_information/app.js"></script>
</body>
</html>
```


```javascript
(function(){

    // angularjs/testbox/app.js
    'use strict';

    angular.module('multiple-requests', [])
        .controller('AppController', AppController)
        .service('Github', GithubService)
    ;

    function AppController(Github) {
        var app = this;

        Github.getAngularInfo()
            .then(function (angularInfo) {
                console.log('angularInfo',angularInfo);
                app.angularInfo = angularInfo;
            })
        ;
    }

    // Github is a data model that allows us to access the github information for a single Github account
    function GithubService($http, $q) {
        var github = this;
        var ANGULAR_USER = 'https://api.github.com/users/angular';
        var ANGULAR_REPOS = 'https://api.github.com/users/angular/repos';
        var ANGULAR_EVENTS = 'https://api.github.com/users/angular/events';

        function getUserData () {
            return $http
                .get(ANGULAR_USER)
                .then(function(responseData) {
                    // console.log('response data: ',responseData);
                    return {
                        name: responseData.data.name,
                        avatarUrl: responseData.data.avatar_url,
                        repoCount: responseData.data.public_repos
                    };
                })
            ;
        };

        // By assigning the getUserData method to the github model, we are now able to test this in isolation as we
        // will now be able to call this method from outside of the Github service.  Note that in the original video
        // the author has left the 01_sliding_animation_with_background_and_avatar as
        // ***github.getUserData = function getUserData()***.  There does not seem to be anything wrong with this,
        //  but so far it does not seem like common practice.  It would seem as though the original author had in
        //  fact forgotten to re-modify this or felt that this was irrelevant.  If we were not concerned with
        // isolated testing for this method, we could have just defined it as a local function by
        // using the standard function declaration, ***function getUserData(){};***
        github.getUserData = function () {
            return $http
                .get(ANGULAR_USER)
                .then(function(responseData) {
                    console.log('responseData: ',responseData)
                    return {
                        name: responseData.data.name,
                        avatarUrl: responseData.data.avatar_url,
                        repoCount: responseData.data.public_repos
                    };
                })
            ;
        };

        github.getUserRepos = function () {
            return $http
                .get(ANGULAR_REPOS)
                .then(function (reposData) {

                    console.log('reposData',reposData);
                    // {data: Array[30], status: 200, headers: function, config: Object, statusText: "OK"}

                    console.log('reposData.data[0]: ',reposData.data[0]);
                    // Object {id: 241, name: "...", full_name: "...", owner: Object, private: false…} (many properties)

                    // The lodash _.map() will iterate through the target collection of objects (reposData.data) and
                    // when used with a function for the second parameter, it will perform that function for each
                    // iteration.  In this case, it passes the data from the current iteration and then returns an
                    // object with the properties mapped to the values as shown below.  Note that in the original
                    // reposData.data, there is a very large number of properties on each object which has been created
                    // by the GitHub API.  When we use the lodash _.map() function, we are only mapping the properties
                    // that we require.
                    var mappedData =  _.map(reposData.data, function (data) {
                        return {
                            name: data.name,
                            description: data.description,
                            stars: data.stargazers_count,
                            forks: data.forks_count,
                            isFork: data.fork
                        }
                    });
                    console.log('mappedData', mappedData);
                    return mappedData;
                })
            ;
        };

        github.getUserEvents = function () {
            return $http
                .get(ANGULAR_EVENTS)
                .then(function (eventsData) {
                    return _.map(eventsData.data, function (data) {
                        return {
                            type: data.type,
                            user: data.actor.login,
                            avatarUrl: data.actor.avatar_url,
                            createdOn: data.created_at,
                            repo: data.repo.name
                        }
                    });
                })
            ;
        };

        github.getAngularInfo = function () {
            var userPromise = github.getUserData();
            console.log('userPromise: ',userPromise); // Promise object
            var userEventsPromise = github.getUserEvents();
            console.log('userEventsPromise', userEventsPromise); // Promise object
            var userReposPromise = github.getUserRepos();
            console.log('userReposPromise',userReposPromise);

            return $q.all([userPromise, userEventsPromise, userReposPromise])
                .then(function(resultArray) {
                    var userData = resultArray[0];
                    var userEventsData = resultArray[1];
                    var userReposData = resultArray[2];

                    console.log('resultArray',resultArray); // [Object, Array[30],Array[30]]
                    console.log('userData',userData); // Object {name: "...", avatarUrl: "...", repoCount: 85}
                    console.log('userEventsData',userEventsData); //Array[30] ~ [Object, Object, Object...]
                    console.log('userReposData',userReposData);// Array[30] ~ [Object, Object, Object....]



                    // The data returned from Github is inconsistent for creating a single object that we can use
                    // to perform useful tasks on within our other parts of the application.  The data is returned
                    // as an object and two arrays of objects.  We can use the lodash function _.extend() to append the
                    // two arrays to become properties on the userData object.
                    var extendedObject = _.extend(userData, {
                        events: userEventsData,
                        repos: userReposData
                    });

                    console.log('extendedObject', extendedObject);
                    //Object {name: "...", avatarUrl: "...", repoCount: 85, events: Array[30], repos: Array[30]}

                    return extendedObject;
            });
        }
    }

})();
```



