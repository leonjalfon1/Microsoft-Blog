A couple of months ago I started to write a <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_modules?view=powershell-5.1">Powershell Module</a> to use the TFS Rest API from Powershell scripts. You can find and download it from <a href="https://github.com/leonjalfon1/tfs-powershell-module">GitHub</a>.

I personally use it for builds, releases and for integration/automation tools. The module helps me to create a much cleaner, shorter and easier scripts to interact with TFS/VSTS. Most of the functions works for TFS 2015.3 and above (including VSTS) but there are a couple of functions (lock git branch for example) that were introduced in later versions. 

The module is strongly documented and each function has a detailed explanation about how to use it (the module should be able to be used also by people who are not familiar with Powershell). For example:

&nbsp;

[code language="powershell" gutter="true"]
# Get BuildInfo from BuildId
function Get-BuildInfo
{
    <#
        .SYNOPSIS
        Get a build information from a specific build

        .DESCRIPTION
        Get a build information from the specified build using the TFS Rest API version 2.0
        https://www.visualstudio.com/en-us/docs/integrate/api/build/builds#get-a-build

        .PARAMETER BuildId
        The build Id
        Example: 373

        .PARAMETER CollectionUrl
		Team Foundation Server Collection Url
		Example: "http://tfsserver:8080/tfs/DefaultCollection"

        .PARAMETER TeamProject
        Team Project Name
        Example: MyProject

        .PARAMETER Credentials
        Domain, username and password to access to TFS
        Example: "domain\leonj:MyP@ssw0rd"

        .EXAMPLE
        Get-BuildInfo -BuildId "373" -CollectionUrl "http://tfsserver:8080/tfs/DefaultCollection" -TeamProject "MyProject" -Credentials "domain\leonj:MyP@ssw0rd"
        Returns the BuildInfo for the specified build (as object)
    #>

    param
    (
        [Parameter(Mandatory=$true)]
        $BuildId,
        [Parameter(Mandatory=$true)]
        $CollectionUrl,
        [Parameter(Mandatory=$true)]
        $TeamProject,
        [Parameter(Mandatory=$true)]
        $Credentials
    )

	try
	{
        $apiVersion = "2.0"
		$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}" -f $Credentials)))
        $requestUrl = "$CollectionUrl/$TeamProject/_apis/build/builds/$BuildId" + "?api-version=$apiVersion"
		$response = Invoke-WebRequest -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -ContentType application/json -Uri $requestUrl -Method GET -UseBasicParsing
		$buildInfo = $response | ConvertFrom-Json
		return $buildInfo
	}

	catch
	{
        Write-Host "Failed to get the BuildInfo for build {$BuildId}, Exception: $_" -ForegroundColor Red
		return $null
	}
}
[/code]

&nbsp;

All the documentation about how to use the module is on GitHub but if there is something that is not clear, I'll be happy to help. There is still a lot of work to be done but I think it can be useful for now. In case you need a function that isn't implemented in the module and you need help to write it, please open a <a href="https://github.com/leonjalfon1/tfs-powershell-module/issues/new">issue</a> in GitHub and I will be glad to help you. And if you write a new function please create a <a href="https://help.github.com/articles/creating-a-pull-request/">pull request</a> to share it with us and add it to the module :)

&nbsp;
&nbsp;