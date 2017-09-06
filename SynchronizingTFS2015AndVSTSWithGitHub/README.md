Many times we have the need to store our project in GitHub because we want (or need) to be open source but still want to keep the capabilities offered by TFS/VSTS. Other times we need to give access to our repository to a person who can’t have (or that we don’t want to give) access to our TFS/VSTS server.

In these cases we can resolve the problem synchronizing our TFS/VSTS repository with our GitHub (public or private) repository. At first glance it may sound like a complicated process but it’s easier than you expect.

You can perform the synchronization manually (using pull/push) or automatically. For automate the process you need to create two builds for each branch, one to send the pushed changes in TFS/VSTS to GitHub and another one to send the changes pushed to GitHub to TFS/VSTS.

This method allows you to synchronize specific branches and decide if the synchronization is in both directions or in a single direction. However it must be taken into account that the synchronization can create conflicts if isn’t done in the right way.

The synchronization from GitHub to TFS on premise is the most complicated case. In TFS 2015 on premise the option to use a GitHub repository as a source repository in a build definition is not available. Also there are problems using the service hooks to connect with GitHub as external Git Repository. Nevertheless you can create a build with schedule as a trigger to run periodically a script that perform the synchronization (pull from GitHub repository and push to TFS) or use a manual trigger and run it each time that you push changes to GitHub.

If you are using TFS on premise my recommendation is to try to avoid the two way synchronization. The best approach is to remove the permissions of all users in the GitHub repository (except for the user that run the sync build) to avoid direct changes in GitHub and works against TFS only. Even so, I recommend to create the both builds (Sometimes you may want to include changes from GitHub, for example pull requests). What is extremely important is to run de sync from GitHub to TFS before push another changes in TFS.

Another option to avoid conflicts (only for TFS on premise) is create a server side plugin to run the sync build (from GitHub to TFS) before receive each push in TFS. (More info about server side plugin for Git repositories <a href="http://almsports.net/tfs-server-side-check-in-policy-for-git-repositories/1025/">here</a>)

&nbsp;

Notes:
<ul>
 	<li>The CI build is trigged only when a new commit is pushed to the repository (no when a new tag is pushed)</li>
 	<li>In this example I will synchronize the tags when they are added but not when there are removed (however you can create a script to compare the tags in the both repositories and delete the tags that doesn’t match)</li>
</ul>
&nbsp;

Here step by step guides for:
<ul>
 	<li><a href="#TFS_GITHUB">Sync from TFS/VSTS to GitHub</a></li>
 	<li><a href="#GITHUB_VSTS">Sync from GitHub to VSTS</a></li>
 	<li><a href="#GITHUB_TFS">Sync from GitHub to TFS</a></li>
</ul>
&nbsp;
<h3 id="TFS_GITHUB"><strong>Sync from TFS/VSTS to GitHub</strong></h3>
1) Create an “Empty” VNext build

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-1.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-1.png" />

&nbsp;

2) Select the Git TFS repository, the branch to synchronize and check the box “Continuous Integration”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-2.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-2.png" />

&nbsp;

3) In the section "variables" create a new variable with the GitHub credentials (user:password). Press the lock icon to hide the details.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-3.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-3.png" />

&nbsp;

4) Add 4 “Command Line” build steps

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-4.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-4.png" />

&nbsp;

5) Set the first one to run "git pull &lt;GitHubRepoUrl&gt; master"

“git pull https://$(credentials)@github.com/leonjalfon1/TFSTeamsMigratorTool.git master”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-5.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-5.png" />

&nbsp;

6) Set the second one to run "git pull --tags &lt;GitHubUrl&gt;"

“git pull --tags https://$(credentials)@github.com/leonjalfon1/TFSTeamsMigratorTool.git”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-6.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-6.png" />

&nbsp;

7) Set the third one to run "git push &lt;GitHubUrl&gt; head:master"

“git push https://$(credentials)@github.com/leonjalfon1/TFSTeamsMigratorTool.git head:master”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-7.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-7.png" />

&nbsp;

8) Set the fourth one to run "git push --tags &lt;GitHubUrl&gt;"

“git push --tags https://$(credentials)@github.com/leonjalfon1/TFSTeamsMigratorTool.git”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-8.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-8.png" />

&nbsp;

9) Save the build

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/VSTS_GITHUB/VSTS_GITHUB-9.png?raw=true" alt="VSTS_GITHUB/VSTS_GITHUB-9.png" />

&nbsp;

&nbsp;
<h3 id="GITHUB_VSTS"><strong>Sync from GitHub to VSTS</strong></h3>
1) First of all you need create a personal token. For this, click the user icon and select “Security”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-1.png?raw=true" alt="GITHUB-VSTS-1.png" />

&nbsp;

2) In the “Personal Access Token” section, click “Add”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-2.png?raw=true" alt="GITHUB-VSTS-2.png" />

&nbsp;

3) Write a description for your token (to remember for what the token is being used), the duration, the affected accounts and the authorized scopes.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-3.png?raw=true" alt="GITHUB-VSTS-3.png" />

&nbsp;

4) Copy your token (will be used in next steps) and store it in a safe place (optional).

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-4.png?raw=true" alt="GITHUB-VSTS-4.png" />

&nbsp;

5) Create a new “Empty” VNext build:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-5.png?raw=true" alt="GITHUB-VSTS-5.png" />

&nbsp;

