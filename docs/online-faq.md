Here you can find some topics which applies specifically to the [jsreportonline cloud service](/online). The common documentation for jsreport can be found at standard place in the [learn](https://jsreport.net/learn) section.

- [Dedicated on-prem vs jsreportonline](#dedicated-on-prem-vs-jsreportonline)
- [How to install custom npm module](#how-to-install-custom-npm-module)
- [Container crashed](#container-crashed)
- [jsreport version](#jsreport-version)
- [What is stored and logged](#what-is-stored-and-logged)

### <a name="dedicated-on-prem-vs-jsreportonline"></a>Dedicated on-prem versus jsreportonline
jsreportonline service provides very convenient way to use jsreport without any installation. It is also high available cloud hosted service which takes care of the software updates for you. The service uses isolated containers for user requests evaluation to prevent the "neighbor noise".  However, the service has strict throttling and timeout limits which you can't change. This implies that the service may not be the best choice if you plan to render very long reports with thousands of pages. It is better to use for such case your own dedicated instance which should have no issues with it. You can find the detail numbers for timeouts and throttling in the dedicated article [Online limits](/learn/online-limits).

### <a name="how-to-install-custom-npm-module"></a>How to install custom npm module

You can use [npm](https://jsreport.net/learn/npm) extension to dynamically install and use custom npm modules.

### <a name="support"></a>Support

The best way to reach us with general questions is to use our [forum](https://forum.jsreport.net/). However, if you have some payment questions or some very urgent problems, contact us directly using email  `support@jsreport.net`

### <a name="container-crashed"></a>Container crashed
You may get this error when running template in jsreportonline.
  
> Error when processing render request The communication with your dedicated recipe container crashed. This is usually caused by reaching provided resources limits. The container is now about to be restarted.

jsreportonline isolates every rendering request in the dedicated docker container sandbox. Each user/tenant has its own. The  container  has strict limits for memory and cpu usage. When the container reaches the memory limit it gets killed by OS. When we lose the communication with the container, we throw this particular error. In our experience, the memory problem usually originates in rendering big images in pdf. The developer usually places an html `img` tag with a huge image and restrict its size with width and height. However, the pdf has this image in a smaller size but with the full original resolution. The big images then cause memory issues even when the image width/height is small in the pdf. The solution, in this case, is to resize the image before it is used.

### <a name="jsreport-version"></a>jsreport version
jsreportonline uses typically a bit older version of jsreport. You can find out the used version in the settings/About dialog.

### <a name="what-is-stored-and-logged"></a>What is stored and logged
jsreportonline doesn't store or log sensitive instance data you send in your API requests. It persists only metadata like templates, assets. The log includes only templates' shortids. The temporary files are isolated between tenants and cleared every 3 minutes.