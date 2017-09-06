In Git TFS there is not “git gc” implemented in the server side. At first sight this can be a big problem because once you push a change to the database it will remain in the database forever (yes, forever). The only way to remove it is to delete the whole repository. Nevertheless this fact allows us to recover at any moment any thing that we have deleted. Let’s see how achieve this…

&nbsp;

1) Get the Internal Repository ID:

[code language="sql" light="true"]
SELECT Name, InternalRepositoryId
FROM [Tfs_DefaultCollection].[dbo].[tbl_GitRepository]
ORDER BY InternalRepositoryId
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_1.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_1.png" />

&nbsp;

2) Search the deleted branch in the reflog using the following query:

[code language="sql" light="true"]
SELECT DISTINCT Name
FROM [Tfs_DefaultCollection].[dbo].[tbl_GitRefLog]
WHERE InternalRepositoryId = 25
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_2.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_2.png" />

&nbsp;

3) Then you can search for all the commits related to the specified branch using:

[code language="sql" light="true"]
SELECT *
FROM [Tfs_DefaultCollection].[dbo].[tbl_GitRefLog]
WHERE Name = 'refs/heads/results' AND InternalRepositoryId = 25
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_3.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_3.png" />

&nbsp;

4) You can access to any deleted commit using the “ObjectId” (SHA1) by removing the “0x” at the beginning:
<pre>     •For Example: 0xCD23C7685DABEDAAF9197E69C6FDFDF12E72DD9C</pre>

<pre>       <img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_4.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_4.png" /></pre>
&nbsp;

5) Find the commit where the branch was removed using:

[code language="sql" light="true"]
SELECT *
FROM [Tfs_DefaultCollection].[dbo].[tbl_GitRefLog]
WHERE Name = 'refs/heads/results' AND InternalRepositoryId = 25 AND NewObjectId =0x0000000000000000000000000000000000000000
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_5.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_5.png" />

&nbsp;

6) Now you are able to recover the branch by:
<pre>     • Checkout the last commit of the deleted branch (as detached head)</pre>
<pre>       <img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_6.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_6.png" /></pre>
&nbsp;
<pre>     • Creating a new branch from the commit checked out</pre>
<pre>       <img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_7.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_7.png" /></pre>
&nbsp;

7) That’s it, your branch was recovered

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_8.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_8.png" />

&nbsp;

<strong>Note:
Do you want to know who delete the branch and kill him?
Follow the steps below to find him… </strong>

&nbsp;

1) Use the query of the step (3) to retrieve the Push Id for the delete commit (newObjectId = 0x000...)

[code language="sql" light="true"]
SELECT *
FROM [Tfs_DefaultCollection].[dbo].[tbl_GitRefLog]
WHERE Name = 'refs/heads/results' AND InternalRepositoryId = 25
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_9.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_9.png" />

&nbsp;

2) Use the “PushId” to retrieve the “PusherId” of the user that perform the commit using the following query:

[code language="sql" light="true"]
SELECT *
FROM [Tfs_DefaultCollection].[dbo].[tbl_GitPush]
WHERE PushId = 4758
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_10.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_10.png" />

&nbsp;

3) And finally get the user details using the following query

[code language="sql" light="true"]
SELECT TOP 1000 *
FROM [Tfs_Configuration].[dbo].[tbl_Identity]
WHERE Id like 'B9050C32-6258-4DCA-A6FD07820EB0'
[/code]

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/RecoverDeletedBranchOrCommitInGitTFS/Images/RecoverDeletedBranchOrCommitInGitTFS_11.png?raw=true" alt="RecoverDeletedBranchOrCommitInGitTFS_11.png" />

&nbsp;