## Server side validation when using AngularJS
$parse to the rescue.

Let us say you already have worked out the JSON response data from your REST call to contain Form level error message and field level error messages.

Now, to bind the field errors from JSON to the view,

In your JS controller

for (var fieldError in response.fieldErrors) {
...
var serverMessage = $parse('addForm.' + fieldName + '.$error.serverMessage');
$scope.addForm.$setValidity(fieldName, false, $scope.addForm);
serverMessage.assign($scope, fieldMsg);
}

In your HTML view,
<input id="zip" class="form-control" type="text" name="zip" data-ng-model="addr.zip" required>
<span class="label label-danger" data-ng-if="addForm.zip.$error.serverMessage">{{addForm.zip.$error.serverMessage}}</span>
