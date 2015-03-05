##creating a directive that uses Google Chart API
* passing data into the directive from a factory service
* converting the data format to use with Google Chart API

```html
<!DOCTYPE html>
<!-- File: angularjs/67/index.php -->
<html ng-app="app">
<head>
    <title>Google Chart App</title>
</head>

<body>

    <div ng-controller="MainController as mainVm">
        <div>
            <button ng-click="mainVm.changeData()">
                Change Pie Chart Data
            </button>
        </div>
        <div pie-chart
             chart-data="mainVm.pieChartData"
             chart-config="mainVm.pieChartConfig">
        </div>
    </div>

    <script src="http://www.google.com/jsapi"></script>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/67/app.js"></script>
    <script src="/angularjs/67/googleChartLoader.js"></script>
    <script src="/angularjs/67/pieChart.js"></script>
</body>
</html>
```

```javascript
//  File: angularjs/67/googleChartLoader.js
(function(){
    'use strict';

    angular.module('app')
        .factory('googleChartLoader',googleChartLoaderFactory)
    ;

    function googleChartLoaderFactory($q, $rootScope, $window){

        // create the deferred object
        var deferred = $q.defer();

        // Load Google Charts API asynchronously
        $window.google.load('visualization','1',{
            packages: ['corechart'],
            callback: function(){

                // when loaded, trigger resolve in an $apply as event is outside AngularJS life cycle
                $rootScope.$apply(function(){
                    deferred.resolve();
                });
            }
        });

        // return the promise object for the directive to chain onto
        return deferred.promise;
    }
})();
```

```javascript
// File: angularjs/67/pieChart.js
(function(){
    'use strict';

    angular.module('app')
        .directive('pieChart', pieChartDirective)
    ;

    function pieChartDirective(googleChartLoader){

        var convertToPieChartDataTableFormat = function(firstColumnName,secondColumnName, data){

            var pieChartArray = [[firstColumnName, secondColumnName]];

            for (var i = 0; i < data.length; i++){
                pieChartArray.push([data[i].label, data[i].value]);
            }

            return google.visualization.arrayToDataTable(pieChartArray);
        };

        return {
            restrict: 'A',
            scope: {
                chartData: '=',
                chartConfig: '='
            },
            link: function($scope, $element){

                googleChartLoader.then(function(){
                    var chart = new google.visualization.PieChart($element[0]);

                    $scope.$watch('chartData', function(newVal, oldVal){
                        var config = $scope.chartConfig;
                        if (newVal) {
                            chart.draw(
                                convertToPieChartDataTableFormat(
                                    config.firstColumnHeader,
                                    config.secondColumnHeader,
                                    newVal),
                                {title: $scope.chartConfig.title});
                        }
                    }, true);
                });
            }
        };
    }
})();
```