<em>(First of all it’s important to make clear that this technique is not supported by Microsoft so PLEASE DON’T TRY THIS AT PRODUCTION)</em>

It’s quite common in medium and big companies to have a TFS test environment to safely test plugins, test third party tools and show customizations before implementing them in production environment.

However, because the test server is an exact copy of the production it may happen that we unintentionally perform the tests on the wrong server (in fact the only difference between them is the url so is easy to get wrong).  So, how can we avoid this typical mistake? Is simple, changing the TFS theme color.

In order to do this we just have to connect to the Application Tier and once there, edit the CSS files that are being used by TFS to build the web portal. There are three elements that we would want to change: the main portal header, the header in the administration page and the tabs in the administration page.

In custom installations the files to change are located in the following path: “<em>C:\Program Files\Microsoft Team Foundation Server 14.0\Application Tier\Web Services\_static\tfs\Dev14.M95.3\App_Themes\Default”</em>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFSThemeColor/Images/RootFolder.png?raw=true" alt="RootFolder.png" />

&nbsp;

<strong>Main Portal Header</strong>

File to change: Core.css
Class to change: .header-post11 #header-row
Property to change: background-color

Change:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFSThemeColor/Images/MainHeaderChange.png?raw=true" alt="MainHeaderChange.png" />

Result:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFSThemeColor/Images/MainHeader.png?raw=true" alt="MainHeader.png" />

&nbsp;

<strong>Administration Page Header</strong>

File to change: Areas.css
Class to change: body.Admin
Property to change: background-color

Change:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFSThemeColor/Images/AdminPageHeaderChange.png?raw=true" alt="AdminPageHeaderChange.png" />

Result:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFSThemeColor/Images/AdminPageHeader.png?raw=true" alt="AdminPageHeader.png?" />

&nbsp;

<strong>Administration Page Tabs</strong>

File to change: Core.css
Class to change: .Admin .legacy-header .hubs &gt; li &gt; a
Property to change: background-color

Change:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFSThemeColor/Images/AdminPageTabsChange.png?raw=true" alt="AdminPageTabsChange.png" />

Result:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFSThemeColor/Images/AdminPageTabs.png?raw=true" alt="AdminPageTabs.png" />

&nbsp;

Needless to say that we can use this technique to make all kinds of customizations in the display of our test server. Just don’t forget that this isn’t supported by Microsoft and although I haven’t had any problems with this technique so far, I don’t recommend it IN ANY WAY for production environments.

&nbsp;