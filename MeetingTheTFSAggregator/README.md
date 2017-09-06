The TFS Aggregator is undoubtedly one of the most known and most useful tools for TFS. This comes to fill all the needs that can’t be covered using the traditional work item rules. Basically allows you to execute your own rules after each save.

&nbsp;

<strong>Installation and Deployment</strong>

The TFS Aggregator is an open source project stored in <a href="https://github.com/tfsaggregator/tfsaggregator">https://github.com/tfsaggregator/tfsaggregator</a>. You can download the code and deploy it by yourself or simply use the installer provided by the project. The installer will detect your TFS version and install for you the console app and the plugin.

&nbsp;

<strong>How the Plugin Works?</strong>

A TFS Plugin is a dll installed under “C:\Program Files\Microsoft Team Foundation Server 14.0\Application Tier\Web Services\bin\Plugins” which is able to catch a variety of events occurring in TFS and execute an action. The TFS Aggregator catch the event “WorkItemChangedEvent” which occurs each time that work item is saved and execute the actions defined in the policies file.

&nbsp;

<strong>What is the Console App?</strong>

The TFS Aggregator console app is an application developed to simulate the behavior of the aggregator plugin (execute the rules defined in the policies file when a work item is updated). It’s very useful for test the policies file before deploying it to the plugins folder.

&nbsp;

<strong>How to Configure the Policies File</strong>

The file is composed of 3 sections: configuration section, rules section and policies section.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MeetingTheTFSAggregator/Images/KnowingTheTFSAggregator1.png?raw=true" alt="KnowingTheTFSAggregator1.png" />

&nbsp;

The <u>configuration section</u> is used to indicate the aggregator how it should be executed:
<ul>
 	<li>rateLimiting: aggregator “time out”, to avoid infinite loops</li>
 	<li>logging: logging type</li>
 	<li>script: language used to write the rules</li>
 	<li>authentication: save the changes using the same user or as the service user (impersonate)</li>
 	<li>server: TFS server url (useful in low balancer environments)</li>
</ul>
<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MeetingTheTFSAggregator/Images/KnowingTheTFSAggregator2.png?raw=true" alt="KnowingTheTFSAggregator2.png" />

&nbsp;

In the <u>rules section</u> is where the actions to be executed are indicated. The rules are written directly in programming language (usually in C#). There are slight changes in the syntax but they are quite intuitive.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MeetingTheTFSAggregator/Images/KnowingTheTFSAggregator3.png?raw=true" alt="KnowingTheTFSAggregator3.png" />

&nbsp;

Regarding to the <u>policies section</u>, this is the place where you specify the scope in which each rule is applied. Each policy defines a single scope but may contain several rules.

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MeetingTheTFSAggregator/Images/KnowingTheTFSAggregator4.png?raw=true" alt="KnowingTheTFSAggregator4.png" />

&nbsp;

<strong>How to Write a Rule</strong>

The rule header contains the rule name (must be unique because is used as id) and can contains two different attributes:
<ul>
 	<li>appliesTo: Indicate which work item types will be affected by the rule</li>
 	<li>hasField: Indicate that the rules will be applied to work items types that contains the specified field</li>
</ul>
The rule content specify the action to be executed when the rule is triggered. This action is written in programing language (usually C#) and must be inside of the CDATA element:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/MeetingTheTFSAggregator/Images/KnowingTheTFSAggregator5.png?raw=true" alt="KnowingTheTFSAggregator5.png" />

&nbsp;

Regarding to the code, if you know the C# TFS API will be quite simple for you understand the syntax. Basically is the same thing but the Aggregator provides a set of objects and methods to help you with the hard work.

&nbsp;

Aggregator Main Objects:
<ul>
 	<li>Self: contain the work item which trigger the aggregator</li>
 	<li>Fields: contain a list with all the work item fields</li>
 	<li>Store: contain the object store (WorkItemStore in the C# TFS API)</li>
 	<li>Logger: contain the method log used to write logs</li>
 	<li>Library: contain two useful methods: SendEmail and GetEmailAddress</li>
</ul>
&nbsp;

Aggregator Main Methods (Note: I will use the work item “self” to show the methods but any work item object contains this methods):
<ul>
 	<li>Fields: can be used to retrieve all the fields or a specific one using: self.Fields[“Title”]</li>
 	<li>Parent: retrieve the work item parent</li>
 	<li>Children: retrieve a list with the work item childs</li>
 	<li>HasParent: retrieve true or false</li>
 	<li>HasChildren: retrieve true or false</li>
 	<li>AddWorkItemLink: add a link</li>
 	<li>AddHyperlink: add a hiperlink</li>
 	<li>LastRevision: retrieve the a work item with the values before the last save</li>
 	<li>MakeNewWorkItem: create a new work item</li>
 	<li>GetGlobalList: retrieve the values of the specified Global List</li>
</ul>
Note: there is a very good examples and explanations in the official documentation <a href="https://tfsaggregator.github.io/using/">https://tfsaggregator.github.io/using/</a>

&nbsp;

<strong>How to Write a Policy</strong>

Write a policy if a simple thing. Just specify the scope and indicate which rules should be applied in this scope. The policy header contains the attribute name (must be unique because is used as id) and its elements are the following:
<ul>
 	<li>collectionScope: contains the collection affected by this policy</li>
 	<li>projectScope: contains the team project (or team projects) affected by this policy</li>
 	<li>ruleRef: specify a rule to be executed (you can add several rules to the same policy)</li>
</ul>
&nbsp;

<strong>How Can I Test My Policies Using The Console App?</strong>

The aggregator console app allows you to execute a specified policies file to a specific work item by running the following command:

"C:\Program Files\TFS Aggregator\bin\TFSAggregator2.ConsoleApp.exe" -f &lt;PoliciesFile&gt; -c &lt;CollectionUrl&gt; -p &lt;TeamProject&gt; -n &lt;WorkItemId&gt;

For example:

"C:\Program Files\TFS Aggregator\bin\TFSAggregator2.ConsoleApp.exe" -f "C:\Users\myuser\Desktop\TFSAggregator2.ServerPlugin.policies" -c http://tfsserver:8080/tfs/DefaultCollection -p MyProject -n 859

&nbsp;

<strong>How to Deploy My Policies?</strong>

After test your policies file using the console app, you will be ready to deploy it as a plugin (to run after each save). To implement the new policies file just paste it in the following path: “C:\Program Files\Microsoft Team Foundation Server 14.0\Application Tier\Web Services\bin\Plugins”

&nbsp;

<strong>Notes</strong>
<ul>
 	<li>When a rule is executed, the changes are not saved immediately. They are saved only at the end of the execution of all the rules (the aggregator uses a bulk save instead of several single saves)</li>
 	<li>After the Aggregator is executed it will run again (but it’s logical, after all this is executed after each save no?)</li>
 	<li>Uses the necessary conditions to avoid “infinite loops” (write the rules with this in mind)</li>
 	<li>You can find a sample policies in the following path “C:\Program Files\TFS Aggregator\Samples”</li>
 	<li>Check the official documentation here: <a href="https://tfsaggregator.github.io/intro/">https://tfsaggregator.github.io/intro/</a></li>
 	<li>There is an option to use the TFS Aggregator as web service (but this version is still in beta)</li>
</ul>