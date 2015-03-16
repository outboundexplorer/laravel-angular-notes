##Notetaking app with CRUD
* using a `NoteService` to hold an array of notes
* using `var deferred = $q.defer()` to handle $http calls
* updating `notes[]` before and after $http response
* example of modular structure for large applications
* simple routing with `ui-router`
* `put` and `delete` with AngularJS and Laravel
* creating a temporary flag with `angular.extend()`

```html
<!doctype html>
<!-- File: angularjs/71/index.php -->
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title></title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width">
    <!-- Place favicon.ico and apple-touch-icon.png in the root directory -->
    <!-- styles/vendor -->
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.css" />

    <!-- styles/main -->
    <link rel="stylesheet" href="/angularjs/71/assets/css/main.css">
</head>
<body ng-app="app">
    <div class="container">
        <div class="header">
            <ul class="nav nav-pills pull-right">

                <!-- home module -->
                <li><a href='' ui-sref="home">Home</a></li>

                <!-- notes module -->
                <li ui-sref-active="active"><a href='' ui-sref="notes.listNotes">Notes</a></li>
            </ul>
            <h3 class="text-muted">AngularJS + UI-Router</h3>
        </div>

        <!-- application content here -->
        <div ui-view></div>

    </div>

    <!-- scripts/vendor -->
    <script src="/bower_components/angular/angular.js"></script>
    <script src="/bower_components/lodash/lodash.min.js"></script>
    <script src="/bower_components/angular-ui-router/release/angular-ui-router.js"></script>

    <!-- scripts/app -->
    <script src="angularjs/71/app.js"></script>
    <script src="angularjs/71/modules/home/app.home.js"></script>
    <script src="angularjs/71/modules/home/config/home.routes.js"></script>
    <script src="angularjs/71/modules/home/controllers/home.controller.js"></script>
    <script src="angularjs/71/modules/notes/app.notes.js"></script>
    <script src="angularjs/71/modules/notes/config/notes.routes.js"></script>
    <script src="angularjs/71/modules/notes/services/NoteService.js"></script>
    <script src="angularjs/71/modules/notes/controllers/ListNotesController.js"></script>
    <script src="angularjs/71/modules/notes/controllers/ShowNoteController.js"></script>
    <script src="angularjs/71/modules/notes/controllers/EditNoteController.js"></script>
    <script src="angularjs/71/modules/notes/controllers/AddNoteController.js"></script>

</body>
</html>
```

```javascript
// File: angularjs/71/app.js
(function(){
    'use strict';

    angular.module('app',[
        'ui.router',
        'iiHome',
        'iiNotes'
    ]);
})();
```

```javascript
// File: angularjs/71/modules/home/app.home.js
(function(){
    'use strict';
    angular.module('iiHome',[]);
})();
```

```javascript
// File: angularjs/71/module/home/config/home.routes.js
(function(){
    'use strict';

    angular.module('iiHome')
        .config(config)
    ;

    function config($stateProvider, $urlRouterProvider){

        $urlRouterProvider.otherwise('/');

        $stateProvider
            .state('home',{
                url: '/',
                templateUrl: '/angularjs/71/modules/home/views/home.html',
                controller: 'HomeController as homeVm'
            })
        ;
    }

})();
```

```javascript
// File: angularjs/71/modules/home/controllers/home.controller.js
(function(){
    'use strict';

    angular.module('iiHome')
        .controller('HomeController', HomeController)
    ;

    function HomeController(){
        var vm = this;

        vm.hello = 'from AngularJS';
    }
})();
```

```javascript
<!-- File: angularjs/71/modules/home/views/home.html.html -->
<h1>Hello
    <small>{{ mainVm.hello }}</small>
</h1>
```

```javascript
// File: angularjs/71/modules/notes/app.notes.js
(function(){
    'use strict';
    angular.module('iiNotes',[]);
})();
```

```javascript
// File: angularjs/71/modules/notes/config/notes.routes.js
(function(){
    'use strict';

    angular.module('iiNotes')
        .config(config)
    ;

    function config($stateProvider){
        $stateProvider
            .state('notes', {
                abstract: true,
                url: '/notes',
                templateUrl: '/angularjs/71/modules/notes/views/notes.base.html'
            })
            .state('notes.listNotes', {
                url:'',
                templateUrl: '/angularjs/71/modules/notes/views/listNotes.html',
                controller: 'ListNotesController as listNotesVm'
            })
            .state('notes.addNote', {
                url: '/add',
                templateUrl: '/angularjs/71/modules/notes/views/addNote.html',
                controller: 'AddNoteController as addNoteVm'
            })
            .state('notes.editNote',{
                url: '/edit/{id}',
                templateUrl: '/angularjs/71/modules/notes/views/editNote.html',
                controller: 'EditNoteController as editNoteVm'
            })
            .state('notes.showNote',{
                url: '/{id}',
                templateUrl: '/angularjs/71/modules/notes/views/showNote.html',
                controller: 'ShowNoteController as showNoteVm'
            })
    }
})();
```

