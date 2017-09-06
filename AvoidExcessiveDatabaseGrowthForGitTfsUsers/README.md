Let’s see some tips to avoid the excessive growth of the database if you are using Git in TFS...

&nbsp;

<strong>Keep in mind that in Git-TFS there is not “git gc” implemented in the server side</strong><br />
<hr />
<blockquote><ul>

    <li>This means that once you push a change to the database it will remain in the database forever (yes, forever). The only way to remove it is to delete the whole repository

<br />
    
    <li>Nevertheless this fact allows us to recover at any moment any thing that we have deleted (<a href="https://blogs.microsoft.co.il/leonj/2017/05/29/recovering-deleted-branchescommits-in-git-tfs/">see how here</a>)

<ul></blockquote>

&nbsp;

<strong>Avoid to push the same data from multiple clients simultaneously</strong><br />
<hr />
<blockquote><ul>

    <li>As we saw above TFS keeps all the packs that Git.exe sends to TFS. In particular circumstances, including when many users are pushing the same data simultaneously, Git will decide to send to TFS more data than necessary. Therefore the database will be filled with duplicate data from each of the clients

<ul></blockquote>

&nbsp;

<strong>Use “.gitignore” file to avoid adding unwanted files</strong><br />
<hr />
<blockquote><ul>

    <li>The gitignore file is used to prevent Git to tracking certain files (ensure that certain files not tracked by Git remain untracked), for example test outputs, logs and build files

<br />

    <li>Note that gitignore ignores just files that weren't tracked before

<br />

    <li>The files/folder in your version control will not just delete themselves just because you added them to the gitignore file. They are already in the repository so you have to remove them

<ul></blockquote>

&nbsp;

<strong>Avoid using the command “git add -A”</strong><br />
<hr />
<blockquote><ul>

    <li>Using the command add with the –A (or --all) option may cause unwanted files to be stored in the repository

<br />

    <li>This risk is significantly reduced when the gitignore file is used correctly

<br />

    <li>However it’s always preferable to add each file separately, or at least check the staged files before commit them

<ul></blockquote>

&nbsp;

<strong>Avoid storing binary files</strong><br />
<hr />
<blockquote><ul>

    <li>Binary files can’t be compressed properly so Git will save the complete version of each file for each version instead of saving the delta only

<br />

    <li>For the same reason binary files are undiffables and can’t be merged

<br />

    <li>To handle dependencies, store the dependencies sources and build them or use a package management tool to version and supply these files

<br />

    <li>Small and infrequently updated binaries can be committed, for example images for the web, icons and other smaller art assets

<br />

    <li>For large or frequently updated binaries you can use tools like Git LFS

<ul></blockquote>

&nbsp;

<strong>Don’t commit compressed archives of data</strong><br />
<hr />
<blockquote><ul>

    <li>Git will handle these files as binaries

<br />

    <li>Let Git handle the file compressions

<ul></blockquote>


&nbsp;

<strong>Use Git Large File Storage (Git LFS) to store binary files</strong><br />
<hr />
<blockquote><ul>

    <li>Is fully supported and free in TFS (from TFS 2015 Update 2)

<br />

    <li>Git LFS is an extension to Git developed by GitHub which commits data describing the large files in a commit to your repo, and stores the binary file contents into separate remote storage

<br />

    <li>When you clone and switch branches in your repo, Git LFS downloads for you the correct version from that remote storage

<br />

    <li>Is a transparent process that makes it appear that the files were stored directly in your repository

<ul></blockquote>

&nbsp;
&nbsp;