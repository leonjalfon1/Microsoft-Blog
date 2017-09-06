<strong>The Issue</strong>

<hr />

<blockquote>The .gitignore file is used to prevent Git to tracking certain files (namely ensure that certain files not tracked by Git remain untracked), for example test outputs, logs and build files.

The best practice is to create this file when the repository is created, however in most cases, this file is created after detecting that unwanted files are being stored.</blockquote>
&nbsp;

<strong>The Problem</strong>

<hr />

<blockquote>The files/folder that already are in the repository will not just delete themselves just because you added them to the gitignore file. Remember, the .gitignore file ignores only files that are not tracked.</blockquote>
&nbsp;

<strong>The Solution</strong>

<hr />

<blockquote>To resolve the problem remove from the repository the tracked files contained in the .gitignore file. To achieve this use "git rm" to remove and untrack all the files in the repository, then use "git add" to re-add all the files (the files contained in the .gitignore file will not be added) and finally commit the changes.

[code language="perl"]
git rm -r --cached .
git add .
git commit -m "untrack files contained in the .gitignore file"
[/code]

</blockquote>
&nbsp;