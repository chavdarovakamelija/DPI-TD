.. _intro-new:

=================================
First Hello World application
=================================

Install create react app
============================
Setting up productive react development environment would need to configure tools like babel, webpack which are complex to configure for a newbie in react world. There are several tools that help to alleviate this problem out of which create react app is the easiest and finest tool with production grade configurations pre-built. The goodness of this tool is, it does not lock in and allows you to alter the configuration by ejecting the create react app.  


We will install create-react-app using npm. On terminal run the install command shown below::

	npm install -g create-react-app
	
.. image:: ../images/prim.jpg
    :width: 500px
    :align: center
    :height: 200px
    :alt: alternate text
	
	
On successful installation you should see the output like above (note your create-react-app version may be different by the time you run this install command)


Test create-react-app 
============================

To test the create-react-app, run below command::

	create-react-app --version
	
Congratulations, you have successfully installed create-react-app



Running the first Hello World application
=============================================

Create react application using create-react-app command line using below command::

	create-react-app hello-react
	
This command creates a new folder named hello-react and creates all the files and setups the necessary libraries within this folder and makes the react project ready to be executed without any additional configuration


Once project is created change into project directory and run application using npm start command::

	npm start
	
	
npm start command starts webpack development server which in turn performs all the build process and opens a browser window and loads application url which runs at `http://localhost:3000`_ by default.

.. _http://localhost:3000: http://localhost:3000


You will see a beautiful window like below one which show you the react icon and some text.

.. image:: ../images/start.jpg
    :width: 700px
    :align: center
    :height: 200px
    :alt: alternate text
	
	
As discussed, create react app comes with great tooling, one of the productive features is webpack hot reloading, which deploys the change on live and saves developer lot of time to redeploy and reload work.

Let’s open the project and make some changes to see the experience of this great feature. We will go through the project structure to understand the importance of the file create by create react app.


You have several folders and files which were created by create-react-app. Let’s walkthrough them

**node_modules:** This folder is managed by package manager (npm) and contains all the project library dependencies. Files in this folder should not be modified as the changes made are not guaranteed to be safe as updating/installing any other library can overwrite the changes made.


**public:** This folder contains the assets that should be served publicly without any restrictions and contains files like index.html, favicon, manifest.json


**src:** This is the folder that contains all your development effort. As a react developer you spent lot of time in this folder creating components and other sources of code.


Now let’s get into the files inside this folder. To start with index.js is the starting file of this project where the execution on your code starts, this file injects App component which is exported from App.js. All the output you see in the browser is resultant on this file::

	import React, {Component } from 'react';
	import logo from './logo.svg';
	import './App.css';
	
	class App extends Component {
	render () {
	  return (
	     <div className="App">
		   <h1> Hello React </h1>
		 </div>
	  );
	 }
	}
	export default App;
	
	
Save the file and switch back to browser to see the changes deployed and loaded as discussed, this one of the features of create-react-app to tooling setup.  
With new changes your browser window should look like below

.. image:: ../images/hello.jpg
    :width: 700px
    :align: center
    :height: 200px
    :alt: alternate text


