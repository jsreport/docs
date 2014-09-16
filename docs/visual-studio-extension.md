<a class="button large bg-emerald bg-hover-gray fg-white" target="_blank" href="http://youtu.be/UQDAgWsCGzw">pdf reports in visual studio and c# youtube video<i class="icon-arrow-right-3 on-right fg-white"></i></a>

> **[Download from visual studio galery](http://visualstudiogallery.msdn.microsoft.com/b684060e-5785-461f-832a-ffb0243a3874)**

Along with html based studio jsreport provides also visual studio extension for managing and designing report templates. You can find it in [visual studio galery](http://visualstudiogallery.msdn.microsoft.com/b684060e-5785-461f-832a-ffb0243a3874) under name `jsreport visual studio tools`.




This extension adds new jsreport file templates into c# project which you will use to create report templates, images or sample data. But to be able to actually create and preview reports you need install first [jsreport.Embedded](https://www.nuget.org/packages/jsreport.Embedded) nuget package or just [jsreport.Client](https://www.nuget.org/packages/jsreport.Client) if you already have remote jsreport server running somewhere.

<a href="http://jsreport.net/img/vstools-3.png" target="_blank">
<img src="http://jsreport.net/img/vstools-3.png" alt="vstools" style="width: 600px;"/>
</a>

##Creating report template

jsreport report template creates for you grouped `Report.jsrep`, `Report.jsrep.html` and `Report.jsrep.js` and you can define report in them. The main `Report.jsrep` file provides a custom editor allowing you to configure basic report settings like paper size and orientation.

<a href="http://jsreport.net/img/vstools-2.png" target="_blank">
<img src="http://jsreport.net/img/vstools-2.png" alt="vstools" style="width: 600px;"/>
</a>

`Report.jsrep.html` file is used to define report template layout using specified javascript templating engine and `Report.jsrep.js` is a place for javascript helper functions. You can navigate to these files from visual studio solution or use buttons in jsreport editor.

To preview report click green `Preview` button or hit `F5` from any of the `.jsrep` files. To view a pdf report you need to have a pdf adobe acrobat reader installed and also you should consider running visual studio with administrator privileges to avoid prompting from visual studio every time you want to preview a report.

Keep in mind you can redock report preview tab and keep report definition together with preview side by side.

<a href="http://jsreport.net/img/vstools-1.png" target="_blank">
<img src="http://jsreport.net/img/vstools-1.png" alt="vstools" style="width: 800px;"/>
</a>

> Note that all `*.jsrep` files are marked with `Copy Always`  option so they all are distributed with the application bin folder.

##Creating sample data for preview

Typical report needs a layout definition and some input data. While in real application you should provide data dynamically and you need to provide some preview testing data during design time. You can create static testing data using `jsreport sample data` project item template from create new item dialog. This will create a `SampleData.jsrep.json` file where you can fill any json data.

To assign testing data into particular report use `Sample Data` combobox in `jsrep` file. Note that you may need to reopen it first.

##Creating images

Reports quite often contain some static images for headers or background. You can easily create and use such an image from `jsreport image` project item template. This will create a `ReportImage.jsrep.png` which is nothing but an image with `.jsrep` saying to jsreport it should  be synchronized with the server.

Adding image into report is done using special tag jsreport [images extension](http://jsreport.net/learn/images) recognizes and replaces with a real base64 image representation.
```
<img src="{#image ImageName}" height... />
```

##Configuring jsreport

You can hook into jsreport configuration by adding `ReporingStartup` class somewhere into your c# project. This class should contain public `Configure` method  accepting `IVSReportingConfiguration` as parameter.

`ReportingStartup.cs`
```c#
public class ReportingStartup {
    public void Configure(IVSReportingConfiguration configuration) {
        configuration.UseRemoteServer("htt://196.168.0.5");
    }
}
```

You can use `IVSReportingConfiguration` to specify if visual studio should use [embedded jsreport server](http://jsreport.net/learn/net-embedded) or a remote one. You can also define dynamically reports sample data.

```c#
public class ReportingStartup {
    public void Configure(IVSReportingConfiguration configuration) {
        configuration.UseEmbedded();
        
        configuration.RegisterSampleData("SomeSampleData", () => {
        
                 using (NorthwindEntities context = new NorthwindEntities())  {
                 
                     var categories = from category in context.Categories
                                 select new
                                 {
                                     categoryID = category.CategoryID,
                                     categoryName = category.CategoryName
                                 };
                    return new {
                        categories = categories
                    }
                 }
        });
        
    }
}
```

Previous example configures visual studio to use embedded version of jsreport and registers action loading sample data from db. Now you should be able to find `SomeSampleData` in the `jsrep` editor combobox.

##Recommendations
It's recommended to have all reports in one visual studio project because switching between projects causes jsreport embedded server to restart. 

For more complex reports it's recommended to use [child templates](http://jsreport.net/learn/child-templates) and split big reports into several small ones. It's also a good practice to separate css styles into separate report templates and reuse them.










