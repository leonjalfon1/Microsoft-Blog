Being an open source project might think that would be enough with a couple of clicks to upload your plugin to Jenkins but nothing is further from reality.

To publish a plugin in Jenkins site we must go through a series of steps that are not entirely intuitive to people who are not related to development tools like Jira and GitHub.

&nbsp;

<h3>Step 1: Create a GitHub Account (or use an existent account)</h3>
1) Go to <a href="https://github.com/">https://github.com/</a> and click “Sign Up”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_1.png?raw=true" alt="PublishJenkinsPlugin_1.png" />

2) Fill the form and click “Create Account”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_2.png?raw=true" alt="PublishJenkinsPlugin_2.png" />

&nbsp;

<h3>Step 2: Create a GitHub Repository (must be public) to store your plugin source code</h3>
1) Sign In to GitHub and click “New Repository”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_3.png?raw=true" alt="PublishJenkinsPlugin_3.png" />

2) Fill the form and click “Create Repository”

--&gt; The repository must be public

--&gt; You must add a license (almost all plugins uses the MIT License)

--&gt; Initialize the repository with a README is optionally

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_4.png?raw=true" alt="PublishJenkinsPlugin_4.png" />

3) Now you can clone your new repository and start to work (or move your plugin source code)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_5.png?raw=true" alt="PublishJenkinsPlugin_5.png" />

&nbsp;

<h3>Step 3: Create an account in the Jenkins Site</h3>

1) Go to <a href="https://accounts.jenkins.io/">https://accounts.jenkins.io/</a> and click in “Create a new account”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_6.png?raw=true" alt="PublishJenkinsPlugin_6.png" />

2) Fill the form and click “Submit”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_7.png?raw=true" alt="PublishJenkinsPlugin_7.png" />

3) Click “Update your profile”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_8.png?raw=true" alt="PublishJenkinsPlugin_8.png" />

4) Then Add your GitHub ID and click “submit”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_9.png?raw=true" alt="PublishJenkinsPlugin_9.png" />

<strong>Note:</strong> I has problems using user accounts with upper case characters (avoid using them)

&nbsp;

<h3>Step 4: Request Hosting for your Plugin Source Code</h3>

1) Connect to Jenkins Jira Portal: <a href="https://issues.jenkins-ci.org/secure/Dashboard.jspa">https://issues.jenkins-ci.org/secure/Dashboard.jspa</a> and login

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_10.png?raw=true" alt="PublishJenkinsPlugin_10.png" />

2) Select your favorite language

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_11.png?raw=true" alt="PublishJenkinsPlugin_11.png" />

3) Choose an Avatar and click next

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_12.png?raw=true" alt="PublishJenkinsPlugin_12.png" />

4) Follow the “quick tour of Jira” to understand the basics of JIRA by click “Next” after each step

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_13.png?raw=true" alt="PublishJenkinsPlugin_13.png" />

5) Click “Create an Issue”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_14.png?raw=true" alt="PublishJenkinsPlugin_14.png" />

6) Select the project “Plugin Hosting Request” and click next

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_15.png?raw=true" alt="PublishJenkinsPlugin_15.png" />

7) Fill the Create Issue Form and click Create

--&gt; Component: New Plugin Hosting Request

--&gt; Summary: A short but meaningful description of your plugin

--&gt; Repository URL: The url of your github repository that contains the plugin source code

--&gt; New Repository Name: The name to give to the repository in the jenkinsci organization

--&gt; Plugin Description: Description of your plugin

--&gt; GitHub users to Authorize as Commiters: GitHub user names (including your own)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_16.png?raw=true" alt="PublishJenkinsPlugin_16.png" />

8) After you have created an issue, make sure to respond to request for additional information in a timely manner, otherwise your issue may be resolved as incomplete.

9) After the issue will be completed, your GitHub repository will be forked (cloned) into jenkinsci organization and permissions will be granted to the specified users that you gave.

&nbsp;

<h3>Step 5: Grant Permissions to publish in Jenkins site</h3>

1) Access to the following project:

--&gt; <a href="https://github.com/jenkins-infra/repository-permissions-updater">https://github.com/jenkins-infra/repository-permissions-updater</a>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_17.png?raw=true" alt="PublishJenkinsPlugin_17.png" />

2) Fork this repository by clicking the button “Fork”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_18.png?raw=true" alt="PublishJenkinsPlugin_18.png" />

3) Clone the repository locally and open the file “Permissions”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_19.png?raw=true" alt="PublishJenkinsPlugin_19.png" />

4) Create (or modify) the permissions file for your plugin

--&gt; The name of the file must met the format: plugin-&lt;pluginName&gt;.yml (lower case)

--&gt; The name parameter is the name of your plugin (this is also the “artifact id” of the maven artifact)

--&gt; The path parameter is a set of paths (usually just one) that correspond to the full maven coordinates (“group id” and “artifact id”) used for the artifact

--&gt; The developer list is the GitHub users that can publish the plugin in the Jenkins site

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_20.png?raw=true" alt="PublishJenkinsPlugin_20.png" />

5) Commit the changes in your fork repository

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_21.png?raw=true" alt="PublishJenkinsPlugin_21.png" />

6) Go to the forked repository in GitHub and click “pull requests”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_22.png?raw=true" alt="PublishJenkinsPlugin_22.png" />

7) Then click “New Pull Request”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_23.png?raw=true" alt="PublishJenkinsPlugin_23.png" />

8) Click “Create Pull Request”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_24.png?raw=true" alt="PublishJenkinsPlugin_24.png" />

9) After the pull request be accepted, the permissions to publish the plugin in the Jenkins site will be granted to all users inserted in the .yml file

&nbsp;

<h3>Step 6: Publish your plugin in the Jenkins Site</h3>

There are several ways to publish the plugin, for example using Maven or Gradle

--&gt; You can find an example using maven in the Jenkins official documentation: <a href="https://wiki.jenkins-ci.org/display/JENKINS/Hosting+Plugins#HostingPlugins-Releasingtojenkinsci.org">https://wiki.jenkins-ci.org/display/JENKINS/Hosting+Plugins#HostingPlugins-Releasingtojenkinsci.org</a>

--&gt; I will use Gradle in this example (actually I created a Jenkins job to perform the publish operation automatically)

1) Modify the file “build.gradle” to specify the publish parameters

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_25.png?raw=true" alt="PublishJenkinsPlugin_25.png" />

2) Create a file “.jenkins-ci.org” and paste it in the machine that will perform the publish in the following path C:/users/{user}/.jenkins-ci.org

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/PublishJenkinsPlugin/Images/PublishJenkinsPlugin_26.png?raw=true" alt="PublishJenkinsPlugin_26.png" />

3) Set Gradle to use Java 7

4) Run Gradle (locally or using a Jenkins Job)

&nbsp;

<h3>For More Info:</h3>
<a href="https://wiki.jenkins-ci.org/display/JENKINS/Plugin+tutorial">https://wiki.jenkins-ci.org/display/JENKINS/Plugin+tutorial</a>

<a href="https://wiki.jenkins-ci.org/display/JENKINS/Hosting+Plugins#HostingPlugins-Releasingtojenkinsci.org">https://wiki.jenkins-ci.org/display/JENKINS/Hosting+Plugins#HostingPlugins-Releasingtojenkinsci.org</a>

&nbsp;