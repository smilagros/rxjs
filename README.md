# Rxjs
RxJS (Reactive Extensions for JavaScript) is a library for transforming, composing, and querying streams of data. It brings the concept of reactive programming to the web. The library allows you to work with asynchronous data streams with ease. Asynchronous data streams can be described in the following way:

Asynchronous: Callback functions are registered to be invoked when results are available. We’re able to wait for data to become available without blocking the application.
Data Streams: Sequences of data made available over time. You don’t need all the information to be present in order to start using them.

Observables are just a representation of any set of values over any amount of time. This is the most basic building block of RxJS.
RxJS is part of the ReactiveX project. The project’s website can be found at http://reactivex.io/


## Installing Dependencies
To get started with our demo project we first need to create a new project folder:

	$ mkdir rxjs-test

Next, change into that newly created folder by executing:

	$ cd rxjs-test
Now we’re using NPM (Node.js Package Manager) to create a new package.json file inside of our project folder:

	$ npm init -y
If you haven’t installed Node.js (and NPM) already on your system you first need to go to https://nodejs.org/first and follow the steps which are needed to install Node.js and NPM for your specific platform.
Next we need to add and install a few dependencies by using NPM again in the following way:

	$ npm install rxjs webpack webpack-dev-server typescript ts-loader

Here we’re installing Webpack, TypeScript, the corresponding TypeScript loader for Webpack, and the Webpack development web server.
Webpack is a module bundler for modern JavaScript applications. When Webpack processes your application, it internally builds a dependency graph which maps every module your project needs and generates one or more bundles.
TypeScript is an open-source programming language developed and maintained by Microsoft. It is a strict syntactical superset of JavaScript, and adds optional static typing to the language.
We also need to make sure that the Webpack CLI (Command Line Interface) is being installed as a development dependencies:

	$ npm install webpack-cli --save-dev

## Adding Script To Package.JSON

Let’s add a script with name start to the scripts section in package.json:

	"scripts": {
		"start": "webpack-dev-server --mode development"
	  },
  
This script is executing the webpack-dev-server command in development mode to start up the Webpack development web server. Later on we’re able to execute the script by using the NPM command in the following way:

	$ npm run start

## Setting Up Webpack

Next, let’s add the Webpack configuration to our project. Therefore create a new file webpack.config.js in the root project folder and insert the following configuration code:

	const path = require('path');
	module.exports = {
		entry: './src/index.ts',
		devtool: 'inline-source-map',
		module: {
		  rules: [
			{
			  test: /\.tsx?$/,
			  use: 'ts-loader',
			  exclude: /node_modules/
			}
		  ]
		},
		resolve: {
		  extensions: [ '.tsx', '.ts', '.js' ]
		},
		output: {
		  filename: 'bundle.js',
		  path: path.resolve(__dirname, 'dist')
		}
	};

## Configuring TypeScript

Furthermore we need to include the TypeScript compiler configuration in the project as well. To do so create a new file tsconfig.json and insert the following JSON code which contains configuration properties for the TypeScript compiler:

	{
		"compilerOptions": {
		  "outDir": "./dist/",
		  "sourceMap": true,
		  "noImplicitAny": true,
		  "module": "es6",
		  "moduleResolution": "node",
		  "target": "es6",
		  "allowJs": true,
		  "lib": [
			"es2017",
			"dom"
		  ]
		}
	}

## Creating An Index.html File

Next, let’s create the index.html file inside of our project folder:

	<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1.0">
		<meta http-equiv="X-UA-Compatible" content="ie=edge">
		<title>RxJS Demo</title>
		<style>
			body { font-family: 'Arial'; background: lightgray }
			ul { list-style-type: none; padding: 20px; }
			li { padding: 15px; background: lightcoral; margin-bottom: 10px; }
		</style>
	</head>
	<body>
		<h1>RxJS Demo</h1>
		<div>
			<ul id="list"></ul>
		</div>
		<script src="/bundle.js"></script>
	</body>

	</html>


A few things to note here:
The body section of this HTML documents contains a <ul> element with ID list. This element will be used to output information about the Observable to the user.
The file bundle.js (which is the output of the Webpack build process) is included via <script> tag at the end of the body section.

## Add The TypeScript Code

Now that the project setup is ready, the TypeScript and Webpack configuration is in place, and the HTML document has been created, we’re able to focus on the TypeScript code next.
Insider the project folder create a new subfolder src. Insert that newly created folder create a new file index.ts and insert the following code:

	import { Observable } from 'rxjs';
	var observable = Observable.create((observer:any) => {
		observer.next('Hello World!');
		observer.next('Hello Again!');
		observer.complete();
		observer.next('Bye');
	})
	observable.subscribe(
		(x:any) => logItem(x),
		(error: any) => logItem ('Error: ' + error),
		() => logItem('Completed')
	);
	function logItem(val:any) {
		var node = document.createElement("li");
		var textnode = document.createTextNode(val);
		node.appendChild(textnode);
		document.getElementById("list").appendChild(node);
	}

First of all we’re importing Observable from the rxjs library. By using method Observable.create we’re creating an instance of Observable and storing it in observable.

As an argument we’re passing in a callback method which gives us access to an Observer object. Now we’re able to use the Observer methods next, complete and error to create notifications of the corresponding types. In this example we’re calling the next method two times to send out notifications with string values. Then the complete method is called to send out the complete notification. After having send out the complete notification we’re calling the next method once again. The expectation here is that no new notification will be raised because the Observable is already completed.

The register callback methods which are invoked if a notification of a certain type is raised are registered by calling the subscribe method of the Observable. We’re able to register up to three callback methods for notification types next, error and complete.
Inside the callback methods we’re making use of a custom helper method logItem. This methods is updating the output by adding new list items to the list element.

Now we’re ready to start of the development web server by using the following command:
	$ npm run start
The web application should now be accessible via URL http://localhost:8080 and you should be able to see the following result in the browser:

As expected the output contains three items:
	Two items printing out the message from the first two next notification
	One item outputting the complete notification
	The last next notification (raised after the Observable was completed) is not emitted and therefore not contained in the output.
