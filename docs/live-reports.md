jsreport isn't limited for rendering static documents like pdf or excel. You can use it also to render live and user interactive reports. Reports where user can filter data, change the matrix or drill down. This is usually achieved with using the  [html-with-browser-client](https://jsreport.net/learn/html-with-browser-client) recipe together with one of the external javascript data visualization components like [D3.js](https://github.com/d3/d3), [pivot table](https://github.com/nicolaskruchten/pivottable), [chartjs](http://www.chartjs.org/) or any other.

![northwind dashboard](https://jsreport.net/img/northwind-dashboard.gif)

> **[See the full demo live here](https://playground.jsreport.net/workspace/HkqlE-Ww/205)**    
> **[See the demo sources here](https://playground.jsreport.net/studio/workspace/HkqlE-Ww/205)**

This tutorial explains how to create a simple (master - detail) interactive report. However if you are rather curious how can look like a complex dashboard, don't hesitate to open the links below the image and come back to tutorial afterwards.

## Master report template

This tutorial uses [NorthWind](https://northwinddatabase.codeplex.com) database and its live [REST endpoint](http://services.odata.org/V4/Northwind/Northwind.svc) as a data source for the reports. The first thing we do is create the master report template and [script](/learn/scripts) which downloads the report input data from the endpoint.

The script runs on the server side in [node.js](http://nodejs.org/) sandbox and has full capabilities to connect to any kind of data source like database or in this case REST endpoint. The easiest way to download data from the REST endpoint in the node.js is to install [request](https://github.com/request/request) module. Afterwards it is just about invoking the request and passing the response further to jsreport rendering pipeline.

At this point we just simply use [handlebars](https://jsreport.net/learn/handlebars) templating engine and [html](https://jsreport.net/learn/html) recipe to render static html table with the downloaded data.

<iframe src='https://playground.jsreport.net/studio/workspace/ryRU5cmw/7?embed=1' width="100%" height="400" frameborder="0"></iframe>

## Drilling down
Time to make the report interactive. We need another script downloading particular customer's data, another template displaying customer detail and extend the master customers template with the drill down function.

The script downloading data additionally accepts `CustomerID` on the input, fetches orders and reduces them by quarter.

The detail template is basically just plain html using input data and [chart.js](http://www.chartjs.org/) to visualize number of orders in time.

Afterwards we need to change the master template's recipe to the [html-with-browser-client](https://jsreport.net/learn/html-with-browser-client). This recipe adds to the page [browser client](https://jsreport.net/learn/browser-client) which is used to render the `detail` template when the user clicks on the customer.

<iframe src='https://playground.jsreport.net/studio/workspace/ryRU5cmw/9?embed=1' width="100%" height="400" frameborder="0"></iframe>

## Exporting static report

Quite common functionality of dashboards or interactive reports is exporting into a static document like pdf or excel. This can be done again using [html-with-browser-client](https://jsreport.net/learn/html-with-browser-client) recipe.

To demonstrate this,  the master template needs to be extended with an export button and proper click event handler. In this tutorial the event handler simply renders and downloads the output of the same detail template. It only overrides its recipe to [phantom-pdf](https://jsreport.net/learn/phantom-pdf).

To produce the proper pdf after the chart animation is finished, the detail template was extended with printing trigger which is called in the chart.js callback.

<iframe src='https://playground.jsreport.net/studio/workspace/ryRU5cmw/11?embed=1' width="100%" height="400" frameborder="0"></iframe>

## Sharing link to report
Now if you have the working live report, you can share it with others using simple link. You can find the button showing the link in the jsreport toolbar.

If you have [authentication](https://jsreport.net/learn/authentication) enabled, you can also generate anonymous link with access token using `Share` toolbar button.

![link-share.png](https://jsreport.net/img/link-share.png)