```html
<!-- File: angularjs/71/modules/notes/views/notes.base.html -->
<br>

<div class="col-md-8 col-md-offset-2">

    <div ui-view></div>
</div>
```

```html
<!-- File: angularjs/modules/notes/views/listNotes.html -->
<p class="lead">
    Notes
    <a ui-sref="notes.addNote" href="" class="btn btn-default pull-right">
        <i class="glyphicon glyphicon-plus-sign"></i>
    </a>
</p>


<div ng-repeat="note in listNotesVm.notes">
    <div class="panel panel-default">
        <div class="panel-heading">
            <div class="pull-right btn-group">
                <a class="btn btn-default btn-xs" ui-sref="notes.editNote({id:note.id})">
                    <i class="glyphicon glyphicon-pencil"></i>
                </a>
                <a class="btn btn-default btn-xs" ng-click="listNotesVm.deleteNote(note)">
                    <i class="glyphicon glyphicon-trash"></i>
                </a>
            </div>
            <!-- temp. include note.id so that we can see when data is updated with db data -->
            <a ui-sref="notes.showNote({id:note.id})" href="">{{ note.name }}{{ note.id }}</a>
        </div>
        <div class="panel-body">
            <pre>{{note.content}}</pre>
        </div>
    </div>
</div>

<!-- check that the user has some notes -->
<div ng-show="listNotesVm.noNotesFlag">
    <h4>
        <a ui-sref="notes.addNote" href="">You have no notes, add some!</a>
    </h4>
</div>
```


```javascript
// File: angularjs/71/modules/notes/controllers/ListNotesController.js
(function() {
    'use strict';

    angular.module('iiNotes')
        .controller('ListNotesController', ListNotesController)
    ;

    function ListNotesController(NoteService) {
        var vm = this;

        NoteService
            .getNotes()
            .then(function (response) {
                console.log('step 5: vm.notes', vm.notes); // undefined
                vm.notes = response;
                console.log('step 6: vm.notes', vm.notes); // [Object, Object]
                if (!vm.notes.length){
                    console.log('vm.notes.length',vm.notes.length);
                    vm.noNotesFlag = true;
                }
            }, function (error) {
                // do something in the view if there was an error
                console.log('there was a problem with the data', error)
            })
        ;

        vm.deleteNote = function (note) {
            NoteService
                .deleteNote(note)
                .then(function (response) {
                })
            ;

            if (!vm.notes.length){
                console.log('vm.notes.length',vm.notes.length);
                vm.noNotesFlag = true;
            }
        }
    }
})();
```

```html
<!-- File: angularjs/modules/notes/views/showNote.html -->
<p class="lead">
    <a ui-sref="^.listNotes" class="btn btn-large btn-default">
        <i class="glyphicon glyphicon-arrow-left"></i>
    </a>
    <a href="#/notes/edit/{{ showNoteVm.note.id }}" class="btn btn-default pull-right">
        <i class="glyphicon glyphicon-pencil"></i>
    </a>
</p>

<div class="panel panel-default">
    <div class="panel-heading">
        {{ showNoteVm.note.name }}
    </div>
    <div class="panel-body">
        <pre>{{ showNoteVm.note.content }}</pre>
    </div>
</div>
```

```javascript
// File: angularjs/71/modules/notes/controllers/ShowNoteController.js
(function(){
    'use strict';

    angular.module('iiNotes')
        .controller('ShowNoteController', ShowNoteController)
    ;

    function ShowNoteController($stateParams, NoteService, $location){
        var vm = this;

        NoteService
            .getNoteById($stateParams.id)
            .then(function(response){
                console.log('response',response);
                // check for note
                if (response){                          // response == Object
                    vm.note = response;
                } else {                                // response == undefined
                    $location.path('/notes');
                }
            })
        ;
    }

})();
```

```html
<!-- File: angularjs/71/modules/notes/views/editNote.html -->
<p class="lead">
    <a ui-sref="^.listNotes" class="btn btn-large btn-default">
        <i class="glyphicon glyphicon-arrow-left"></i>
    </a>
    <a ng-click="editNoteVm.save()" href="" class="btn btn-default pull-right">
        <i class="glyphicon glyphicon-floppy-save"></i>
    </a>
</p>

<div class="form-group">
    <input class="form-control" id="name" name="name" ng-model="editNoteVm.noteCopy.name" placeholder="Name">
</div>
<div class="form-group">
    <textarea class="form-control" id="content" name="content" ng-model="editNoteVm.noteCopy.content" placeholder="Content"></textarea>
</div>

<hr>

<div class="panel panel-default">
    <div class="panel-heading">
        {{editNoteVm.note.name}}
    </div>
    <div class="panel-body">
        <pre>{{editNoteVm.note.content}}</pre>
    </div>
</div>
```

