## JavaScript / AngularJS Tips for a Java Developer
Data Types
Everything is an object except the primitives - null, boolean, string, number, undefined.
To test,
alert(typeof(myObject));

Defined & Undefined
var a;
alert(b);  // b is not defined. Hence, you will get a ReferenceError

If you have
var a;
alert(a); // a still has no value set. 'undefined' value gets set automatically on all function variables.

for..in
The for..in loop is used in Java to iterate over the elements in a collection. In JavaScript, its usage is not recommended for arrays. It is used to iterate over object properties.

var array = ['a', 'b', 'c'];
for (var i in array) {
  alert(array[i]);
}

The above code may or may not iterate in given order. Inherited properties on Array.prototype.xx will also be iterated. Any functions defined on the object are also iterated over since in JavaScript, a function is just another property in a object.

You can use forEach
var myStringArray = ["Hello", "World"];
myStringArray.forEach( function(s) { 
     // ... do something with s ...
});

OR the classic for-loop.

var length = myStringArray.length;
for (var i=0; i < length; i++) {

}

Creating an object
myObject=new Object();
OR
var myObject={};
OR 
function MyObject() {}
var myObject = new MyObject();

Hoisting is the act of moving the declarations to the top of the function.

Variable hoisting
Scope of a variable is not defined by the block they are enclosed in, as with Java, C++, C#, etc.
There are only 2 scopes in JavaScript - local and global.
var myvar = "global";
 
function mymethod(){
    alert(myvar);   // undefined
    var myvar = "local";
    alert(myvar);   // local
}
 
mymethod();

This is the same as 

var myvar = "global";
 
function mymethod(){
    var myvar;      
    alert(myvar);   // undefined
    myvar = "local";
    alert(myvar);  // local
}
 
mymethod();



But dont count on variable hoisting. To make your code readable, declare your variables at the top of the function explicitly.



Function Hoisting

Two ways to declare a function within a function - function declaration and function expression.




function something(){  
    // function declaration
    function mymethod(){}
         
    // function expression
    var mysecondmethod = function() {};
     
    mymethod();
    mysecondmethod();
}


function mymethod(){
   alert("global mymethod");
}
 
function mysecondmethod(){
   alert("global mysecondmethod");
}
 
// we are re-defining the functions and hence hiding the global functions successfully
function hoisting(){
    alert(typeof mymethod);
    alert(typeof mysecondmethod);
     
    mymethod();       // local mymethod
    mysecondmethod(); // TypeError: undefined is not a function
     
    // mymethod AND the implementation get hoisted
    function mymethod(){
        alert("local mymethod");  
    }
     
    // Only the variable mysecondmethod get's hoisted
    var mysecondmethod = function() {
        alert("local mysecondmethod");  
    };
}
hoisting();
 


Object copy by reference similar to Java
var addressArr = [{street: '123 main st',  city: 'vienna'}, {street: '123 bourbon st', city:'new orleans'}];
var address1 = addressArr[1];
address1 = {street:'washington st', city:'new york'};
alert(addressArr[1].street + ' ' + addressArr[1].city); // still  prints 123 bourbon st new orleans because the assignment of new object resulted in new memory pointer
address1 = addressArr[1];                                          // reinitialize
address1.street = 'washington st';
address1.city = 'new york';
alert(addressArr[1].street + ' ' + addressArr[1].city); // now will print washington st new york

Inheritance
If you add a function to an object, all instances of the object DO NOT automatically have access to that function directly.
You would have to add the function to the object's prototype for it to propagate to all instances of the object. i.e. The object's prototype is similar to class definition or template that the object is based on. Hence, to have functions accessed directly by instances of an object, you have to add to the prototype.

Method overriding works similar to Java. i.e. an instance can choose to override a method it inherited.

See example below.
function MyObject() {}
var myObject = new MyObject();
MyObject.draw = function() { alert("circle"); }
var anotherObject = new MyObject(); 
anotherObject.draw = function() { alert("rectangle"); }
//myObject.draw();       // ReferenceError because function draw() added on line 3 is a static function
anotherObject.draw();  // rectangle
MyObject.draw();       // circle - static function call
MyObject.prototype.draw = MyObject.draw;
anotherObject.draw();  // rectangle - because overriden
var secondObject = new MyObject();
secondObject.draw();   // circle
myObject.draw();       // circle - prior to the assignment to prototype, you got ReferenceError
secondObject.draw = function() { alert("triangle"); }
secondObject.draw();  // triangle - method overriding
myObject.draw();      // circle - nothing changed with all other instances

New Operator in JavaScript
An understanding of the new operator in javascript is essential to the understanding the difference between AngularJS Factory vs Service. A service object although a singleton is instantiated by Angular using new operator and then injected wherever needed. A Factory object is also a singleton but is not instantiated using new operator. Hence, this explains the different style in writing a Service object vs a Factory object.

When new operator is used to create an object such as

var Employee = function(name, dept)  {
   this.name = name;
   this.dept = dept;
 }

 using
 var phil = new Employee('Phillip', 'sales');

 The javascript interpreter does the following:

 var Employee = function (name, dept) {
   var obj = Object.create(Employee.prototype);
   this = obj;
  
   this.name = name;
   this.dept = dept;
  
   return this;
 }

  Note : When a call is made on the object returned by new, if the call is not implemented on the object directly, then the object's Prototype is consulted to see if the object's Prototype implements that call.

 phil.payRaise(); will fail unless you have defined payRaise() on Employee or you have defined it on Employee.Prototype.

 Employee.prototype.payRaise = function() {
    alert('Go get a drink');
 }
 phil.payRaise(); will succeed now.
