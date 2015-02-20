##Debugging tricks and turning off debugging for production
* `$compileProvider.debugInfoEnabled(false)` for production
* get an angular element's scope with `angular.element($0).scope()`
* get an angular element's data bindings with `angular.element($0).data()`


When we select an element in ChomeDevTools and then go into the console:

####To get the regular DOM element
```command
> $0

< <div class="ng-binding">hello</div>
```

####To get angular element
```command
> angular.element($0)

< [ <div class="ng-binding">hello</div>]
```

####To get scope on an angular element
```console
> angular.element($0).scope()

< {$id: 2, $$childTail: null, $$childHead: null, $prevSibling: null, $$nextSibling: null...}
```

####To get the data bindings on an angular element
```console
> angular.element($0).data()

<  Object {$binding: Array[1]}
```


If we look at the raw HTML, it is supplemented with angular debug info.

```html
<note message="hello" class="ng-isolate-scope">
    <div class="ng-binding">hello</div>
</note>

This is good for debugging and coding our applications, but when it comes to our production code, we are 
able to gain considerable performance gains by turning this off.  This is done by injecting 
`$compileProvider` into the `config` method.

```html
<!DOCTYPE html>
<!-- angularjs/25/index.php -->
<html ng-app="app">
<head>
    <title>bindToController</title>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/24/app.js"></script>
</head>
<body>
    <note message="hello"></note>
</body>
</html>
```

```javascript
// angularjs/25/app.js
(function(){
    'use strict';

    angular.module('app',[])
        .config(config)
        .directive('note', NoteDirective)
        .controller('NoteController', NoteController)
    ;

    function config($compileProvider){
        $compileProvider.debugInfoEnabled(false);
    }

    function NoteDirective(){
        return {

            // need a scope in order to pass in the message from the HTML
            scope: {
                // passing in a string so we use the '@' symbol
                message:'@'
            },

            // bind the 'scope' properties to 'noteVm'
            bindToController: true,
            controller: 'NoteController as noteVm',
            template: '<div>{{noteVm.message}}</div>'
        }
    }

    function NoteController(){
        var vm = this;
    }

})();
```