```javascript
// File: angularjs/71/modules/notes/controllers/EditNoteController.js
(function(){
    'use strict';

    angular.module('iiNotes')
        .controller('EditNoteController',EditNoteController)
    ;


    function EditNoteController($stateParams,$location, NoteService){
        var vm = this;

        NoteService
            .getNoteById($stateParams.id)
            .then(function(response){
                //check for note
                if( response){
                    vm.note = response;
                    vm.noteCopy = angular.copy(vm.note);
                } else {
                    $location.path('/notes');
                }
            }, function(error){
                // do something if there is an error
            })
        ;

        vm.save = function(){
            NoteService
                .updateNote(vm.note,vm.noteCopy)
                .then(function(response){
                },function(error){
                    // do something if there is an error
                });

            // Do not wait for NoteService response as notes[] already temporarily updated before $http request
            $location.path('/notes');
        }
    }

})();
```

```html
<!-- File: angularjs/71/modules/notes/views/addNote.html -->
<p class="lead">
    <a ui-sref="^.listNotes" class="btn btn-large btn-default">
        <i class="glyphicon glyphicon-arrow-left"></i>
    </a>
    <a ng-click="addNoteVm.save()" href="" class="btn btn-default pull-right">
        <i class="glyphicon glyphicon-floppy-save"></i>
    </a>
</p>

<div class="form-group">
    <input class="form-control" id="name" name="name" ng-model="addNoteVm.note.name" placeholder="Name">
</div>
<div class="form-group">
    <textarea class="form-control" id="content" name="content" ng-model="addNoteVm.note.content" placeholder="Content"></textarea>
</div>

<hr>
```

```javascript
// File: angularjs/71/modules/notes/controllers/AddNoteController.js
(function(){
    'use strict';

    angular.module('iiNotes')
        .controller('AddNoteController', AddNoteController)
    ;

    function AddNoteController($location, NoteService){
        var vm = this;

        console.log('vm.note',vm.note);

        vm.save = function(){
            console.log('vm.note1',vm.note);

            // DECISION: $location not wait for promise as visually should be same but appear much
            // faster.  Should still include then(successCallback, errorCallback, notifyCallback)
            NoteService
                .saveNote(vm.note)
                .then(function(){
                    console.log('success');
                })
            ;
            $location.path('/notes');

            /*
             // If $location in then(successCallback), then promise is resolved before loading
             NoteService
             .saveNote(vm.note)
             .then(function(){
             $location.path('/notes');
             })
             ;
             */
        }
    }
})();
```


```php
// 71
Route::get('/71', function()
{
    return View::make('/71/index');
});

Route::get('/71api/notes', function()
{
   $notes = DB::table('notes')->get();
    return $notes;
});

Route::post('/71api/notes', function()
{
    $time = \Carbon\Carbon::now()->toDateTimeString();

    $angularData = file_get_contents('php://input');

    $phpObject = json_decode($angularData);

    $data = [
        'name' 		=> 	$phpObject->name,
        'content' 	=>	$phpObject->content,
        'created_at' => $time,
        'updated_at' => $time
    ];

    $insertId = DB::table('notes')->insertGetId($data);
    $createdAt = $time;
    $updatedAt = $time;

    $responseData = [
            'insertId'  => $insertId,
            'createdAt' => $createdAt,
            'updatedAt' => $updatedAt
    ];

    return Response::json($responseData);

});

Route::put('/71api/notes/edit/{id}', function($id)
{
    $time = \Carbon\Carbon::now()->toDateTimeString();

    $angularData = file_get_contents('php://input');
    Log::info('This is the $angularData', [$angularData]);

    $phpObject = json_decode($angularData);

    //$data =[];
    $data['updated_at'] = $time;
    if(property_exists($phpObject,'name')){
        $data['name'] = $phpObject->name;
    }
    if(property_exists($phpObject,'content')){
        $data['content'] = $phpObject->content;
    }

    Log::info('This is the prepared data',[$data]);

    DB::table('notes')
        ->where('id','=',$id)
        ->update($data);

});

Route::get('/71api/notes/{id}', function($id)
{
   $note = DB::table('notes')
            ->where('id','=',$id)
            ->get();
   return $note;
});

Route::delete('/71api/notes/{id}', function($id)
{
   DB::table('notes')
            ->where('id','=',$id)
            ->delete();
});

Route::get('/71-seed', function(){

    // We need to convert the time into a format that our table can use
    $time = \Carbon\Carbon::now()->toDateTimeString();

    $data = [
        [
            'name' 		=> 	'This is the first note',
            'content' 	=>	'This is the first Content',
            'created_at' => $time,
            'updated_at' => $time
        ],
        [
            'name' 		=> 	'This is the second note',
            'content' 	=>	'This is the second Content',
            'created_at' => $time,
            'updated_at' => $time
        ]
    ];

    DB::table('notes')->insert($data);
});
```



