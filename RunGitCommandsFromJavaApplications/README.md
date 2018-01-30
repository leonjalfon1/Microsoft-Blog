A few days ago I came across the following question: "how to execute git commands from a java application". After a little research I found a library called JGit which fulfilled my expectations. In this post I will explain how to use this library to execute the basic commands.

&nbsp;

To import the library, add the following dependency to your pom file:

[code language="xml" gutter="true"]
<dependency>
    <groupId>org.eclipse.jgit</groupId>
    <artifactId>org.eclipse.jgit</artifactId>
    <version>4.10.0.201712302008-r</version>
</dependency>
[/code]

&nbsp;

Then you will be able to import and use the JGit classes

[code language="java" gutter="true"]
import org.eclipse.jgit.*;
[/code]

&nbsp;

To clone a repository you can use the following method:

[code language="java" gutter="true"]
public void cloneRepo(String repositoryUrl, String gitLocalRepositoryPath, String tfsUser, String password) throws GitAPIException {
    CloneCommand cloneCommand = Git.cloneRepository()
        .setURI(repositoryUrl)
        .setDirectory(new File(gitLocalRepositoryPath))

    UsernamePasswordCredentialsProvider credentials = new UsernamePasswordCredentialsProvider(username, password);
    cloneCommand.setCredentialsProvider(user);
    repository = cloneCommand.call();
    return repository;
}
[/code]
&nbsp;

You can also configure the clone command. For example, to clone only the last commit in a specific branch and show the cloning progress:

[code language="java" gutter="true"]
public void cloneRepo(String repositoryUrl, String gitLocalRepositoryPath, String tfsUser, String password, String branch) throws GitAPIException {

String branchRef = "refs/heads/" + branch

    CloneCommand cloneCommand = Git.cloneRepository()
        .setURI(repositoryUrl)
        .setDirectory(new File(gitLocalRepositoryPath))
        .setProgressMonitor(new TextProgressMonitor(new PrintWriter(System.out)))
        .setBranchesToClone(Arrays.asList(branchRef))
        .setBranch(branchRef)
        .setCloneAllBranches(false);

    UsernamePasswordCredentialsProvider user = new UsernamePasswordCredentialsProvider(tfsUser, tfsPassword);
    cloneCommand.setCredentialsProvider(user);
    repository = cloneCommand.call();
    return repository;
}
[/code]

&nbsp;

To run "git add" you can use the following method:

[code language="java" gutter="true"]
public void addFileToIndex(Git repository, File fileToAdd, String gitLocalRepositoryPath) throws IOException, GitAPIException {
    String filePath = fileToAdd.getAbsolutePath().replace("\\","/").substring(gitLocalRepositoryPath.length()+1);
    repository.add().addFilepattern(filePath).call();
}
[/code]

&nbsp;

To run "git add -A" you can use the following method:

[code language="java" gutter="true"]
public void addFileToIndex(Git repository) throws IOException, GitAPIException {
    repository.add().addFilepattern("*").call();
}
[/code]

&nbsp;

To run "git commit" you can use the following method:

[code language="java" gutter="true"]
public void commitChanges(Git repository, String commitMessage) throws GitAPIException {
    CommitCommand commit = repository.commit();
    commit.setAllowEmpty(false);
    commit.setMessage(commitMessage).call();
}
[/code]

&nbsp;

To run "git push" you can use the following method:

[code language="java" gutter="true"]
public void pushChanges(Git repository, String user, String password) throws GitAPIException {
    PushCommand pushCommand = repository.push();
    pushCommand.setCredentialsProvider(new UsernamePasswordCredentialsProvider(user, password));
    pushCommand.call();
}
[/code]

&nbsp;

For more information see the <a href="http://download.eclipse.org/jgit/site/4.10.0.201712302008-r/apidocs/index.html">JGit official documentation</a>

&nbsp;
&nbsp;