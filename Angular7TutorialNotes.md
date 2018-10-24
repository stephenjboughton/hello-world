## A Basic Angular App

We can create a new Angular App by going to the command prompt (can be the integrated terminal in VS Code or just normal Windows Command Prompt) and type:

    ng new <project-name>

Once you have the new project created, cd into the folder and then type:

    ng serve

This will make a connection to the localhost and keep a live update version of the project in your browser.

Every Angular application is comprised of some higher level parts: 

    1. Every Angular appication has at least 1 module - the root module, which by convention will be called app.module.ts (AppModule) when you use the Angular CLI to start your project
    2. Each module is made up of components and services.  Each component makes up a portion of the screen view.  Each application has a root component which will be standardized as app.component.ts (AppComponent.) 
    3. Each component contains an html template and a class file that help define the view and control the logic.
    4. A service helps control the business logic that your app runs off of.
    5. Modules import and export different classes and views in order to render the overall project view on your screen.

Every Angular application starts out with some files to note:

    1. package.json - contains the dependencies - the libraries that angular needs in order for your application to work. These all get installed inside the node_modules folder within the project hierarchy.
    2. main.ts file - a file inside the src folder that kickstarts the whole project by bootsrapping the AppModule
    3. app.module.ts - a file inside the app folder that boostraps the different components in order to render the view, combining the template of the app.component.ts file and the logic of the app.component.ts class file. Styles and Templates can be set inline inside the metadata or linked to a url where that info resides in a separate file.

## Components

A component is made up of a template file and a class file.

The template uses html to represent the view for the component

The class uses typescript code to represent the data (our model) that is returned by a service and methods. A decorator is used to provide metadata that help Angular recognize the class as a component and tie it together with the selector, styles, and html that are needed to create the view. the @Component decorator is used to define a class as a component and not just something that contains some data or an interface.

## Property Binding

Something to consider about html and the dom - There is a difference between attributes and properties:

    * Attributes are native to and only really exist in html. Once they initialize a property they no longer exist in the DOM and they will not change even if the property is updated.
    * Properties are initialized by attributes but then live in the DOM, and as a result, properties can be changed by the user

The follwing html will bind the html element to our class property dynamically, so that any time the class property changes, the html element property changes as well:

    |   <input [id]="myId" type="text" value="SteveB">

    |   public myId = "testId";


    

## Template reference variables

Allow us to take properties and values from dom elements in the template file if/when we need to log or use data entered by the user.

We create the variable by adding #variableName to the element in the template:

    <input #myInput type="text">
    <button (click="logMessage(myInput.value)")>Log</button>

In this example adding #myInput to our input element allows us to pass the value of the input field to our logging function as a parameter referred to by myInput.value. If we left of the .value property of the myInput variable then we could pass the entire dom element to the function and then we would have to specify within the function that we are going to log just the value property - but we could also log any other available properties or perform other actions besides logging.

## Two Way Binding

We know that if we want a dom element to display the value of a class property we can use property binding, and if we want text entered by the user to flow to a class and be used in some way we use event binding, but what if we want both, so that our data in the template/dom is always consistent with our data in the class?  We use two way binding.  As an example, say we have a class with the property:

    public name = "";

and a dom template with an input form:

    <input text="type">

we can add the ngModel DIRECTIVE as so:

    <input [(ngModel)]="name" text="type">
    {{name}}

what the ngModel does is bind click events to the name property so that it updates within the class upon clicks AND binds the dom element to the value of the property so that it is always consistent with the property data.  It gets both the square brackets of property binding AND the parentheses of the event binding.  And of course, by using interpolation we can display the results of any update to the class property right next to the input element, which confirms visually that our two way binding is working.

One thing we have to remember: The ngModel directive is not automatically accessible within angular - we have to be sure we import the Forms module, whithin which the ngModel directive resides, to our app module, then include the forms module in our imports array metadata within the app module so that it can be used by any of the componenets of our app.

## ngIf directive

Next we tackle the first of three DIRECTIVES that help us conditionally control the structure of the dom, the ngIf directive.

If we have a dom element that we want to display based on a truthy or falsey evaluation, we can add the ngIf directive and set it equal to a simple truthy value, wihch we might access through a class property:

    |   <h2 *ngIf="displayGreeting">Welcome Stephen!<h2>

    |   public displayGreeting = "true";

Now if displayGreeting was set to false we would not see the h2 with our greeting, and not only would it not display on the screen, but it would also be taken out of the dom entirely, which is a bit different than when we set a css selector applied to the element to display: none.

