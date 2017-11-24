<strong>Note:</strong> This is the second part in a series of posts <a href="https://blogs.microsoft.co.il/leonj/2017/11/19/getting-started-with-google-container-builder">Getting Started with Google Container Builder</a>

<hr />
&nbsp;

<h1>Configure basic CI to create Docker images and store them in Google Container Registry</h1>

In this part we will create the most basic build possible, just create a docker image and store it in Google Container Registry to use it later.

&nbsp;

• Create a new file in the repository root: <strong><em>cloudbuild.yaml</em></strong>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-1.png?raw=true" alt="Image" />

&nbsp;

• Add the below to the file to use the npm builder to run <em>npm install</em> and the docker builder to build the docker image:

[code language="text"]
steps:
- name: 'gcr.io/cloud-builders/npm'
  args: ['install']
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/sdp-demo-app:$SHORT_SHA', '.' ]
[/code]

&nbsp;

• Add the image section below to configure container builder to push the created image to the container registry:

[code language="text" highlight="6,7"]
steps:
- name: 'gcr.io/cloud-builders/npm'
  args: ['install']
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/sdp-demo-app:$SHORT_SHA', '.' ]
images:
- 'gcr.io/$PROJECT_ID/sdp-demo-app'
[/code]

&nbsp;

• Finally add a tags section and within it add tag(s) to be able to filter the builds later:

[code language="text" highlight="8,9,10"]
steps:
- name: 'gcr.io/cloud-builders/npm'
  args: ['install']
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/sdp-demo-app:$SHORT_SHA', '.' ]
images:
- 'gcr.io/$PROJECT_ID/sdp-demo-app'
tags:
- 'demo'
- 'nodejs'
[/code]

&nbsp;

• Commit, push the changes and go to the <strong><em>build triggers</em></strong> section under container registry and click <strong><em>Add Trigger</em></strong> to configure the build to run after each commit

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-5.png?raw=true" alt="Image" />

&nbsp;

• Select <strong><em>Cloud source Repository</em></strong> and click <strong><em>Continue</em></strong>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-6.png?raw=true" alt="Image" />

&nbsp;

• Select the repository and click <strong><em>Continue</em></strong>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-7.png?raw=true" alt="Image" />

&nbsp;

• Configure the trigger with the information below and click <strong><em>Create Trigger</em></strong>:

[code language="text" gutter="false"]
Trigger Name: sdp-demo-build
Trigger Type: Branch
Branch (regex): .*
Build Configuration: cloudbuild.yaml
cloudbuild.yaml location: /cloudbuild.yaml
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-8.png?raw=true" alt="Image" />

&nbsp;

• Trigger the created build by click <strong><em>Run Trigger</em></strong> -&gt; <strong><em>master</em></strong>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-9.png?raw=true" alt="Image" />

&nbsp;

• Ensure the build was triggered (in the <strong><em>build history</em></strong> section under container registry) and click the <strong><em>build Id</em></strong> to see the build log

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-10.png?raw=true" alt="Image" />

&nbsp;

• Ensure the docker image was pushed to container registry (in the <strong><em>images</em></strong> section under container registry)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/2-ConfigureBasicCI/Images/2-11.png?raw=true" alt="Image" />

&nbsp;

• <strong><em>Optional:</em></strong> Commit and push new changes to see how the build is triggered automatically 

&nbsp;
&nbsp;

<strong>Continue with: </strong><a href="https://blogs.microsoft.co.il/leonj/2017/11/20/getting-started-with-google-container-builder-3">Getting Started with Google Container Builder - Part 3</a>

&nbsp;