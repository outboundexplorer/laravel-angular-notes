##end-to-end testing with protractor
* testing visual output to the DOM with protractor
* testing login functionality
* checking whether certain elements are displayed or hidden
* checking that URL routing responds in the correct way. 

```
public> npm install -g protractor
public> webdriver-manager update
public> webdriver-manager start
```

open a separate terminal
```
...public\angularjs\68> protractor protractor.conf.js
```

```html
<!DOCTYPE html>
<!-- angularjs/68/index.php -->
<html>
<head>
    <title>Egghead.io</title>
    <link rel="stylesheet" href="/bower_components/bootstrap/dist/css/bootstrap.min.css"/>
    <script src="/bower_components/lodash/lodash.min.js"></script>
    <script src="/bower_components/angular/angular.min.js"></script>
    <script src="/angularjs/68/app.js"></script>
</head>
<body ng-app="app" ng-controller="AppController as appVm" class="container">
    <input type="text" ng-model="appVm.searchTerm"/>
    <span>{{ appVm.infoMessage }}</span>

    <table class="table table-bordered">
        <thead>
        <tr>
            <th>#</th>
            <th>Name</th>
            <th>Category</th>
            <th>Price</th>
        </tr>
        </thead>
        <tbody>
        <tr ng-repeat="product in appVm.products | filter: appVm.debounceTerm track by product.id">
            <td>{{ $index + 1 }}</td>
            <td>{{ product.name }}</td>
            <td>{{ product.category }}</td>
            <td>{{ product.price }}</td>
        </tr>
        </tbody>
    </table>
</body>
</html>
```

```javascript
// angularjs/68/app.js
(function(){
    angular.module("app", [])
        .controller("AppController", AppController)
    ;

    function AppController ($http, $scope) {
        var model = this;
        model.infoMessage = "";
        model.searchTerm = "";
        model.debounceTerm = "";

        $http
            .get("//testlab70/products")
            .success(function (data) {
                model.products = data;
            })
        ;


        $scope.$watch(angular.bind(this, function(searchTerm){
            return this.searchTerm;
        }), function (newValue, oldValue) {
            if(newValue == oldValue) return;
            model.infoMessage = "Loading...";
        });


        $scope.$watch(angular.bind(this, function(searchTerm) {
            return this.searchTerm;
        }), _.debounce(function (newValue, oldValue) {
            model.debounceTerm = newValue;
            model.infoMessage = "";
            $scope.$apply();
        }, 300));

           }
})();
```

```javascript
// File: angularjs/68/protractor.conf.js
exports.config = {
    // The address of a running Selenium server
    seleniumAddress: 'http://localhost:4444/wd/hub',

    // The URL where the server we are testing is running
    baseUrl: 'http://testlab70/',

    // Capabilities to be passed to the WebDriver instance
    capabilities: {
        'browserName': 'chrome'
    },

    // Spec patterns are relative to the location of the
    // spec file. They may include glob patterns.
    specs: ['e2e/*.spec.js'],

    // Options to be passed to Jasmine-node
    jasmineNodeOpts: {
        showColors: true // Use colors in the command-line report
    }
};
```

```javascript
// File: angularjs/68/e2e/simpleRouting.spec.js
describe('Routing Test', function(){

    it('should show products on the first page', function(){

        // open the products page
        browser.get('/68');

        // Check whether there are 97 rows in the repeater
        var rows = element.all(
            by.repeater('product in appVm.products')
        );
        expect(rows.count()).toEqual(97);

        // check the first row details
        var firstRowName = element(
            by.repeater('product in appVm.products')
                .row(0).column('product.name')
        );
        var firstRowCategory = element(
            by.repeater('product in appVm.products')
                .row(0).column('product.category')
        );
        var firstRowPrice = element(
            by.repeater('product in appVm.products')
                .row(0).column('product.price')
        );
        expect(firstRowName.getText()).toEqual('Jumper');
        expect(firstRowCategory.getText()).toEqual('Clothes');
        expect(firstRowPrice.getText()).toEqual('300');

        // check the last row details
        var firstLastName = element(
            by.repeater('product in appVm.products')
                .row(96).column('product.name')
        );
        var firstLastCategory = element(
            by.repeater('product in appVm.products')
                .row(96).column('product.category')
        );
        var firstLastPrice = element(
            by.repeater('product in appVm.products')
                .row(96).column('product.price')
        );
        expect(firstLastName.getText()).toEqual('ff');
        expect(firstLastCategory.getText()).toEqual('dd');
        expect(firstLastPrice.getText()).toEqual('33');

        /*
        // Check that login link is shown and logout is hidden
        expect(element.(by.css('.login-link')).isDisplayed())
            .toBe(true);
        expect(element(by.css('.logout-link')).isDisplayed())
            .toBe(false);
        */

    });

    /*
    // Cheking for login functionality
    it('should allow logging in', function() {

        // Navigate to the login page
        browser.get('#/login');

        var username = element(
            by.model('loginCtrl.user.username'));

        var password = element(
            by.model('loginCtrl.user.password'));

        // Type in the username and password
        username.sendKeys('admin');
        password.sendKeys('admin');

        // Click on the login button
        element(by.css('.btn.btn-success')).click();

        // Ensure that the user was redirected
        expect(browser.getCurrentUrl())
            .toEqual('http://localhost:8000/#/');

        // Check that login link is hidden and
        // logout link is shown
        expect(element(by.css('.login-link')).isDisplayed())
            .toBe(false);
        expect(element(by.css('.logout-link')).isDisplayed())
            .toBe(true);
    });
    */
});
```