Additionally, we can attach an else to our *ngIf by using an ng-template and modifying the original ngIf as follows:

    <h2 *ngIf="displayGreeting; else elseBlock">Welcome Stephen!<h2>
    <ng-template #elseBlock><h2>User not recognized<h2></ng-template>

in this case, if our displayGreeting property is set to false the ngIf will skip over our greeting and look at the else portion, which tells it to look for a template reference variable of "elseBlock", and since that is within an ng-template block, the ngIf will add that element to the dom in place of the Greeting. We could translate that first line of code to "If displayGreeting is true, show this Greeting, else, if it's false, show the element referred to by "elseBlock". Of course while the ngIf is true the ng-template block is omitted entirely from the dom.

Another way we an construct this that is more verbose but perhaps easier to read:

    <div *ngIf="displayGreeting; then thenBlock; else elseBlock"></div>

    <ng-template #thenBlock><h2>Welcome Stephen!<h2></ng-template>
    <ng-template #elseBlock><h2>User not recognized<h2></ng-template>

This is easily read as "If displayGreeting is true then add thenBlock to the dom, else add elseBlock to the dom.  Clear, but not quite as concise.

## ngSwitch directive

This conditional structural control directive is pretty much the same as a switch statement in any other language - it allow us to pick one of several cases based on the input and it will display the appropriate one (or perhaps a default if we don't match a specific case):

    |    <div [ngSwitch]="user">
    |       <div *ngSwitchCase="'Steve'">Hello Steve</div>
    |       <div *ngSwitchCase="'Pat'">Hello Pat</div>
    |       <div *ngSwitchCase="'Ryan'">Hello Ryan</div>
    |       <div *ngSwitchDefault>Hello User</div>
    |    </div>

    | public user="Brian"

In this example the switch cases compare the value of the property "user" to eah case and when they find the match, they display a greeting for that user's name, but if the value of user is none of the cases, it just displays "hello user".

## ngFor directive

The ngFor DIRECTIVE is basically the equivalent of a foreach loop in C# where it allows us to iterate through an array of values that are assigned to a property:

    |    <div *ngFor="let user of users">
    |       <h2>{{user}}</h2>
    |    </div>

    | public users = ["steve", "pat", "ryan", "brian"];

"let" assigns a variable "user" and foreaches through each element of "users", and whatever we put within the element we attach the ngFor to gets repeated for each of the users.  Because we are iterating through an array, which has an index that the code has access to, angular gave us some modifiers to the *ngFor that work with that index value:

    <div *ngFor="let user of users; index as i">
        <h2>{i} {{user}}</h2>  
    </div>  

This will interpolate the value of the array index of each element added to the dom (0, 1, 2, 3, etc).

    <div *ngFor="let user of users; first as f">      <div *ngFor="let user of users; last as l">     

This will interpolate either true or false and add it to the dom based on whether the "user" is the first or last element within the array

    <div *ngFor="let user of users; even as e">        <div *ngFor="let user of users; odd as o">

Would interpolate either true or false based on whether the "user" is at an even or odd numbered index.  This could be handy if we want to assign different classes to alternating elements in the list of items.

## Sending data back and forth between Parent and Child componenets

In order to send data to a child component from the main component (AppComponent) we must:

1) Declare a property within the parent component
    C.TS>   public name = "Steve"
2) Bind the property to the child selector within the parent template
    P.HT>   <app-child [parentData]="name"></app-child>
3) Declare a property called parentdata in the child component and add the @input() decorator to it, or give it an alias name and insert 'parentdata' as a paremeter within the @input() decorator parentheses
    C.TS>   @Input('parentData') public name;
4) Bind the child property within the child template
    C.HT>   <h2>{{"Hello" + name}}</h2>
5) MAKE SURE you have imported the import decorator from the main module at the top of the child class file
    C.TS>   import {Component, OnInit, Input } from '@angular/core';

In order to send data to the parent component fro the child component, we must use a click event and emit it with the EventEmitter class:

1) Declare an instance of the EventEmitter class as a variable within the child component and add the @output() decorator to it
    C.TS>   @Output() public childEvent = new EventEmitter();
2) Insert a button in the child template that listens for a click event and upon hearing it, fires an EventEmitter method, which we can define within the child class
    C.HT>   <button (click)="fireEvent()">Send Event to App</button>

    C.TS>   fireEvent() {this.childEvent.emit('Hey Parent App!');}
