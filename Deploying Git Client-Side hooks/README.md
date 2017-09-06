First of all let’s define what Git hook is. Like a Git documentation explain Git hooks is a “way to fire off custom scripts when certain important actions occur”. There are two kinds of hooks: “client-side” and “server-side”. In this post we will talk about how we can deploy our client-side hooks in the simplest way possible.

&nbsp;

When you think about client-side hooks there are two main issues to think about:

1) If a developer delete the repository and clone it again, how can he add the hooks again?

2) If a developer creates a new repository how can he add the hooks easily?

&nbsp;

We must keep in mind that generally we want to maintain the hooks as a transparent process for the clients. So how can we achieve this? I personally divided this into two different cases:

1) Hooks that we want to apply for all the repositories in the company

2) Hooks that we want to apply for specific repository
<h3></h3>
&nbsp;
<h3><strong>Hooks that we want to apply for all the repositories in the company </strong></h3>
In the first case what we want to achieve is to change the default content that Git add into the folder .git when it performs the clone or the init command. For this we need to understand how this data get there.

When Git clones a repository he uses a template stored in the client machine to build the repository (the folder .git) and then it download from the remote repository all the data and store it into the repository.

Therefore to change the default content of the .git folder we can do two different things:

1) Change the default template

2) Create a new template and says Git to use it by default

&nbsp;

<strong><u>Option 1</u>: Change the default template</strong>

1)  Go to the following path (for typical Git installations):

--&gt; Windows: <em>C:\Program Files\Git\mingw64\share\git-core</em>

--&gt; Linux: <em>usr/share/git-core</em>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-1-1.png?raw=true" alt="1-1-1.png" />

&nbsp;

2) The folder “templates” contains all the items that are added under .git folder

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-1-2.png?raw=true" alt="1-1-2.png" />

&nbsp;

3) Edit the hooks stored in the hooks folder

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-1-3.png?raw=true" alt="1-1-3.png" />

&nbsp;

4) Now when you create a new repository or when you clone a existent one, the hooks will be added automatically to the repository

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-1-4.png?raw=true" alt="1-1-4.png" />

&nbsp;

<strong><u>Option 2</u>: Create a new template and says Git to use it by default</strong>

1) Go to the following path:

--&gt; Windows: <em>C:\Program Files\Git\mingw64\share\git-core</em>

--&gt; Linux: <em>usr/share/git-core</em>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-2-1.png?raw=true" alt="1-2-1.png" />

&nbsp;

2) Copy the folder “templates” and give a name for your custom template

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-2-2.png?raw=true" alt="1-2-2.png" />

&nbsp;

3) Edit your template adding your hooks

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-2-3.png?raw=true" alt="1-2-3.png" />

&nbsp;

4) Run the following command to change the default template used by Git

--&gt; $ git --global init.templateDir &lt;TemplatePath&gt;

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-2-4.png?raw=true" alt="1-2-4.png" />

&nbsp;

<strong><u>Note</u>: You can specify which template use when you clone/init a repository using [--template]</strong>

--&gt; $ git init --template &lt;TemplatePath&gt;

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/1-3-1.png?raw=true" alt="1-3-1.png" />

Don’t matter which way you choice to use, you will need to perform the changes in each client machine. For this the best approach is to create a script to perform the changes automatically and then ask the clients to run it.

&nbsp;
<h3><strong>Hooks that we want to apply for specific repository</strong></h3>
This case is a bit more complicated because it is not generic but there are some things that can be done to achieve it.

<strong><u>Option 1</u>:</strong> Write a script that install the desired hooks and store it in the repository. Then run it each time that you clone the repository.

<strong><u>Option 2</u>:</strong> Create a default template as above, but add conditions to the hooks to act differently according to the repository name.

<strong><u>Option 3</u>:</strong> Create a template for each repository in clients machines and use the option [--template] when clone command is performed to use the desired template.

<strong><u>Note</u>:</strong> If you are using Linux my favorite approach is the following:

1) Create a init script that install the desired hooks in the repository

2) Save the init script in the git repository

3) Create a bash function called git that check if you are running the command “git clone” and if so, run the git command and then the init script in the repository. Something like this:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Deploying%20Git%20Client-Side%20hooks/Images/2-1-1.png?raw=true" alt="2-1-1.png" />

&nbsp;