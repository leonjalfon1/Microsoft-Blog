<em>(First of all it’s important to make clear that this technique is not supported by Microsoft so PLEASE DON’T TRY THIS AT PRODUCTION)</em>

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFS2017ThemeColor/Images/Welcome.png?raw=true" alt="WelcomePage.png" />

&nbsp;

It’s quite common in medium and big companies to have a TFS test environment to safely test plugins, test third party tools and show customizations before implementing them in production environment.

However, because the test server is an exact copy of the production it may happen that we unintentionally perform the tests on the wrong server (in fact the only difference between them is the url so is easy to get wrong).  So, how can we avoid this typical mistake? Is simple, changing the TFS theme color.

In order to do this we just have to connect to the Application Tier and once there, edit the CSS files that are being used by TFS to build the web portal and restart the application in order to apply the changes. There are three main elements that we would want to change: the main portal header, the header in the administration page and the tabs in the administration page.

In custom installations the files to change are located in the following path: “<em>C:\Program Files\Microsoft Team Foundation Server 15.0\Application Tier\Web Services\_static\tfs\Dev15.M#.#\App_Themes”</em> (the folder Dev15. may change between tfs update versions)

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFS2017ThemeColor/Images/Main.png?raw=true" alt="MainPage.png" />

&nbsp;

In a previous post I explained how to perform this trick in TFS 2015 (Change TFS 2015 Theme Color). There I explain how to perform the changes to the css files manually. However this time I created a script to do all the work for us including create a backup and restore the default theme colors. Great right?

&nbsp;

You can get the code from [<a href="https://github.com/leonjalfon1/TFS2017ThemeColorUpdater">TFS2017ThemeColorUpdater</a>] and read the documentation to understand how it works and how using it. However it's quite simple (remember that you must run them with administrator privileges):

&nbsp;

To change the theme colors for the script defaults (red as in a pictures above) just run:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFS2017ThemeColor/Images/ChangeColorsDefault.png?raw=true" alt="ChangeColorsDefault.png" />

&nbsp;

To change the theme colors for a custom ones just pass them as parameters to the script as following:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFS2017ThemeColor/Images/ChangeColors.png?raw=true" alt="ChangeColors.png" />

&nbsp;

And finally to restore the default theme colors just run:

<img src="https://github.com/leonjalfon1/Microsoft-Blog/blob/master/ChangeTFS2017ThemeColor/Images/RestoreColors.png?raw=true" alt="RestoreColors.png" />

&nbsp;

Needless to say that we can use this technique (change the source files) to make all kinds of customizations in the display of our test server. Just don’t forget that this isn’t supported by Microsoft and although I haven’t had any problems with this technique so far, I don’t recommend it IN ANY WAY for production environments.

&nbsp;