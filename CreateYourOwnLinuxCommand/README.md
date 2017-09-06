Create your custom command in Linux is very simple. I will take a specific case and use it to explain the process.

I have a Linux machine which is used as a Jenkins slave that is configured to work with windows slaves. I can’t change this configuration so my workaround is replace the command “C:\Program File\Git\cmd\git.exe” for the “git” command. To achieve this I’ll create a command called “C:\Program File\Git\cmd\git.exe” that will be used to run git commands.

&nbsp;

1. Create a Bash Script which will run your command

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/CreateYourOwnLinuxCommand/Images/CreateYourOwnLinuxCommand_1.png?raw=true" alt="CreateYourOwnLinuxCommand_1.png" />

&nbsp;

2. Make the command executable

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/CreateYourOwnLinuxCommand/Images/CreateYourOwnLinuxCommand_2.png?raw=true" alt="CreateYourOwnLinuxCommand_2.png" />

&nbsp;

3. Copy your script to the path “/usr/bin”

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/CreateYourOwnLinuxCommand/Images/CreateYourOwnLinuxCommand_3.png?raw=true" alt="CreateYourOwnLinuxCommand_3.png" />

&nbsp;

4. Test your new Linux Command

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/CreateYourOwnLinuxCommand/Images/CreateYourOwnLinuxCommand_4.png?raw=true" alt="CreateYourOwnLinuxCommand_4.png" />

&nbsp;

<strong>Note:</strong> To delete your command just move to the path “/usr/bin” and remove the script

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/CreateYourOwnLinuxCommand/Images/CreateYourOwnLinuxCommand_5.png?raw=true" alt="CreateYourOwnLinuxCommand_5.png" />

&nbsp;