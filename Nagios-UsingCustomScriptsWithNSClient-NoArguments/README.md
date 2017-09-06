If you are using Nagios to monitor your environments probably at some point you will want to write your custom scripts in order to monitor more complex or specific things. Next we will review how to do it using the check_nrpe command for scripts that don't need arguments (for scripts with arguments see my another post <a href="https://blogs.microsoft.co.il/leonj/2017/08/03/nagios-using-custom-scripts-with-nsclient-with-arguments" target="_blank" rel="noopener">here</a>)

&nbsp;

<strong>1) Write the Script</strong>

<hr />

• Throw the exit code using a <em><strong>$LASTEXITCODE</strong></em> variable following this convention:
<blockquote>
<ul>
 	<li>0 –&gt; OK</li>
 	<li>1 –&gt; Warning</li>
 	<li>2 –&gt; Critical</li>
 	<li>3 –&gt; Unknown</li>
</ul>
</blockquote>

&nbsp;

• The messages printed in the console will be shown in Nagios

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Nagios-UsingCustomScriptsWithNSClient-NoArguments/Images/Image1.png?raw=true" alt="Image" />

&nbsp;

<strong>2) Add the script to the nsclient++ “scripts” folder in the agent machines</strong>

<hr />

• Add the script to the folder <em><strong>“..\nsclient++\scripts”</strong></rm>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Nagios-UsingCustomScriptsWithNSClient-NoArguments/Images/Image2.png?raw=true" alt="Image" />

&nbsp;

<strong>3) Edit the “nsclient.ini” file to allow using custom scripts</strong>

<hr />

• Ensure the file <em><strong>"nsclient.ini”</strong></em> in the client machine contains the option “CheckExternalScripts=enabled” in the <em><strong>[/modules]</strong></em> section

[code language="text"]
[/modules]

CheckHelpers = 1
CheckNSCP = 1
CheckDisk = 1
CheckSystem = 1
NSClientServer = 1
CheckEventLog = 1
NSCAClient = 1
NRPEServer = enabled
CheckExternalScripts = enabled
[/code]

&nbsp;

• Ensure the <em><strong>“NRPE settings"</strong></em> are configured in the <em><strong>“nsclient.ini”</strong></em> file in the client machines

[code language="text"]
[/settings/NRPE/server]

verify mode = none
insecure = true
port = 9999
extended response = 0
allow arguments = true
allow nasty characters = true
[/code]

&nbsp;

• Add the script to the <em><strong>“external scripts settings”</strong></em> section (if the section not exist, create it)

Note:
<blockquote>
<ul>
 	<li><em>TfsStuckBuilds</em> is the name of the monitor check</li>
 	<li><em>CheckForTfsStuckBuilds.ps1</em> is the name of the script</li>
 	<li>You must meet the format below</li>
</ul>
</blockquote>

[code language="text"]
[/settings/external scripts/scripts]

TfsStuckBuilds = cmd /c echo scripts\CheckForTfsStuckBuilds.ps1; exit($lastexitcode) | powershell.exe -command -
[/code]

&nbsp;

<strong>4) Edit the Nagios configuration to add a service using the custom script</strong>

<hr />

• Ensure the command <em><strong>“check_nrpe”</strong></em> is configured in the file <em><strong>“commands.cfg”</strong></em> (If not, create it adding the below)

[code language="text"]
define command{
    command_name   check_nrpe
    command_line   $USER1$/check_nrpe -H $HOSTADDRESS$ -p 9999 -c $ARG1$ -t 60
}
[/code]

&nbsp;

• Configure a service in the <em><strong>“windows.cfg”</strong></em> file using the command <em><strong>“check_nrpe”</strong></em> and passing the monitor check name as parameter

[code language="text"]
define service{
    use                   generic-service
    host_name             server01
    service_description   TFS Stuck Builds
    check_command         check_nrpe! -H 127.0.0.1 -c TfsStuckBuilds
}
[/code]

&nbsp;

<strong>5) Go to the host machine and restart the nsclient++ service (to refresh the nsclient.ini file)</strong>

<hr />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Nagios-UsingCustomScriptsWithNSClient-NoArguments/Images/Image3.png?raw=true" alt="Image" />

&nbsp;

<strong>6) Go to Nagios portal and re-schedule the next check of the service (or wait until the next check)</strong>

<hr />

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/Nagios-UsingCustomScriptsWithNSClient-NoArguments/Images/Image4.png?raw=true" alt="Image" />

&nbsp;