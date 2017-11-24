<h1>Introduction</h1>
<hr />

In this series of posts we will learn how to store our application in GCP and configure a CI/CD pipeline within Google Cloud Platform using Google Container Builder.

So let's review what Google Container Builder is, let's see what we are going to do and let's start!

&nbsp;


<h1>Google Container Builder</h1>
<hr />

Container Builder  is a tool that uses Docker to execute builds and optionally, to create Docker containers. At first sight it seems to be a tool to create containers but it's rater a tool that uses containers to perform builds.

To define the build process, Google Container Builder uses <strong><em>build requests</em></strong>. A build request configuration file is a <strong><em>YAML</em></strong> or <strong><em>JSON</em></strong> document which describes the build process.

Container Builder offers several supported build steps that you can use and also allows you to create your own custom build steps.

Let's see the example below:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/0-Introduction/Images/0-1.png?raw=true" alt="Image" />

1- The <strong><em>npm builder</em></strong> is used to run <strong><em>"npm install"</em></strong>
2- The <strong><em>ubuntu builder</em></strong> is used to run <strong><em>"bash ./myscript.bash"</em></strong>
3- The <strong><em>docker builder</em></strong> is used to run <strong><em>"docker build -t gcr.io/nodejs-demo ."</em></strong>
4- The created container is <strong><em>pushed to the container registry</em></strong>

Note that Google Container Builder support <strong><em>"substitutions"</em></strong> (like <strong><em>$PROJECT_ID</em></strong> in the example above). They are useful for setting variables whose value isn't known until runtime (built-in substitutions), or for re-using an existing build request with different variable values.

For more information about Google Container Builder <a href="https://cloud.google.com/container-builder/docs/">read the documentation</a>

&nbsp;

<h1>The Tutorial</h1>
<hr />

In this tutorial we will build and deploy our Nodejs application using Google Container Builder.

&nbsp;

<h1>The Application</h1>
<hr />

You can find the application used in this example in <a href="https://github.com/leonjalfon1/nodejs-demoapp">GitHub</a>

The application show a basic <strong><em>"Hello World"</em></strong> message and have a basic function to <strong><em>determine if a number is prime or not</em></strong>.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/0-Introduction/Images/0-2.png?raw=true" alt="Image" />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/0-Introduction/Images/0-3.png?raw=true" alt="Image" />

The folder <strong><em>"spec"</em></strong> contains the application tests which are run using the jasmine-node module.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/0-Introduction/Images/0-4.png?raw=true" alt="Image" />

The application <strong><em>Dockerfile</em></strong> is very simple, use node as a base image, copy the application files, download the application dependencies and run the application in the port 3000.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/0-Introduction/Images/0-5.png?raw=true" alt="Image" />

&nbsp;

<h1>The Workflow</h1>
<hr />

This series of post will guide you step by step to implement the pipeline below: 

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/0-Introduction/Images/0-6.png?raw=true" alt="Image" />

1) Mirror GitHub with Google Source Repositories
2) Configure basic CI to create Docker Images and store them in Google Container Registry
3) Configure GKE to deploy the application
4) Configure CD to Kubernetes
5) Create custom build step to run tests
6) Add build notifications using cloud functions

&nbsp;
&nbsp;

Let's Start!

<strong>Continue with: </strong><a href="https://blogs.microsoft.co.il/leonj/2017/11/20/getting-started-with-google-container-builder-1">Getting Started with Google Container Builder - Part 1</a>

&nbsp;