3) Capture the event being emitted by adding a click event within the child selector in the parent template and binding it to a property that we will create within the parent component class
    P.HT>   <app-child (childEvent)="message=$event" [parentData]="name"></app-child>
    // the $event variable gives access to the argument that was supplied to the Event.emit method, which we assign to the property message within the parent class
    P.TS>   public message="";
4) Interpolate the property that captures the message withing the parent component in the parent template
    P.HT> <h1>{{message}}</h1>
5) MAKE SURE you have imported the output and EventEmitter decorators/classes from the main module at the top of the child class file
    C.TS>   C.TS>   import {Component, OnInit, Input, Output, EventEmitter} from '@angular/core';

WHEWWWWW!

## Pipes

Pipes are Angular methods that provide some text/number/date formatting for us to help us achieve different display objectives.  We can use them to change caseing of strings, pad or round off numbers, give short or long formatted dates, display json etc.  They generally work by putting a pipe then the name of the pipe and and arguments it takes within an interpolation variable:

    {{name | lowercase}}    {{name | uppercase}}    {{message | titlecase}}     {{name | slice:3:8}}

In the case of slice, it is like doing a string.Substring() method in C# where you can supply just one argument and it will give you from that index to the end or you can supply two arguments, one for the starting index and one for the ending index.

    {{5.678 | number:'1.2-3'}}  {{5.678 | number:'3.4-5'}}  {{5.678 | number:'4.1-2'}}

In each case the first digit within the single quotes will be the number of digits before the decimal, the second number will be the minimum number of places after the decimal, the third will be the maximum number of places after. So above yields these results:

    5.678                       005.6780                    0005.68

There are also pipes for currency, json (helpful if you property contains an object), date:

    {{date | date}}                             {{date | date:'short'}}       {{date | date:'shortDate'}}

    Tue Oct 23 2018 14:13:35 EST etc....        10/23/2018 2:13 PM              10/23/2018

https://angular.io/api?type=pipe


## Services

Let's say we are going to have multiple child components within an app that all need to access the same data.  We could copy that data into an array or object within each of the child components, but this violates DRY (Don't Repeat Yourself) and SRP (Single Responsibility Principle) - our components are supposed to be responsible for creating views and handling user interaction with those views, not holding or supplying data.  That's a good reason to create a service, which is a class that exists specifically for the purpse of supplying data to other components, sending and retrieving data from a database, and possibly conducting business logic using our data that should not be a part of the view components themselves.  We will create these files as Service.serice.ts and we will use Angular's Dependency Injection framework in order to provide our services to componenets.

## Dependency Injection in Angular

The thing to remember here is that a class should receive its dependencies from an outside source rather than instantiating instances of other classes within itself.  We inject the dependencies into the constructor as parameters so that the class has everything it needs as soon as it is instantiated.  When using Angular DI, our process will become:

    1) Define the service class
    2) Register the service with an injector
    3) Declare the service as a dependency in all components that need it

## Creating Services

Start with the angular CLI and put in the command "ng g s <nameOfService>" which generates a .ts file for your service with a basic service template.  We can add a method to this service class that returns some data in any number of ways - to start we can just hardcode an array of objects and have the method return that array.  Later on we can get into using an api call to bring back an array of objects from a database so that our service is truly dynamic. We also want to declare a property that will hold the data we bring back using our service within any class that we inject the service into.  An example could be "public data = [];". Once we inject the service into our class constructor this array will be filled with the array that our service method returns.

Next we need to register the service with the Injector.  It can be registered in several places, including within the class itself, but if we do that then only that class and that class's children will have access to the service.  However, if we go up to the app.module then all of our components and their children will have access.  So we do this by going to the app.module.ts file and adding the name of the service to the "providers" array within the module metadata ( providers: [nameOfService]).  We also need to add it to the imports list at the top of the class file (import { nameOfService } from './nameOf.service';). NOTE that the convention is that if you name the service file "data" then the .ts file will be data.service.ts and the class name within the .ts file will be DataService, so our providers list will include "DataService" and our import reference will be { DataService } from './data.service'.

Finally, we will declare the service as a dependency of the class we want to inject it into by putting it into the constructor:

    constructor(private _dataService: DataService) {}

This essentially reads as injecting the local variable _dataService of type DataService into our constructor. From here we need to call the method we defined within the service class inside our ngOnInit lifecycle hook within the class:

    ngOnInit() { 
        this.data = this._dataService.getData(); 
    }

Something to note about the service class typescript file is that your need the @injectable decorator at the top of this class in order to allow for a service to rely on other services, ie to have dependencies itself.  If you don't have that decorator you will not be able to inject other services into your service class.

## https://angular.io/api?type=pipe