6) Select GitHub as source repository and check the continuous integration box

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-6.png?raw=true" alt="GITHUB-VSTS-6.png" />

&nbsp;

7) Go to the variables section and add the created token as a new build variable called “token” (optional, lock to hide)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-7.png?raw=true" alt="GITHUB-VSTS-7.png" />

&nbsp;

8) Add 4 "Run command line" build steps

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-8.png?raw=true" alt="GITHUB-VSTS-8.png" />

&nbsp;

9) Configure the first one to run “git pull &lt;VSTSRepoUrl&gt; master”

“git pull https://$(token)@leonj.visualstudio.com/DefaultCollection/MyProject/_git/TFSTeamsMigratorTool master”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-9.png?raw=true" alt="GITHUB-VSTS-9.png" />

&nbsp;

10) Configure the second one to run “git pull --tags &lt;VSTSRepoUrl&gt;”

“git pull --tags https://$(token)@leonj.visualstudio.com/DefaultCollection/MyProject/_git/TFSTeamsMigratorTool”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-10.png?raw=true" alt="GITHUB-VSTS-10.png" />

&nbsp;

11) Configure the third one to run “git push &lt;VSTSRepoUrl&gt; head:master”

“git push https://$(token)@leonj.visualstudio.com/DefaultCollection/MyProject/_git/TFSTeamsMigratorTool head:master”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-11.png?raw=true" alt="GITHUB-VSTS-11.png" />

&nbsp;

12) Configure the third one to run “git push --tags &lt;VSTSRepoUrl&gt;”

“git push --tags https://$(token)@leonj.visualstudio.com/DefaultCollection/MyProject/_git/TFSTeamsMigratorTool”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB_VSTS-12.png?raw=true" alt="GITHUB-VSTS-12.png" />

&nbsp;

13) In the “Repository” section click “Manage” to add a new connection

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GitHub-VSTS-13.png?raw=true" alt="GITHUB-VSTS-13.png" />

&nbsp;

14) Click “New Service Endpoint” and select “GitHub”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB-VSTS-14.png?raw=true" alt="GITHUB-VSTS-14.png" />

&nbsp;

15) Give a connection name and authenticate using a GitHub personal token or authenticating by the web browser and click “OK”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GITHUB-VSTS-15.png?raw=true" alt="GITHUB-VSTS-15.png" />

&nbsp;

16) Go back to the previous tab and click the refresh button

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GitHub-VSTS-16.png?raw=true" alt="GITHUB-VSTS-16.png" />

&nbsp;

17) Select a connection, repository and branch (master in this example)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GitHub-VSTS-17.png?raw=true" alt="GITHUB-VSTS-17.png" />

&nbsp;

18) Save the Build

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GitHub-VSTS-18.png?raw=true" alt="GITHUB-VSTS-18.png" />

&nbsp;

Note that after configuring a continuous integration build that is linked to GitHub repository, VSTS will automatically create a webhook in GitHub to trigger the VSTS build.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_VSTS/GitHub-VSTS-19.png?raw=true" alt="GITHUB-VSTS-19.png" />

&nbsp;

&nbsp;
<h3 id="GITHUB_TFS"><strong>Sync from GitHub to TFS</strong></h3>
1) Create a new “Empty” VNext build

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-1.png?raw=true" alt="GITHUB-TFS-1.png" />

&nbsp;

2) Select your team project as repository source and select the repository and branch (master in this example)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-2.png?raw=true" alt="GITHUB-TFS-2.png" />

&nbsp;

3) In the variables section add the GitHub credentials (for private repos) and the TFS credentials

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-3.png?raw=true" alt="GITHUB-TFS-3.png" />

&nbsp;

4) Add 4 "Run command line" build steps

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-4.png?raw=true" alt="GITHUB-TFS-4.png" />

&nbsp;

5) Configure the first one to run “git pull &lt;GitHubRepoUrl&gt; master”

“git pull https://$(GitHubCredentials)@github.com/leonjalfon1/TeamsMigratorTool.git master”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-5.png?raw=true" alt="GITHUB-TFS-5.png" />

&nbsp;

6) Configure the second one to run “git pull --tags &lt;GitHubRepoUrl &gt;”

“git pull --tags https://$(GitHubCredentials)@github.com/leonjalfon1/TeamsMigratorTool.git”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-6.png?raw=true" alt="GITHUB-TFS-6.png" />

&nbsp;

7) Configure the third one to run “git push &lt;TFSRepoUrl&gt; master:master”

“git push http://$(tfscredentials)leonjalfon:8080/tfs/DefaultCollection/MyProject/_git/TFSTeamsMigratorTool master:master”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-7.png?raw=true" alt="GITHUB-TFS-7.png" />

&nbsp;

8) Configure it to run “git push --tags &lt;TFSRepoUrl&gt;”

“git push --tags http://$(tfscredentials)leonjalfon:8080/tfs/DefaultCollection/MyProject/_git/TFSTeamsMigratorTool”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-8.png?raw=true" alt="GITHUB-TFS-8.png" />

&nbsp;

9) Go to “Triggers” section, select “Scheduled” and configure when the build runs

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-9.png?raw=true" alt="GITHUB-TFS-9.png" />

&nbsp;

10) Save the build

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/SynchronizingTFS2015AndVSTSWithGitHub/Images/GITHUB_TFS/GITHUB-TFS-10.png?raw=true" alt="GITHUB-TFS-10.png" />

&nbsp;

&nbsp;