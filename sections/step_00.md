


You are now ready to build the AngularJS phonecat app. In this step, you will become familiar
with the most important source code files, learn how to start the development servers bundled with
angular-seed, and run the application in the browser.


In `angular-phonecat` directory, run this command:

```
git checkout -f step-0
```


This resets your workspace to step 0 of the tutorial app.

You must repeat this for every future step in the tutorial and change the number to the number of
the step you are on. This will cause any changes you made within your working directory to be lost.

If you haven't already done so you need to install the dependencies by running:

```
npm install
```

To see the app running in a browser, open a *separate* terminal/command line tab or window, then
run `npm start` to start the web server. Now, open a browser window for the app and navigate to
<a href="http://localhost:8000/app/" target="_blank">`http://localhost:8000/app/`</a>

Note that if you already ran the master branch app prior to checking out step-0, you may see the cached
master version of the app in your browser window at this point. Just hit refresh to re-load the page.

You can now see the page in your browser. It's not very exciting, but that's OK.

The HTML page that displays "Nothing here yet!" was constructed with the HTML code shown below.
The code contains some key Angular elements that we will need as we progress.

__`app/index.html`:__


```html
<!doctype html>
<html lang="en" ng-app>
<head>
  <meta charset="utf-8">
  <title>My HTML File</title>
  <link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.css">
  <link rel="stylesheet" href="css/app.css">
  <script src="bower_components/angular/angular.js"></script>
</head>
<body>

  <p>Nothing here {{'yet' + '!'}}</p>

</body>
</html>
```



## What is the code doing?

**`ng-app` directive:**

    <html ng-app>

  The `ng-app` attribute represents an Angular directive named `ngApp` (Angular uses
  `spinal-case` for its custom attributes and `camelCase` for the corresponding directives
  which implement them).
  This directive is used to flag the html element that Angular should consider to be the root element
  of our application.
  This gives application developers the freedom to tell Angular if the entire html page or only a
  portion of it should be treated as the Angular application.

**AngularJS script tag:**

    <script src="bower_components/angular/angular.js">

  This code downloads the `angular.js` script which registers a callback that will be executed by the
browser when the containing HTML page is fully downloaded. When the callback is executed, Angular
looks for the ngApp directive. If
Angular finds the directive, it will bootstrap the application with the root of the application DOM
being the element on which the `ngApp` directive was defined.

**Double-curly binding with an expression:**

    Nothing here {{'yet' + '!'}}

  This line demonstrates two core features of Angular's templating capabilities:

* a binding, denoted by double-curlies `{{ }}`
* a simple expression `'yet' + '!'` used in this binding.

  The binding tells Angular that it should evaluate an expression and insert the result into the
  DOM in place of the binding. Rather than a one-time insert, as we'll see in the next steps, a
  binding will result in efficient continuous updates whenever the result of the expression
  evaluation changes.

  Angular expression is a JavaScript-like code snippet that is
  evaluated by Angular in the context of the current model scope, rather than within the scope of
  the global context (`window`).

  As expected, once this template is processed by Angular, the html page contains the text:
  "Nothing here yet!".

## Bootstrapping AngularJS apps

Bootstrapping AngularJS apps automatically using the `ngApp` directive is very easy and suitable
for most cases. In advanced cases, such as when using script loaders, you can use the
imperative / manual way to bootstrap the app.

There are 3 important things that happen during the app bootstrap:

1. The injector that will be used for dependency injection is created.

2. The injector will then create the root scope that will
   become the context for the model of our application.

3. Angular will then "compile" the DOM starting at the `ngApp` root element, processing any
   directives and bindings found along the way.


Once an application is bootstrapped, it will then wait for incoming browser events (such as mouse
click, key press or incoming HTTP response) that might change the model. Once such an event occurs,
Angular detects if it caused any model changes and if changes are found, Angular will reflect them
in the view by updating all of the affected bindings.

The structure of our application is currently very simple. The template contains just one directive
and one static binding, and our model is empty. That will soon change!

<img src="https://raw.githubusercontent.com/outlearn-content/angular/master/img/tutorial/tutorial_00.png">


## What are all these files in my working directory?


Most of the files in your working directory come from the angular-seed project which
is typically used to bootstrap new Angular projects. The seed project is pre-configured to install
the angular framework (via `bower` into the `app/bower_components/` folder) and tools for developing
a typical web app (via `npm`).

For the purposes of this tutorial, we modified the angular-seed with the following changes:

* Removed the example app
* Added phone images to `app/img/phones/`
* Added phone data files (JSON) to `app/phones/`
* Added a dependency on [Bootstrap](http://getbootstrap.com) in the `bower.json` file.



# Experiments

* Try adding a new expression to the `index.html` that will do some math:

          <p>1 + 2 = {{ 1 + 2 }}</p>

<!-- @task, "hasDeliverable" : false, "text" : "Add a new expression with some math."-->

# Summary

Now let's go to step 1 and add some content to the web app.




[angular-seed]: https://github.com/angular/angular-seed
