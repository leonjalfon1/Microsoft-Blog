<strong>Note:</strong> This is the fourth part in a series of posts <a href="https://blogs.microsoft.co.il/leonj/2017/11/19/getting-started-with-google-container-builder">Getting Started with Google Container Builder</a>

<hr />
&nbsp;

<h1>Configure CD to Kubernetes</h1>

In this part we will configure the build to deploy the application to our Kubernetes cluster after each commit (CD).

&nbsp;

• Add a new kubectl step in the <strong><em>cloudbuild.yaml</em></strong> file to replace the image used by kubernetes to run the application:

[code language="text" highlight="6,7,8,9,10"]
steps:
- name: 'gcr.io/cloud-builders/npm'
  args: ['install']
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/sdp-demo-app:$SHORT_SHA', '.' ]
- name: 'gcr.io/cloud-builders/kubectl'
  args: ['set', 'image', 'deployment/demo-app', 'demo-app=gcr.io/$PROJECT_ID/sdp-demo-app:$SHORT_SHA']
  env:
  - 'CLOUDSDK_COMPUTE_ZONE=europe-west1-c'
  - 'CLOUDSDK_CONTAINER_CLUSTER=demo-cluster'
images:
- 'gcr.io/$PROJECT_ID/sdp-demo-app'
tags:
- 'demo'
- 'nodejs'
[/code]

&nbsp;

• Change the application message to test that the CD is working. For this edit the get main function in the "server.js" file to change the message from <strong><em>Hello World!</em></strong> to <strong><em>Hello New World!</em></strong>:

[code language="text" highlight="6,7,8"]
var express = require('express');
var app = express();
var port = 3000;
var primes = require('./primes.js');

app.get('/', function(req, res){
    res.status(200).send('Hello New World!');
})

app.get('/isPrime/:number', function(req, res){
    res.status(200).send(primes.isPrime(req.params.number));    
})

exports.stop = function(){
    server.close();
}
var server = app.listen(port, function(){
  console.log("Express server listening on port %d in %s mode", port, app.settings.env);
});
[/code]

&nbsp;

• After commit and push the changes a new build should be triggered in the <strong><em>build history</em></strong> view:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/4-ConfigureCDToKubernetes/Images/4-1.png?raw=true" alt="Image" />

&nbsp;

• Wait for the build to finish and then browse to the application page

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/4-ConfigureCDToKubernetes/Images/4-2.png?raw=true" alt="Image" />

&nbsp;
&nbsp;

<strong>Continue with: </strong><a href="https://blogs.microsoft.co.il/leonj/2017/11/20/getting-started-with-google-container-builder-5">Getting Started with Google Container Builder - Part 5</a>

&nbsp;