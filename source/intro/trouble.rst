.. _intro-trouble:

============================
Troubleshooting
============================

Some common issues:

Warning: setState(...): Cannot update during an existing state transition
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You might get this warning while using React. An easy way to end up getting it is to trigger setState() within a method, such as render(). Sometimes this can happen indirectly. One way to cause the warning is call a method instead of binding it. Example: <input onKeyPress={this.checkEnter()} />. Assuming this.checkEnter uses setState(), this code will fail. Instead, you should use <input onKeyPress={this.checkEnter} /> as that will bind the method correctly without calling it.


Project Fails to Compile
^^^^^^^^^^^^^^^^^^^^^^^^^^^
Even though everything should work in theory, sometimes version ranges can bite you, despite SemVer. If some core package breaks, let's say babel, and you happen to execute npm i in an unfortunate time, you may end up with a project that doesn't compile.

A good first step is to execute npm update. This will check out your dependencies and pull the newest matching versions into your SemVer declarations. If this doesn't fix the issue, you can try to nuke node_modules (rm -rf node_modules) from the project directory and reinstall the dependencies (npm i). Alternatively you can try to explicitly pin some of your dependencies to specific versions.

Often you are not alone with your problem. Therefore, it may be worth your while to check out the project issue trackers to see what's going on. You can likely find a good workaround or a proposed fix there. These issues tend to get fixed fast for popular projects.


Warning: React attempted to reuse markup in a container but the checksum was invalid
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can get this warning through multiple means. Common causes below:

	* You tried to mount React multiple times to the same container. Check your script loading and make sure your application is loaded only once.
	* The existing markup on your template doesn't match the one rendered by React. This can happen especially if you are rendering the initial markup through a server.