How many of us have ever used the "Poll SCM" option in our Jenkins jobs?

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/JenkinsPollSCMTheNightmareForPerformance/Images/JenkinsPollSCM.png?raw=true" alt="JenkinsPollSCM.png" />

It’s very easy to simply say to our job "hey can you please verify every X time my repository and trigger the job only in case there are any changes"

At first glance it’s a simple, quick and harmless way. And the most important, it’s an easy method and works well.

However, have we ever wondered what this represents for our server? What is the cost of this "comfort"? Probably not, what is sure is that every time that the server get slow we quickly complain and look for any complicated reason instead of seeing what is before our eyes.

One of the most common reasons for this problem is nothing more and nothing less than our "comfortable" Poll SCM.

Most of the time that we use Poll SCM we set it to check our repository every 5 minutes. But what that really means? It means that Jenkins should get the sources and compare them with the latest version every 5 minutes.

In small environments this is not a problem, but when we talk about servers with dozens of nodes and hundreds of jobs where everyone has to get the sources and perform the comparison every 5 minutes this becomes a real headache. And if the sources are large and the only fact of downloading them is a challenge the problem starts to grow exponentially.

Now, if using Poll SCM is a problem, how can we implement CI and trigger our job every time our repository changes?

As the saying goes, if Mahoma does not go to the mountain, the mountain goes to Mahoma. Namely, instead of Jenkins having to constantly review if our repository changes, we can configure our repository to notify Jenkins whenever we make a change.

And what is the best way to achieve this? Using hooks!

Configuring our repository so that every time it changes, it will send a signal to our Jenkins server and trigger the job through the Jenkins Rest API. Each source control system is different and has different ways of set it up, the most typical examples of these hooks are: Service Hooks or CI Builds in TFS, server side githooks in Git servers and webhooks in GitHub, Bitbucket and Gitlab.

Certainly there are some cases where we have no other option than using Poll SCM (for example if our Jenkins server is not accessible from GitHub).  The important thing is not to be carried away by the temptation of the "comfort" offered by the Poll SCM option and use this method only as a last resource.

&nbsp;