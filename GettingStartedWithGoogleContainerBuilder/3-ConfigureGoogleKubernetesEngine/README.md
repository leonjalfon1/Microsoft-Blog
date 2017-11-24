<strong>Note:</strong> This is the third part in a series of posts <a href="https://blogs.microsoft.co.il/leonj/2017/11/19/getting-started-with-google-container-builder">Getting Started with Google Container Builder</a>

<hr />
&nbsp;

<h1>Configure Google Kubernetes Engine and deploy the  application</h1>

In this part we will configure a simple Kubernetes cluster to use it to deploy the application.

Variables that will be used in this demo:

[code language="text" gutter="false"]
[PROJECT_ID]: sdp-demo-185120
[YOUR_ZONE]: europe-west1-c
[YOUR_REGION]: europe-west1
[CLUSTER_NAME]: demo-cluster
[/code]

&nbsp;

• Open the <strong><em>shell console</em></strong> and set the following variables:

[code language="text" gutter="false"]
gcloud config set project [PROJECT_ID]
gcloud config set compute/zone [YOUR_ZONE]
gcloud config set compute/region [YOUR_REGION]
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-1.png?raw=true" alt="Image" />

&nbsp;

• Confirm that the variables were set

[code language="bash" gutter="false"]
gcloud config list
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-2.png?raw=true" alt="Image" />

&nbsp;

• Create a kubernetes cluster

[code language="bash" gutter="false"]
gcloud container clusters create [CLUSTER_NAME] --num-nodes 3
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-3.png?raw=true" alt="Image" />

&nbsp;

• Deploy the image created before to the cluster (manually)

[code language="bash" gutter="false"]
kubectl run demo-app --image=gcr.io/[PROJECT_ID]/sdp-demo-app:[IMAGE_TAG]
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-4.png?raw=true" alt="Image" />

&nbsp;

• Verify that the pods are running

[code language="bash" gutter="false"]
kubectl get pods
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-5.png?raw=true" alt="Image" />

&nbsp;

• Expose the demo-app as an external service

[code language="bash" gutter="false"]
kubectl expose deployment demo-app --port=80 --target-port=3000 --type=LoadBalancer
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-6.png?raw=true" alt="Image" />

&nbsp;

• Find the network load balancer address

[code language="bash" gutter="false"]
kubectl get service demo-app
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-7.png?raw=true" alt="Image" />

&nbsp;

• Browse to the deployed site

[code language="bash" gutter="false"]
http://EXTERNAL_IP
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-8.png?raw=true" alt="Image" />

&nbsp;

• Scale the application

[code language="bash" gutter="false"]
kubectl scale deployment demo-app --replicas=3
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-9.png?raw=true" alt="Image" />

&nbsp;

• Give Container Builder Service Account (container.developer) role access to your Container Engine clusters

[code language="bash" gutter="false"]
PROJECT=''$(gcloud projects describe \
    $(gcloud config get-value core/project -q) --format='get(projectNumber)')''

gcloud projects add-iam-policy-binding $PROJECT \
    --member=serviceAccount:$PROJECT@cloudbuild.gserviceaccount.com \
    --role=roles/container.developer
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-10.png?raw=true" alt="Image" />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/GettingStartedWithGoogleContainerBuilder/3-ConfigureGoogleKubernetesEngine/Images/3-11.png?raw=true" alt="Image" />

&nbsp;
&nbsp;

<strong>Continue with: </strong><a href="https://blogs.microsoft.co.il/leonj/2017/11/20/getting-started-with-google-container-builder-4">Getting Started with Google Container Builder - Part 4</a>

&nbsp;