When new operator is used to create an object such as 

var Employee = function(name, dept)  {
   this.name = name;
   this.dept = dept;
 }

 using
 var phil = new Employee('Phillip', 'sales');

 The javascript interpreter does the following:

 var Employee = function (name, dept) {
   var obj = Object.create(Employee.prototype);
   this = obj;
   
   this.name = name;
   this.dept = dept;
   
   return this;
 }

  Note : When a call is made on the object returned by new, if the call is not implemented on the object directly, then the object's Prototype is consulted to see if the object's Prototype implements that call.

 phil.payRaise(); will fail unless you have defined payRaise() on Employee or you have defined it on Employee.Prototype.

 Employee.prototype.payRaise = function() {
    alert('Go get a drink');
 }

 phil.payRaise(); will succeed now.

AngularJS Scope inheritance
Angular Scope objects act as context to the view elements. Hence, AngularJS Scope inheritance follows same rules as Javascript View inheritance(based on nesting of elements).

Scope variable look-ups go upward in the scope hierarchy. So, if a scope variable is overriden in the child, then the parent scope variable becomes hidden. This is exactly how Prototype Inheritance behaves.

However, when you set/define a new value for the (parent's) variable in a child scope, then the scope gets a new copy of the variable. i.e. The variable in the parent scope remains unmodified.

If you want to be able to modify the parent scope variable value from within the child, then the variable should be defined as an object in the parent scope.

If you want to explicitly refer to the parent scope variable from within child, you can always also use $scope.$parent.var

 You're $scope is a place to PUT the "model", but is not actually the "model". You'll want to do more with objects and always see a "." in your views when referencing a $scope property.

http://jonathancreamer.com/adding-clarity-to-scope-inheritance-in-angular/

Scope inheritance in AngularJS explained in great detail in the article link shown above.

this vs $scope in AngularJS
It is very tempting to have functions that are not accessed from the view directly, defined using this.

functions defined on this are associated with the controller. They are not always accessible inside the scope functions when the scope functions are executed. Hence, if a function is needed by a scope function, easy rule of thumb- declare it on the scope instead of on the controller, although, you don't really access that function from the view directly.


AngularJS UI-Router
The easiest way to declare nested states is using the . notation.

*** State hierarchy may or may not mimic View hierarchy. Nothing is inherited from parent state to child state by default. If you see data from Controller defined in Parent State, that is probably due to the View hierarchy(Nested views).

To explicitly share data via states(not based on view hierarchy), you should use "data" property on the state. Typically, this is unnecessary if your views are nested.

https://github.com/angular-ui/ui-router/wiki/Nested-States-%26-Nested-Views

Alert: If you need to use AngularUI Bootstrap Datepicker in one of the nested pages, be careful using the sample code as-is. If the datepicker event handlers are declared in the parent view controller, they may not work(due to how the datepicker directive is implemented?). 
<p class="input-group">
              <input type="text" class="form-control" datepicker-popup="{{format}}" ng-model="dt" is-open="opened" min-date="minDate" max-date="'2015-06-22'" datepicker-options="dateOptions" date-disabled="disabled(date, mode)" ng-required="true" close-text="Close" />
              <span class="input-group-btn">
                <button type="button" class="btn btn-default" ng-click="open($event)"><i class="glyphicon glyphicon-calendar"></i></button>
              </span>
            </p>
If you followed the recommended practice and declared event handlers on an object like you should have, then you wont have the issue.
<p class="input-group">
              <input type="text" class="form-control" datepicker-popup="{{mydate.format}}" ng-model="mydate.dt" is-open="mydate.opened" min-date="mydate.minDate" max-date="'2015-06-22'" datepicker-options="mydate.dateOptions" date-disabled="mydate.disabled(date, mode)" ng-required="true" close-text="Close" />
              <span class="input-group-btn">
                <button type="button" class="btn btn-default" ng-click="mydate.open($event)"><i class="glyphicon glyphicon-calendar"></i></button>
              </span>
            </p>


Event Handling
Events can travel upwards the scope hierarchy(from child to root). The official term for this is emitting events.
Events can also be sent downwards to all children in the scope hierarchy by broadcasting the events.
Some directives that depend on event handlers use this event propagation mechanism. You can stop the event propagation by invoking stopPropagation() in the controller.

If you want the ParentController to broadcast an event downwards..
.controller('ParentController', ['$scope', function($scope) {
  $scope.$broadcast('specialevent');
 ])};

.controller('ChildController', ['$scope', '$log', function($scope, $log) {
  $scope.$on('specialevent', function() {
         $log.debug('specialevent received');
    });
 ])};

If you want to pass data along with the notification, you can inject both controllers with a simple factory.

Similarly, to propagate an event upwards from child to parent, you can use $emit and $on.


[] brackets in Controllers, Factories 
Ensures that when code minification is used, there wont be any issues.
app.controller("catgController", ['$scope', '$log', 'catgFactory', 

                         function ($scope,   $log,   catgFactory) {
...
}]);


AngularJS scope.$watch and scope.$apply


AngularJS controllers, factories, providers and services

Angular DatePicker works fine with the model date object set directly on scope, but causes odd behavior when set on $scope.Form1.date.

An awesome AngularJS calendar - https://github.com/mattlewis92/angular-bootstrap-calendar
