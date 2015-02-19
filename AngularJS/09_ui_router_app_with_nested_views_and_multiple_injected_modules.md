##ui-router app with nested views and multiple injected modules

https://egghead.io/lessons/angularjs-ui-router-activating-states

* Activating different states within a ui-router app
* Displaying the active class in a ui-router app
* Using `controllerAs` when controller is defined in `$stateProvider`
* Module dependency management for nested ui-views
* Using $state.includes() to check for url state









```html
<!DOCTYPE html>
<!-- angularjs/09/index.php -->
<html ng-app="app">
<head>
  <title>Bug Squasher</title>
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
  <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css">
  <link rel="stylesheet" href="/angularjs/09/app.css"/>
</head>
<body>

    <div ng-include="'/angularjs/09/navbar/navbar.html'"></div>

    <div ui-view class="container"></div>

    <!-- Vendor JS -->
    <script src="/bower_components/jquery/dist/jquery.min.js"></script>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/bower_components/bootstrap/dist/js/bootstrap.min.js"></script>
    <script src="/bower_components/angular-ui-router/release/angular-ui-router.min.js"></script>

    <!-- App JS -->
    <script src="/angularjs/09/app.js"></script>
    <script src="/angularjs/09/api/projectData.js"></script>
    <script src="/angularjs/09/projectsList/projectsList.js"></script>
    <script src="/angularjs/09/projectsList/projectBoard/projectBoard.js"></script>
    <script src="/angularjs/09/projectsList/projectBoard/bugDetail/bugDetail.js"></script>
</body>
</html>
```

First of all, we have included the navbar as a part of every view.

```html
<!-- angularjs/09/navbar/navbar.html -->
<nav class="navbar navbar-default container" role="navigation">
  <a class="navbar-brand" href="#">Bug Squasher</a>
  <button type="button" class="btn btn-default navbar-btn pull-right">Sign in</button>
</nav>
```

The ui-view in the index.php file will be populated by the default view.  The `app` module will set the
default url to `/projects/p1`.  The `'app'` module requires that both the `'projectData'` and `'projectsList'`
modules are loaded.

```javascript
// angularjs/09/app.js
(function(){
    angular.module('app', ["ui.router", "projectData", "projectsList"])
        .config(config)
        .run(run)
    ;

    function config($urlRouterProvider){
        $urlRouterProvider.otherwise("/projects/p1");
    }

    function run($rootScope, $state) {
        $rootScope.$state = $state;
        $rootScope.$on('$stateChangeError', function (event, toState, toParams, fromState, fromParams, error) {
            console.log("StateChangeError:", error)
        });
    }
})();
```

The `projectData` module returns a service that can be injected into other modules.

```javascript
// anuglarjs/09/projectData.js
(function(){
     angular.module("projectData", [])
         .service('projectData', ProjectDataService)
     ;

     function ProjectDataService (){

         var model = this;

         model.getProject = function(id){
             return model.getAll().filter(function(project){
                 return project.id == id;
             })[0];
         };

         model.getBugs = function(projectId){
             var project = model.getProject(projectId);
             return project.bugs;
         };

         model.getBug = function(projectId, bugId){
             var project = model.getProject(projectId);
             var bug = project.bugs.filter(function(bug){
                 return bug.id == bugId;
             });
             return bug[0];
         };

         model.getAll = function(){
             return [
                 {
                     id: "p1",
                     name: "Project 1",
                     bugs: [
                         { id: "b01", name: "Bug 1", status: "Open",        priority: "High"    },
                         { id: "b02", name: "Bug 2", status: "Resolved",    priority: "High"    },
                         { id: "b03", name: "Bug 3", status: "Open",        priority: "Normal"  },
                         { id: "b04", name: "Bug 4", status: "In Progress", priority: "Normal"  },
                         { id: "b05", name: "Bug 5", status: "In Progress", priority: "Low"     }
                     ]
                 },
                 {
                     id: "p2",
                     name: "Project 2",
                     bugs: [
                         { id: "b11", name: "Bug 1", status: "Open",        priority: "High"    },
                         { id: "b12", name: "Bug 2", status: "Resolved",    priority: "High"    },
                         { id: "b13", name: "Bug 3", status: "Open",        priority: "Normal"  },
                         { id: "b14", name: "Bug 4", status: "In Progress", priority: "Normal"  },
                         { id: "b15", name: "Bug 5", status: "In Progress", priority: "Low"     }
                     ]
                 },
                 {
                     id: "p3",
                     name: "Project 3",
                     bugs: [
                         { id: "b21", name: "Bug 1", status: "Open",        priority: "High"    },
                         { id: "b22", name: "Bug 2", status: "Resolved",    priority: "High"    },
                         { id: "b23", name: "Bug 3", status: "Open",        priority: "Normal"  },
                         { id: "b24", name: "Bug 4", status: "In Progress", priority: "Normal"  },
                         { id: "b25", name: "Bug 5", status: "In Progress", priority: "Low"     }
                     ]
                 },
                 {
                     id: "p4",
                     name: "Project 4",
                     bugs: [
                         { id: "b31", name: "Bug 1", status: "Open",        priority: "High"    },
                         { id: "b32", name: "Bug 2", status: "Resolved",    priority: "High"    },
                         { id: "b33", name: "Bug 3", status: "Open",        priority: "Normal"  },
                         { id: "b34", name: "Bug 4", status: "In Progress", priority: "Normal"  },
                         { id: "b35", name: "Bug 5", status: "In Progress", priority: "Low"     }
                     ]
                 },
                 {
                     id: "p5",
                     name: "Project 5",
                     bugs: [
                         { id: "b41", name: "Bug 1", status: "Open",        priority: "High"    },
                         { id: "b42", name: "Bug 2", status: "Resolved",    priority: "High"    },
                         { id: "b43", name: "Bug 3", status: "Open",        priority: "Normal"  },
                         { id: "b44", name: "Bug 4", status: "In Progress", priority: "Normal"  },
                         { id: "b45", name: "Bug 5", status: "In Progress", priority: "Low"     }
                     ]
                 }
             ]
         }
     }
})();
```

