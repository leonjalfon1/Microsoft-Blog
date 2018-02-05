By default in TFS/VSTS, the CI build configuration is used to trigger a build after each push. Additionally there is a option to "batch changes" (queue a build with all the changes since the last build instead of queue a build for each push). 

However there are some cases where you want to build each introduced commit separately. It's true that there is no way to do it automatically OOB, but in this post I want to share a way to achieve it with a little creativity (and code).

&nbsp;
<h1>The Idea</h1>
<hr />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_1.png?raw=true" alt="Image" />

Create two different build definitions:

<strong>- Listener Definition:</strong> Catch every push, retrieve the commits related to the push and trigger a build for each one of them.

<strong>- Build Definition:</strong> contains the build configuration to be executed for each commit.

&nbsp;
<h1>Build Definition</h1>
<hr />

1.- Configure the build definition

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_2.png?raw=true" alt="Image" />

(In my case I created a very basic build definition that print its source commit and its source branch. However you can configure the build to do whatever you want)

&nbsp;
<h1>Listener Definition</h1>
<hr />

1.- Create the build definition

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_3.png?raw=true" alt="Image" />

2.- Set the build as continuous integration (no batch changes)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_4.png?raw=true" alt="Image" />

3.- Set the branch trigger filter (dev branch in my case)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_5.png?raw=true" alt="Image" />

4.- Create the "Credential" parameter (as secret parameter)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_6.png?raw=true" alt="Image" />

5.- Create the "TargetBuildDefinitionName" parameter

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_7.png?raw=true" alt="Image" />

6.- Add the listener job script

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_8.png?raw=true" alt="Image" />

(Note the parameters passed to the script as arguments)

7.- Save the definition

&nbsp;
<h1>Listener Job</h1>
<hr />

(You can find the listener job script in <a href="https://github.com/leonjalfon1/tfs-listener-job-script">GitHub</a>)

The first step in the script is get the push that contains the commit that triggered the build:

[code language="powershell" gutter="true" firstline="266"]
$PushId = Get-PushIdByCommitId -BranchName $BranchName -CommitId $SourceVerison -CollectionUrl $CollectionUrl -TeamProject $TeamProject -GitRepository $GitRepository -Credentials $Credentials
[/code]

&nbsp;

Then, all the commit associated to the push are retrieved:

[code language="powershell" gutter="true" firstline="274"]
$Commits = Get-CommitsInPush -PushId $PushId -CollectionUrl $CollectionUrl -TeamProject $TeamProject -GitRepository $GitRepository -Credentials $Credentials
[/code]

&nbsp;

Next, get all the history for the branch (to know which commits inside the push are related to the branch):

[code language="powershell" gutter="true" firstline="277"]
$CommitsnBranch = (Get-CommitsByBranch -BranchName $BranchName -CollectionUrl $CollectionUrl -TeamProject $TeamProject -GitRepository $GitRepository -Credentials $Credentials).commitId
[/code]

&nbsp;

Then, create a list with all the commits introduced in the push that are related to the branch:

[code language="powershell" gutter="true" firstline="280"]
$CommitsInfo = @()

# Fill the list with all the commits introduced in the push
foreach($CommitId in ($Commits | Select commitId).commitId)
{
    # Filter only the commits for the branch
    if($CommitsnBranch -contains $CommitId)
    {
        $CommitInfo = Get-CommitById -CommitId $CommitId -CollectionUrl $CollectionUrl -TeamProject $TeamProject -GitRepository $GitRepository -Credentials $Credentials
        $CommitsInfo += $CommitInfo
    }
}
[/code]

&nbsp;

Now that we have the commits that we want to use, we need to order them in chronological order:

[code language="powershell" gutter="true" firstline="295"]
$CommitsInfo = $CommitsInfo | Select commitId,committer

foreach($value in $CommitsInfo) 
{ 
    $value.committer = $value.committer.date 
}   

$OrderedCommits = $CommitsInfo | Sort committer
[/code]

&nbsp;

Finally a build is triggered for each commit in the list:

[code language="powershell" gutter="true" firstline="306"]
foreach($Commit in $OrderedCommits)
{
    # Create queue body (build parameters) and trigger build definition
    $CommitSha1 = $Commit.commitId
    $TargetDefinitionId = Get-BuildDefinitionId -BuildDefinitionName $TargetBuildDefinitionName -CollectionUrl $CollectionUrl -TeamProject $TeamProject -Credentials $Credentials
    $QueueBody = @{ definition = @{id = $TargetDefinitionId}; sourceBranch = "refs/heads/$BranchName"; sourceVersion = $CommitSha1 }
    $Response = Start-BuildCustomBody -Body $QueueBody -CollectionUrl $CollectionUrl -TeamProject $TeamProject -Credentials $Credentials
         
    # Print build trigger request response
    if($Response -ne $null)
    {
        $BuildNumber = $Response.buildNumber
        Write-Host "Build [$BuildNumber] successfully trigger from commit [$CommitSha1]"
        Start-Sleep -Seconds 5
    }
    else 
    { 
        Write-Host "Error Triggering build from commit [$CommitSha1]" 
    }
}
[/code]

&nbsp;
<h1>Putting All Together</h1>
<hr />

1.- Checkout the dev branch

[code language="bash" gutter="false"]
$ git checkout dev
[/code]

2.- Create new commits

[code language="bash" gutter="false"]
$ echo content >> file.txt && git add -A && git commit -m "commit 1"
$ echo content >> file.txt && git add -A && git commit -m "commit 2"
$ echo content >> file.txt && git add -A && git commit -m "commit 3"
$ echo content >> file.txt && git add -A && git commit -m "commit 4"
$ echo content >> file.txt && git add -A && git commit -m "commit 5"
[/code]

3.- Get commit id's of created commits

[code language="bash" gutter="false"]
$ git log --oneline -n 5
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_9.png?raw=true" alt="Image" />

4.- Push the new commits

[code language="bash" gutter="false"]
$ git push
[/code]

5.- Check the listener job log

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_10.png?raw=true" alt="Image" />

6.- Verify the builds were triggered

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ConfigureACIBuildForEachCommit/Images/ConfigureACIBuildForEachCommit_11.png?raw=true" alt="Image" />

&nbsp;
&nbsp;
