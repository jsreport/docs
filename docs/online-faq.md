Here you can find some topics which applies specifically to the [jsreportonline cloud service](/learn/online). The common documentation for jsreport can be found at standard place in the [learn](https://jsreport.net/learn) section.


- [Windows recipes](#windows-recipes)
- [Limits](#limits)    
- [Status page](#status-page)
- [Support](#support)

### <a name="windows-recipes"></a>Windows recipes

jsreport uses technologies for pdf rendering which behaves a little bit differently when running on Windows and Linux platform. It mainly produces different sizes of the elements inside the pdf which can be painful when doing transition from the local to the cloud environment. Fortunately jsreportonline support both - Windows as well as the Linux based rendering. To choose the rendering platform, you only need to navigate to the template menu and set the right item in the `properties/phantom-pdf/phantomjs version`. The default platform is Linux and the version with `windows` postfix instructs the service to run particular template on the Windows.

Please note that the Windows based rendering doesn't currently provide such isolation as when running on the default Linux. There can be some noise from the other tenants which can temporary slow down your pdf rendering. For this reason we strongly recommend to **use the default Linux for the mission critical scenarios**. 

### <a name="limits"></a>Limits
The limits and throttling for the specific pricing plans is described in the dedicated article [Online limits](/learn/online-limits).

### <a name="status-page"></a>Status page

Please follow our [jsreportonline status page](https://jsreportonline.a.offsitestatus.com/) to get the latest info about the current service health. Additionally to the service health, you can also find there the notifications about changes and latest releases.

### <a name="support"></a>Support

The best way to reach us is to use our [forum](https://forum.jsreport.net/). You can also use our support email if you have a sensitive question or urgent problem. `support@jsreport.net`
