A few days ago I was asked to move all the artifacts stored on the Artifactory server from the “C:\” drive to the “E:\” drive. I went to the official documentation but I didn't find exactly what I was looking for. There explain how to specify where to save the artifacts when configuring the server but not how to do it on an existing server. Here are the steps to follow to achieve it:

&nbsp;

1) Get the ARTIFACTORY_HOME path:

.    • Open PowerShell and run the command:

.          • Get-ChildItem Env:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Artifactory-MoveFilestoreFromOneDriveToAnother/Images/Img1.png?raw=true" alt="MoveFilestoreFromOneDriveToAnother-1.png" />

&nbsp;

2) Search for the file “binarystore.xml”

.    • In the path: $ARTIFACTORY_HOME/etc

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Artifactory-MoveFilestoreFromOneDriveToAnother/Images/Img2.png?raw=true" alt="MoveFilestoreFromOneDriveToAnother-2.png" />

&nbsp;

3) Create/Edit the “binarystore.xml” file to specify the custom filestore location

.    • Meeting the following syntax:

.          • In my case I want to store the binaries in: “E:\ArtifactoryData”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Artifactory-MoveFilestoreFromOneDriveToAnother/Images/Img3.png?raw=true" alt="MoveFilestoreFromOneDriveToAnother-3.png" />

&nbsp;

4) Update the “binarystore.xml” file

.    • Copy/Save the file in the following path:

.          • $ARTIFACTORY_HOME/etc

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Artifactory-MoveFilestoreFromOneDriveToAnother/Images/Img4.png?raw=true" alt="MoveFilestoreFromOneDriveToAnother-4.png" />

&nbsp;

5) Restart the server to apply the changes

&nbsp;

<strong>Note:</strong> After restart the server two things will happen:

1)  All the new artifacts will be stored in the new location (“E:\ArtifactoryData\” in my case)

2)  All existent artifacts will be unavailable (You will receive the following error when trying to download any artifact)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Artifactory-MoveFilestoreFromOneDriveToAnother/Images/Img5.png?raw=true" alt="MoveFilestoreFromOneDriveToAnother-5.png" />

&nbsp;

Now we need to move our existent artifacts to the new filestore to allow users download them. For this you can follow the following steps:

1)  Go to the old filestore location (by default: $ARTIFACTORY_HOME/data/filestore)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Artifactory-MoveFilestoreFromOneDriveToAnother/Images/Img6.png?raw=true" alt="MoveFilestoreFromOneDriveToAnother-6.png" />

&nbsp;

2)  Copy all the folders except “_pre” into the new filestore folder (Optional: then, remove the artifacts from the old filestore to free up disk space)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Artifactory-MoveFilestoreFromOneDriveToAnother/Images/Img7.png?raw=true" alt="MoveFilestoreFromOneDriveToAnother-7.png" />

&nbsp;

From now, all new artifacts will be stored in the new filestore, and all existing artifacts will still be available.

&nbsp;