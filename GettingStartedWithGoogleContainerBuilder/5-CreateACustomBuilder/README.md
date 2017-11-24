<strong>Note:</strong> This is the fifth part in a series of posts <a href="https://blogs.microsoft.co.il/leonj/2017/11/19/getting-started-with-google-container-builder">Getting Started with Google Container Builder</a>

<hr />
&nbsp;

<h1>Create a custom builder and add a build step to run the tests</h1>

In this part we will create a custom builder and use it to add a build step to test the application. For this purpose we will create a custom build step to run npm using the jasmine module.

&nbsp;

• Open the <strong><em>Cloud Shell</em></strong> and create a folder to store the custom builder source code

[code language="text" gutter="false"]
mkdir ~/npm-jasmine-node
cd ~/npm-jasmine-node
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-1.png?raw=true" alt="Image" />

&nbsp;

• Create a <strong><em>Dockerfile</em></strong> to use the <strong><em>npm</em></strong> builder as the base image, install the <strong><em>jasmine-node</em></strong> module in it and set <strong><em>npm</em></strong> as entrypoint.

[code language="text" title="Dockerfile"]
FROM gcr.io/cloud-builders/npm
RUN npm install -g jasmine-node
ENTRYPOINT ["npm"]
[/code]

I will use VIM to create the file (for more info: <a href="https://help.dreamhost.com/hc/en-us/articles/115006413028-Creating-and-editing-a-file-via-SSH">How to use the VIM tool?</a>)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-2.png?raw=true" alt="Image" />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-3.png?raw=true" alt="Image" />

&nbsp;

• Create a <strong><em>cloudbuild.yaml</em></strong> file to build and deploy the image to container registry

[code language="text" title="cloudbuild.yaml"]
steps:
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/npm-jasmine-node', '.' ]
images:
- 'gcr.io/$PROJECT_ID/npm-jasmine-node'
tags:
- 'builder'
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-4.png?raw=true" alt="Image" />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-5.png?raw=true" alt="Image" />

&nbsp;

• Trigger the build manually running the following command in the <strong><em>Cloud Shell</em></strong>

[code language="text" gutter="false"]
gcloud container builds submit --config cloudbuild.yaml .
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-6.png?raw=true" alt="Image" />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-7.png?raw=true" alt="Image" />

&nbsp;

• Verify that the image was successfully triggered

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-8.png?raw=true" alt="Image" />

&nbsp;

• Check that the image was pushed to container registry

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-9.png?raw=true" alt="Image" />

&nbsp;

• Add a step to the <strong><em>cloudbuild.yaml</em></strong> of the <strong><em>demo application</em></strong> to use the custom builder to run the tests

[code language="text" highlight="4,5"]
steps:
- name: 'gcr.io/cloud-builders/npm'
  args: ['install']
- name: 'gcr.io/$PROJECT_ID/npm-jasmine-node'
  args: ['test']
- name: 'gcr.io/cloud-builders/docker'
  args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/sdp-demo-app:$SHORT_SHA', '.' ]
images:
- 'gcr.io/$PROJECT_ID/sdp-demo-app'
tags:
- 'demo'
- 'nodejs'
[/code]

&nbsp;

• Commit and push the changes, then open the <strong><em>build log</em></strong> and check that the tests run

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/5-CreateACustomBuilder/Images/5-10.png?raw=true" alt="Image" />

Note: If you already created a custom builder, why not share it with the <a href="https://github.com/GoogleCloudPlatform/cloud-builders-community">community</a>?

&nbsp;
&nbsp;

<strong>Continue with: </strong><a href="https://blogs.microsoft.co.il/leonj/2017/11/21/getting-started-with-google-container-builder-6">Getting Started with Google Container Builder - Part 6</a>

&nbsp;