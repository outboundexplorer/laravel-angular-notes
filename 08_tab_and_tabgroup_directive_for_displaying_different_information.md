##Tab and tabgroup directive for displaying different information

http://plnkr.co/edit/CmyT5C?p=preview

https://egghead.io/lessons/angularjs-codeschool-refactor-reusable-directives-part-1-2

https://egghead.io/lessons/angularjs-codeschool-refactor-flexible-directives-part-2-2

* Using `scope:{ item: "="}` within a directive to improve decoupling
* Creating a tabs directive to create simple navigation for displaying different information
* Creating a tabgroup directive to create a simple navigation
* Using angular.forEach() and angular.equals() to check for active tab









```html
<!DOCTYPE html>
<!-- angularjs/08/index.php -->
<html ng-app="gemStore">

<head>
    <meta charset="utf-8" />
    <title>AngularJS</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="/bower_components/semantic-ui/dist/semantic.min.css" />
    <script src="/bower_components/jquery/dist/jquery.min.js"></script>
    <script src="/bower_components/semantic-ui/dist/semantic.min.js"></script>
    <link rel="stylesheet" href="/angularjs/08/assets/css/style.css" />
    <script src="/bower_components/angular/angular.min.js" data-semver="1.2.15"></script>
    <script src="/angularjs/08/app.js"></script>
    <script src="/angularjs/08/products.js"></script>
</head>

<body ng-controller="StoreController as storeVm">
<!--  Store Header  -->
<header>
    <h1 class="text-center">Flatlander Crafted Gems</h1>
    <h2 class="text-center">– an Angular store –</h2>
</header>

<!--  Products Container  -->
<div class="list-group">

    <!--  Product Container  -->
    <div class="list-group-item" ng-repeat="product in storeVm.products | orderBy:'-price'">

        <!-- $index Practice:  -->
        <h3>Gem #{{$index + 1}}: {{product.name | uppercase}}
            <em class="pull-right">{{product.price | currency}}</em>
        </h3>

        <!-- Image Gallery  -->
        <div ng-show="product.images.length" ng-controller="GalleryController as gallery">
            <div class="img-wrap">
                <img ng-src="{{product.images[gallery.imageIndex]}}"
                     class="img-thumbnail center-block" />
            </div>
            <ul class="img-thumbnails clearfix">
                <li class="small-image pull-left thumbnail" ng-repeat="image in product.images"
                    ng-click="gallery.currentImageChange($index)">
                    <img ng-src="{{image}}" />
                </li>
            </ul>
        </div>

        <!-- Tab Navigation and Contents -->
        <tabgroup>
            <tab title="Description">
                <product-description item="product"></product-description>
            </tab>
            <tab title="Specs">
                <product-specs item="product"></product-specs>
            </tab>
            <tab title="Reviews">
                <product-reviews item="product"></product-reviews>
            </tab>
        </tabgroup>

    </div>
</div>
</body>
</html>
```

___


