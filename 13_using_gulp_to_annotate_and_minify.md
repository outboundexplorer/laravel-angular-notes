##Using gulp to annotate and minify angular scripts

https://www.npmjs.com/package/gulp-ng-annotate
http://blog.carbonfive.com/2014/05/05/roll-your-own-asset-pipeline-with-gulp/
https://medium.com/@dickeyxxx/best-practices-for-building-angular-js-apps-266c1a4a6917

Create a package.json file in the public folder (can also just create empty json object)

```json
{
    "file":"package.json"
}
```

```
public> npm install --global gulp
public> npm install --save-dev gulp
public> npm install --save-dev gulp-ng-annotate
public> npm install --save-dev gulp-uglify
```

Our `package.json` file should look like this now.

```json
{
  "file": "package.json",
  "devDependencies": {
    "gulp": "^3.8.11",
    "gulp-ng-annotate": "^0.5.2",
    "gulp-uglify": "^1.1.0"
  }
}
```

We can now create our `gulpfile.js` and begin performing some automatic actions.  Below is a simple script
that will allow us to call specific actions upon our script.  This needs developing further, but with the
way the simple script is written at the moment, it will allow us to first annotate the javascript file and
then minify it ready for production.

```javascript
// angularjs/13/gulpfile.js
var gulp = require('gulp');

// annotate
var ngAnnotate = require('gulp-ng-annotate');
gulp.task('annotate', function(){
   return gulp.src('app.js')
        .pipe(ngAnnotate())
        .pipe(gulp.dest('dist'))
});

// minify
var uglify = require('gulp-uglify');
gulp.task('minify', function(){
    return gulp.src(['dist/app.js'])
        .pipe(uglify())
        .pipe(gulp.dest('min'))
});
```

We can perform the relevant actions with the following commands (note the commands must be carried out in
the following order - this can easily be improved upon):

```
public>gulp annotate
public>gulp minify
```
