Today I was helping a client to create a script to monitor builds that are stuck in TFS (which seems to happen often and they have not yet been able to detect why). For this reason while find out why, it's very important to detect these jams and be notified to restart the build.

During the process I run into a build that has been stuck for more than 3 years (and, surprisingly, nobody had noticed)

Then I run into the problem that when I tried to cancel it by using the web portal it simply didn't disappear. In the hope of being able to correct this from visual studio (was a XAML build) I set myself to search it to cancel it from there, however to my surprise the build was not in the list of queued builds.

A bit confused I used the Rest Api (the GET command below) to understand if the build really exist or had been canceled, and inexplicably was still there.

Http://tfs2015app:8080/tfs/DefaultCollection/Enterprise/_apis/build/builds?Status=InProgress&api-version=1.0

Then I tried to get help on the internet but I didn't get anything useful so I opted for the most logical option that was in my hands: clone the build definition, delete the definition, and rename the clone as if nothing had happened.

And guess what, that worked!