```javascript
// angularjs/08/app.js
(function() {
    var app = angular.module('gemStore', ['store-directives']);

    app.controller('GalleryController', function() {
        this.imageIndex = 0;
        this.setCurrent = function(imageNumber) {
            console.log(imageNumber);
            this.imageIndex = imageNumber || 0;
        };
    });

    app.controller('StoreController', function() {
        this.products = gems;
    });


    app.controller("ReviewController", function(){

        this.review = {};

        this.addReview = function(product){
            this.review.createdOn = Date.now();
            product.reviews.push(this.review);
            this.review = {};
        };

    });

    var gems = [{
        name: 'Azurite',
        description: "Some gems have hidden qualities beyond their luster, " +
                "beyond their shine... Azurite is one of those gems.",
        shine: 8,
        price: 110.50,
        rarity: 7,
        color: '#CCC',
        faces: 14,
        images: [
            "/angularjs/08/assets/images/gem-02.gif",
            "/angularjs/08/assets/images/gem-05.gif",
            "/angularjs/08/assets/images/gem-09.gif"
        ],
        reviews: [{
            stars: 5,
            body: "I love this gem!",
            author: "joe@thomas.com",
            createdOn: 1397490980837
        }, {
            stars: 1,
            body: "This gem sucks.",
            author: "tim@hater.com",
            createdOn: 1397490980837
        }]
    }, {
        name: 'Bloodstone',
        description: "Origin of the Bloodstone is unknown, hence its low value. " +
                "It has a very high shine and 12 sides, however.",
        shine: 9,
        price: 22.90,
        rarity: 6,
        color: '#EEE',
        faces: 12,
        images: [
            "/angularjs/08/assets/images/gem-01.gif",
            "/angularjs/08/assets/images/gem-03.gif",
            "/angularjs/08/assets/images/gem-04.gif",
        ],
        reviews: [{
            stars: 3,
            body: "I think this gem was just OK, could honestly use more shine, IMO.",
            author: "JimmyDean@sausage.com",
            createdOn: 1397490980837
        }, {
            stars: 4,
            body: "Any gem with 12 faces is for me!",
            author: "gemsRock@alyssaNicoll.com",
            createdOn: 1397490980837
        }]
    }, {
        name: 'Zircon',
        description: "Zircon is our most coveted and sought after gem. You will pay much to be " +
                "the proud owner of this gorgeous and high shine gem.",
        shine: 70,
        price: 1100,
        rarity: 2,
        color: '#000',
        faces: 6,
        images: [
            "/angularjs/08/assets/images/gem-06.gif",
            "/angularjs/08/assets/images/gem-07.gif",
            "/angularjs/08/assets/images/gem-08.gif"
        ],
        reviews: [{
            stars: 1,
            body: "This gem is WAY too expensive for its rarity value.",
            author: "turtleguyy@zdn.me",
            createdOn: 1397490980837
        }, {
            stars: 1,
            body: "BBW: High Shine != High Quality.",
            author: "LouisW407@gmail.com",
            createdOn: 1397490980837
        }, {
            stars: 1,
            body: "Don't waste your rubles!",
            author: "nat@flatland.com",
            createdOn: 1397490980837
        }]
    }];

})();
```

___


```javascript
// angularjs/08/products.js
(function(){
    angular
        .module('store-directives', [])
        .directive('productDescription', ProductDescriptionDirective)
        .directive('productReviews', ProductReviewsDirective)
        .directive('productSpecs', ProductSpecsDirective)
        .directive('tabgroup', TabgroupDirective)
        .directive('tab', TabDirective)
    ;


    function ProductDescriptionDirective(){
        return {
            restrict: "E",
            // in the original demo on CodeSchool, the directive template was tightly bound to the location it was used in
            // the index.php file as it used {{product.description}} with product coming from
            // 'ng-repeat="product in storeVm.products"'.  This means that if the directive is placed outside of the
            // repeater, the code will be broken.  So to avoid this and increase the flixibility of our directive, we
            // add a scope attribute which means we can define the object that is used.  In this case we can pass in
            // item="product" into our index.php file, which means that we are now actively declaring the target object
            // at the point of usage, which means that if it is outside of the repeater, we can still use it and therefore
            // declare it in a different way.
            scope: {
                item: "="
            },
            templateUrl: "/angularjs/08/product-description.html"
        }
    }

    function ProductReviewsDirective(){
        return {
            restrict: "E",
            scope: {
                item: "="
            },
            templateUrl: "/angularjs/08/product-reviews.html"
        }
    }

    function ProductSpecsDirective(){
        return {
            restrict: "E",
            scope: {
                item: "="
            },
            templateUrl: "/angularjs/08/product-specs.html"
        }
    }

    function TabgroupDirective(){
        return {
            restrict:'E',
            transclude: true,
            templateUrl: '/angularjs/08/product-tabs.html',
            controller: function($scope){
                $scope.tabs = [];
                this.addTab = function(tab){
                    if($scope.tabs.length == 0){
                        tab.selected = true;
                    }
                    $scope.tabs.push(tab);
                };

                $scope.select = function(tab){
                    angular.forEach($scope.tabs, function(eachTab){
                        eachTab.selected = angular.equals(tab, eachTab);
                    })
                }
            }
        }
    }

    function TabDirective(){
        return {
            restrict:'E',
            scope: {
                title:'@'
            },
            transclude: true,
            template:'<div ng-show="selected" ng-transclude></div>',
            require: "^tabgroup",
            link: function (scope,element,attributes,controller){
                controller.addTab(scope);
            }
        }
    }

})();
```
___

