##Adding custom functionality to HTML attributes
* adding functionality to the `src` attribute
* matching URLs with regular expressions
* getting the domain name from a URL
* using lodash `_.template()` method to pass an object into a template

```html
<!DOCTYPE html>
<!-- angualrjs/21/index.php -->
<html>
<head>
    <meta charset="utf-8">
    <title>Hijacking HTML Attributes</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css">
    <link rel="stylesheet" href="/angularjs/21/styles.css">
</head>
<body>
    <div class="container.fluid" ng-app="app">
        <div class="row">
            <div class="col-lg-6">
                <img src="http://fursealworld.com/wp-content/uploads/2013/03/1280BabyHarpSeal11.jpg"/>
            </div>
            <div class="col-lg-6">
                <img src="http://resources.news.com.au/files/2012/01/13/1226243/386315-harp-seal-1.jpg"/>
            </div>
        </div>
    </div>
    <script src="/bower_components/lodash/lodash.min.js"></script>
    <script src="/bower_components/angular/angular.js"></script>
    <script src="/angularjs/21/app.js"></script>
</body>
</html>
```

```css
/* angularjs/21/styles.css */
img {
    position: relative;
    width: 100%;
}
```

In order to get the domain from the full link URL, we must create a `context` object which has a 
`url` property.  We assign the first part of the URL to the `context.url` property using the regular
expression `/^http:\/\/[^\/]*/` which basically means that we are looking for a string that starts
with `http://` and is then followed by any number or characters apart from `/`.  This of course means
that this only matches upto the first `/`.

Next a `context.domain` property is created which strips the `http://` part from `context.url`.  

We now have a `context` object with two properties `url` and `domain`.

If we pass the `context` object into the lodash `_.template(context)` method, it will pass the 
properties into the template and return the template as a string which can then be appended to 
the DOM.



```javascript
// angularjs/21/app.js
(function(){
    'use strict';

    angular.module('app', [])
        .directive('src', srcDirective)
    ;

    function srcDirective () {
        var URL_RE = /^http:\/\/[^\/]*/;
        var HTTP_RE = /^(http|https):\/\//;

        return function (scope, element, attributes) {

            // match the URL upto the first '/'
            var context = {url: attributes.src.match(URL_RE)[0]};

            // strip 'http://' from the URL
            context.domain = context.url.replace(HTTP_RE, '');

            // define a templateFunction that can accept the 'context' object
            var templateFunction = _.template('<a href="<%= url %>" target="_blank">' +
                'Photo courtesy of <%= domain %></a>');

            // append the output from the templateFunction to the element
            element.after(templateFunction(context));
        };
    }
})();
```