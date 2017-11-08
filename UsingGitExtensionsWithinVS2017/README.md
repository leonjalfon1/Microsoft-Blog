USING GIT EXTENSIONS WITHIN VISUAL STUDIO 2017

It is no secret to anyone that the implementation of git within visual studio leaves much to be desired. There are a lot of features that are basic for any such as checkout specific commit, git stash and log visualization of what the ide does behind the scenes. Another thing that can not be neglected is that versions prior to VS2017 do not really use git but the git4net library to perform the operations (which causes even more problems such as ocacionar that githooks can not be used)

However if we are using C # solutions and / or TFS as the host of our repository, Visual Studio should be our default IDE by far. However many developers end up using other git clients to perform the specific operations of git and others simply decide to abandon visual studio for the reasons stated above.

But how to deal with problems caused by the implementation of git in VS2017?
What to do to stay in visual studio but without having to give up the missing features?
Introduce another git client within Visual Studio to be able to use it within the development environment!

For this we must install 2 things
1) Git extensions client (git client)
2) Git tools (visual studio extension which add git extensions to the IDE)
And porsias wonder, can I use another git client instead of git extensions? The answer is NO, the vs extension works only with git extensions

