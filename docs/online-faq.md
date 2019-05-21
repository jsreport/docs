

Here you can find some topics which applies specifically to the [jsreportonline cloud service](/online). The common documentation for jsreport can be found at standard place in the [learn](https://jsreport.net/learn) section.

- [Dedicated on-prem vs jsreportonline](#dedicated-on-prem-vs-jsreportonline)
- [Windows recipes](#windows-recipes)
- [Status page](#status-page)
- [Support](#support)
- [Container crashed](#container-crashed)
- [jsreport version](#jsreport-version)


### <a name="dedicated-on-prem-vs-jsreportonline"></a>Dedicated on-prem versus jsreportonline
jsreportonline service provides very convenient way to use jsreport without any installation. It is also high available cloud hosted service which takes care of the software updates for you. The service uses isolated containers for user requests evaluation to prevent the "neighbor noise". However there are also some limitations when using jsreportonline instead of the dedicated jsreport on your own servers.

#### Custom extensions or npm modules
You can't install additional npm packages and use them in your helpers or scripts. You need to rely on the set of libraries provided by the service. The only workaround is use an asset to reference a custom script or use cdn to link it.

#### Throttling and timeout limits
The service has strict throttling and timeout limits which you can't change. This implies that the service may not be the best choice if you plan to render very long reports with thousands of pages. It is better to use for such case your own dedicated instance which should have no issues with it.

You can find the detail numbers for timeouts and throttling in the dedicated article [Online limits](/learn/online-limits).

### <a name="windows-recipes"></a>Windows recipes

jsreport uses technologies for pdf rendering which behaves a little bit differently when running on Windows and Linux platform. It mainly produces different sizes of the elements inside the pdf which can be painful when doing transition from the local to the cloud environment. Fortunately jsreportonline support both - Windows as well as the Linux based rendering. To choose the rendering platform, you only need to navigate to the template menu and set the right item in the `properties/phantom-pdf/phantomjs version`. The default platform is Linux and the version with `windows` postfix instructs the service to run particular template on the Windows.

Please note that the Windows based rendering doesn't currently provide such isolation as when running on the default Linux. There can be some noise from the other tenants which can temporary slow down your pdf rendering. For this reason we strongly recommend to **use the default Linux for the mission critical scenarios**.

### <a name="status-page"></a>Status page

Please follow our [jsreportonline status page](https://jsreportonline.a.offsitestatus.com/) to get the latest info about the current service health. Additionally to the service health, you can also find there the notifications about changes and latest releases.

### <a name="support"></a>Support

The best way to reach us is to use our [forum](https://forum.jsreport.net/). You can also use our support email if you have a sensitive question or urgent problem. `support@jsreport.net`

### <a name="container-crashed"></a>Container crashed
You may get this error when running template in jsreportonline.
  
> Error when processing render request The communication with your dedicated recipe container crashed. This is usually caused by reaching provided resources limits. The container is now about to be restarted.

jsreportonline isolates every rendering request in the dedicated docker container sandbox. Each user/tenant has its own. The  container  has strict limits for memory and cpu usage. When the container reaches the memory limit it gets killed by OS. When we lose the communication with the container, we throw this particular error. In our experience, the memory problem usually originates in rendering big images in pdf. The developer usually places an html `img` tag with a huge image and restrict its size with width and height. However, the pdf has this image in a smaller size but with the full original resolution. The big images then cause memory issues even when the image width/height is small in the pdf. The solution, in this case, is to resize the image before it is used.

### <a name="jsreport-version"></a>jsreport version
jsreportonline uses typically a bit older version of jsreport. You can find out the used version in the settings/About dialog or get more information [here](/learn/online-versions).