The `projectsList` module is also injected into the `app` module.  The `projectsList` module uses `$stateProvider`
to respond to the state that has `/projects` within the url.  The model returned from the `projectData` module is
injected into the controller.  The `projectsList.html` template is inserted into the root `ui-view` location.
The `projectBoard` module is injected into this module.

```javascript
// angularjs/09/projectsList/projectsList.js
(function(){
    angular.module("projectsList", ["projectBoard"])
        .config(function ($urlRouterProvider, $stateProvider) {
            $stateProvider.state('projects', {
                abstract: true,
                url: '/projects',
                templateUrl: "/angularjs/09/projectsList/projectsList.html",
                controller: function(projectData){
                    var vm = this;
                    vm.projects = projectData.getAll();
                },
                controllerAs: 'projectsVm'
            });
        });
})();
```

The `projectsList.html` file provides the projects navigation menu.  In this state the navigation is a list of
projects. Each project is linked to the child view `.board` which populates the url with the additional project
id.  We can use `ng-class="{active: $state.includes('projects',{projectId: project.id})}"` in order to confirm that
the currently active project matches with the url in the current state.  The child ui-view is inserted into
this template.


```html
<!-- angularjs/09/projectsList.html -->
<div class="row">
  <ul class="nav nav-tabs" role="tablist">
    <li ng-repeat="project in projectsVm.projects"
        ng-class="{active: $state.includes('projects', {projectId: project.id})}">
      <a ui-sref=".board({projectId:project.id})">{{ project.name }}</a>
    </li>
  </ul>
  <div class="row" ui-view></div>
</div>
```

The `projectBoard` module is also injected into the `projectsList` module.  The `projectBoard` module
uses `$stateProvider` to match the specific `/:projectId` within the url.  The model returned from the
`projectData` module is injected into the controller.  The `projectBoard.html` template is inserted into the
parent `ui-view` location. The `bugDetail` module is injected into this module.


```javascript
// angularjs/09/projectsList/projectBoard/projectBoard.js
(function(){
    angular.module("projectBoard", ["bugDetail"])
        .config(function ($stateProvider) {
            $stateProvider.state('projects.board', {
                url: '/:projectId',
                templateUrl: "/angularjs/09/projectsList/projectBoard/projectBoard.html",
                controller: function(projectData, $stateParams){
                    var vm = this;
                    vm.bugs = projectData.getBugs($stateParams.projectId);
                },
                controllerAs: 'projectBoardVm'
            });
        });
})();
```

The `projectBoard.html` file provides the bug navigation menu for the currently selected project in the
parent state.  Each bug is linked to the child view `.bugDetail` which populates the url with the
additional bug id.  We can use `ui-sref-active="active"` to show which link has been clicked.  The child
ui-view is inserted into this template.


```html
<!-- angularjs/09/projectsList/projectBoard/projectBoard.html -->
<div class="col-xs-6">
  <div class="list-group" ng-repeat="bug in projectBoardVm.bugs">
    <a href="#" class="list-group-item" ui-sref=".bugDetail({bugId:bug.id})" ui-sref-active="active">
      <h4 class="list-group-item-heading">Bug {{ bug.id }}</h4>
    </a>
  </div>
</div>
<div class="col-xs-6" ui-view></div>
```

The `bugDetail` module uses $stateProvider to watch for a state that has `/bug/:bugId` appended to the
parent url.


```javascript
// angularjs/09/projectsList/projectBoard/bugDetail/bugDetail.js
(function(){
    angular.module("bugDetail", [])
        .config(function ($stateProvider) {
            $stateProvider.state('projects.board.bugDetail', {
                url: '/bug/:bugId',
                templateUrl: "/angularjs/09/projectsList/projectBoard/bugDetail/bugDetail.html",
                controller: function(projectData, $stateParams){
                    var vm = this;
                    vm.bug = projectData.getBug($stateParams.projectId, $stateParams.bugId);
                },
                controllerAs: 'bugDetailVm'
            });
        });
})();
```


```html
<!-- angularjs/09/projectsList/projectBoard/budDetail/bugDetail.html -->
<div class="well">
  <h4>Information for Bug {{ bugDetailVm.bug.id }}</h4>
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec ac nulla hendrerit, venenatis sem vel,
      auctor urna. Nulla tristique magna non eleifend molestie. Pellentesque nec bibendum mauris. Praesent
      gravida leo in purus blandit, dictum sollicitudin diam eleifend. Vestibulum scelerisque sed orci nec
      ultricies. Donec consectetur leo eros, a fermentum lorem pellentesque sit amet. Proin lacus sem,
      luctus sed consectetur vitae, facilisis vitae sem. Interdum et malesuada fames ac ante ipsum primis
      in faucibus. Pellentesque lacinia leo molestie mauris lobortis, sit amet pharetra erat facilisis.
      Etiam lobortis lobortis lorem, sit amet feugiat eros fermentum ac. In posuere, leo a convallis
      sagittis, ipsum mi sagittis leo, at consequat nisi mi sit amet sem.</p>
</div>
```


```css
/* angularjs/09/app.css */
.nav-tabs {
  margin-bottom: 10px;
}
```