```html
<!-- angularjs/08/product-tabs.html -->
<section>
    <ul class="nav nav-pills">
        <li ng-repeat="tab in tabs" ng-class="{active:tab.selected}">
            <a href ng-click="select(tab)">
                {{ tab.title }}
            </a>
        </li>
    </ul>


    <!-- This will ensure that the original HTML that was placed between the <tabgroup> tags will
    still be visible -->
    <div ng-transclude></div>
</section>
```

```html
<!-- angularjs/08/product-description.html -->
<div>
    <h4>Description</h4>
    <blockquote>{{item.description}}</blockquote>
</div>
```

___

```html
<!-- angularjs/08/product-reviews.html -->
<div>
    <!--  Product Reviews List -->
    <ul>
        <h4> Reviews </h4>
        <li ng-repeat="review in item.reviews">

            <blockquote>
                <strong>{{review.stars}} Stars</strong>
                {{review.body}}
                <cite class="clearfix">—{{review.author}}  on {{review.createdOn | date}}</cite>
            </blockquote>
        </li>
    </ul>

    <!--  Review Form -->
    <form name="reviewForm" role="form" ng-controller="ReviewController as reviewsVm" ng-submit="reviewsVm.addReview(product)">
        <!--  Live Preview -->
        <blockquote ng-show="reviewsVm.review.stars">
            <strong>{{reviewsVm.review.stars}} Stars</strong>
            {{reviewsVm.review.body}}
            <cite class="clearfix">—{{reviewsVm.review.author}}</cite>
        </blockquote>

        <!--  Review Form -->
        <h4>Submit a Review</h4>
        <fieldset class="form-group">
            <select ng-model="reviewsVm.review.stars" class="form-control" ng-options="stars for stars in [5,4,3,2,1]" title="Stars">
                <option value="">Rate the Product</option>
            </select>
        </fieldset>
        <fieldset class="form-group">
            <textarea ng-model="reviewsVm.review.body" class="form-control" placeholder="Write a short review of the product..." title="Review"></textarea>
        </fieldset>
        <fieldset class="form-group">
            <input ng-model="reviewsVm.review.author" type="email" class="form-control" placeholder="jimmyDean@sausage.com" title="Email" />
        </fieldset>
        <fieldset class="form-group">
            <input type="submit" class="btn btn-primary pull-right" value="Submit Review" />
        </fieldset>
    </form>
</div>
```

___

```html
<!-- angularjs/08/product-specs.html -->
<div>
    <h4>Specs</h4>
    <ul class="list-unstyled">
        <li>
            <strong>Shine</strong>
            : {{item.shine}}</li>
        <li>
            <strong>Faces</strong>
            : {{item.faces}}</li>
        <li>
            <strong>Rarity</strong>
            : {{item.rarity}}</li>
        <li>
            <strong>Color</strong>
            : {{item.color}}</li>
    </ul>
</div>
```

___

```css
/*  angularjs/08/assets/css/style.css */
body{
    max-width: 500px;
    margin: 0 auto;
}

a {
    color: #ac005f;
}
a:hover,
a:focus {
    text-decoration: underline;
}

ul{
    padding: 0;
}

li{
    list-style: none;
}

li.small-image{
    width: 50px;
}

li.small-image img{
    width: 100%;
}

img{
    max-width: 230px;
    max-height: 230px;
}

.img-wrap {
    background: #f8f8f8;
    height: 200px;
    margin: 0 auto 20px;
    padding: 10px;
    width: 200px;
}

.img-thumbnail {
    background: none;
    border: 0;
    height: auto;
    max-width: 100%;
}

.img-thumbnails {
    margin: 0 auto 10px;
    width: 200px;
}

.img-thumbnails .thumbnail {
    background: #f8f8f8;
    border: 0;
    margin-right: 25px;
    min-height: 50px;
}

.img-thumbnails .thumbnail:last-child {
    margin-right: 0;
}

.nav-pills {
    margin-bottom: 30px;
}

.nav-pills > li > a:hover,
.nav-pills > li > a:focus{
    color: #ac005f;
}

.nav-pills > li.active > a,
.btn-primary,
.btn-primary:focus {
    background: #ac005f;
    border: 0;
}

.nav-pills > li.active > a:hover,
.btn-primary:hover {
    background: #ac005f;
}
```

