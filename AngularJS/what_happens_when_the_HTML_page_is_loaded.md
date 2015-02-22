##what happens when the HTML page is loaded

ref: AngularJS Up & Running

1. The HTML is loaded. This triggers requests for all the scripts that are a part of it.
2. After the entire document has been loaded, AngularJS kicks in and looks for the
ng-app directive.
3. When it finds the ng-app directive, it looks for and loads the module that is specified
and attaches it to the element.
4. AngularJS then traverses the children DOM elements of the root element with the
ng-app and starts looking for directives and bind statements.
5. Each time it hits an ng-controller or an ng-repeat directive, it creates what we
call a scope in AngularJS. A scope is the context for that element. The scope dictates
what each DOM element has access to in terms of functions, variables, and the like.
6. AngularJS then adds watchers and listeners on the variables that the HTML accesses,
and keeps track of the current value of each of them. When that value
changes, AngularJS updates the UI immediately.
7. Instead of polling or some other mechanism to check if the data has changed, AngularJS
optimizes and checks for updates to the UI only on certain events, which
can cause a change in the data or the model underneath. Examples of such events
include XHR or server calls, page loads, and user interactions like click or type.