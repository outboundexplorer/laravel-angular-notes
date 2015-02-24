##Should my functionality go in a controller or a service

Source: AngularJS Up & Running

* It needs to be reusable - 
More than one controller or service will need to access the particular function that
is being implemented.


* Application-level state - 
Controllers get created and destroyed. If we need state stored across our application,
it belongs in a service.


* It is independent of the view - 
If what we are implementing is not directly linked to a view, it probably belongs in
a service.


* It integrates with a third-party service - 
We need to integrate a third-party service (think SocketIO, BreezeJS, etc.), but we
want to be able to mock or replace it in our unit tests. A service makes that easy.


* Caching/factories - 
Do we need an object cache? Or something that creates model objects? Services are
our best bet.