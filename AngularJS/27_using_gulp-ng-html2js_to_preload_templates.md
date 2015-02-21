##using gulp-ng-html2js to preload templates
* using `gulp-ng-annotate` to prepare `app.js` for minification
* using `gulp-minify-html` to minify html templates
* using `gulp-concat` to concat all javascript files

https://www.npmjs.com/package/gulp-uglify

https://www.npmjs.com/package/gulp-concat

https://www.npmjs.com/package/gulp-ng-annotate

https://www.npmjs.com/package/gulp-ng-html2js

https://www.npmjs.com/package/gulp-minify-html


Create a package.json file in the public-root folder of the application (can also just create
empty json object)

```json
{
    "file":"package.json"
}
```

```
public> npm install --global gulp   // don't need this if gulp already installed globally
public> npm install --save-dev gulp
public> npm install --save-dev gulp-ng-annotate
public> npm install --save-dev gulp-uglify
public> npm install --save-dev gulp-concat
public> npm install --save-dev gulp-minify-html
public> npm install --save-dev gulp-ng-html2js
```

Our `package.json` file should look like this now.

```json
{
  "file": "package.json",
  "devDependencies": {
     "gulp": "^3.8.11",
     "gulp-concat": "^2.5.1",
     "gulp-minify-html": "^1.0.0",
     "gulp-ng-annotate": "^0.5.2",
     "gulp-ng-html2js": "^0.1.8",
     "gulp-uglify": "^1.1.0"
  }
}
```

Note that the `gulpfile.js` that is shown below will produce the results that we need, but as it has
been created with 4 gulp commands `gulp prepareHtmlTemplates`, `gulp annotate`, `gulp uglifyAppJs` and 
`gulp concatAllJs`, it must also be called in the same order.  For a more advanced version of this, it
would be better to carry out these 4 gulp commands as a single command.  Also, note that the file 
structure that has been created via the gulp commands is not ideal and should be improved upon 
for greater clarity.


```javascript
// angularjs/27/gulpfile.js
var gulp = require('gulp');

var ngHtml2Js = require('gulp-ng-html2js');
var minifyHtml = require('gulp-minify-html');
var concat = require('gulp-concat');
var uglify = require('gulp-uglify');
var ngAnnotate = require('gulp-ng-annotate');

gulp.task('prepareHtmlTemplates', function(){
    return gulp.src('./templates/*.html')
        .pipe(minifyHtml({
            empty: true,
            spare: true,
            quotes: true
        }))
        .pipe(ngHtml2Js({
            moduleName: 'app',
            prefix: '/templates'
        }))
        .pipe(concat('templates.min.js'))
        .pipe(uglify())
        .pipe(gulp.dest('./dist/templates'))
    ;
});

gulp.task('annotate', function(){
    return gulp.src('app.js')
        .pipe(ngAnnotate())
        .pipe(gulp.dest('dist/annotate'))
});

gulp.task('uglifyAppJs', function(){
    return gulp.src('dist/annotate/app.js')
        .pipe(uglify())
        .pipe(gulp.dest('./dist/js'))
    ;
});

gulp.task('concatAllJs', function(){
    return gulp.src(['./dist/js/app.js','./dist/templates/templates.min.js'])
        .pipe(concat('all.js'))
        .pipe(gulp.dest('./dist/all'));
});
```

```html
<!DOCTYPE html>
<!-- angularjs/27/index.php -->
<html ng-app="app">
<head>
    <title>ui-router states and directives</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/angular-ui-router/release/angular-ui-router.min.js"></script>
    <script src="/angularjs/27/dist/all/all.js"></script>
</head>
<body>

    <div class="container" style="border: solid; border-color: red;">
        <h4>This is the 'container'</h4>
        <app-header></app-header>

        <div style="border: solid; border-color: #0000FF">
            <h4>This is the 'ui-view' which loads the 'home.html' template</h4>
            <ui-view></ui-view>
        </div>
    </div>
</body>
</html>
```

```javascript
// angularjs/27/app.js
(function(){
    'use strict';

    angular.module('app',['ui.router'])
        .config(config)
        .controller('HomeController',HomeController)
        .directive('appHeader',AppHeaderDirective)
        .controller('AppHeaderController',AppHeaderController)
    ;

    function config($stateProvider){
        $stateProvider
            .state('home', {
                url: '',
                controller: 'HomeController as homeVm',
                templateUrl: '/angularjs/26/templates/home.html'
            })
        ;
    }
    config.$inject = ["$stateProvider"];

    function AppHeaderDirective(){
        return {
            controller: 'AppHeaderController as appHeaderVm',
            templateUrl: '/angularjs/26/templates/appHeader.html'
        }
    }

    function HomeController(){
        var vm = this;
        vm.content = 'This content is from the HomeController';
    }

    function AppHeaderController(){
        var vm = this;
        vm.content = 'Header content from the AppHeaderController';
    }
})();
```

```html
<!-- angularjs/27/templates/home.html -->
<app-header></app-header>
<div>{{ homeVm.content }}</div>
```

```html
<!-- angularjs/27/templates/appHeader.html -->
<h2>{{ appHeaderVm.content }}</h2>
```








