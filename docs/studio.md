
> The studio extension provides jsreport UI which you typically reach using browser and prepare your templates there.

## Themes
The jsreport by default comes with a light and dark theme. You can select the theme through the settings menu.
![themes](/img/studio-themes.png)

You can install additional themes or implement your own because themes are just standard jsreport extensions. Take a look at how the [dark theme](https://github.com/jsreport/jsreport-theme-dark) code looks.

You can globally change the default light theme to dark using config `extensions.studio.theme.name=dark`.

## White labeling
You can adapt the studio look and add for example your company logo. You can also change some basic colors or even add your own css.

![studio-white-labeling](/img/studio-white-labeling.png)

**Logo**
The logo can be changed using config `extensions.studio.theme.logo.path` which expects path to the image or `extensions.studio.theme.logo.base64` which expects string image representation.

**Color variables**
The colors can be changed using config `extensions.studio.theme.variables.primaryColor` and others. The full list can be found again using cli command `jsreport help config`.

**Custom css**
The path to the custom css can be provided in config `extensions.studio.theme.customCss.path` or you can pass raw css in config `extensions.studio.theme.customCss.content`.

## Startup page
jsreport studio provides in the startup page some interesting information like last edited templates and logs for the last requests.

![startup](/img/studio-startup.png)

You can disable the startup page or disable persisting logs. Check the configuration details using cli command `jsreport help config`.

