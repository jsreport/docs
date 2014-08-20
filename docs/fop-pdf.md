
The most common way to print pdf report from jsreport is to use [phantom-pdf](http://jsreport.net/learn/phantom-pdf) recipe and let it convert html into pdf. This is very productive but sometimes you want to have better control over pdf rendering. In this case you should consider using `Fop-pdf` recipe. `Fop-pfg` recipe is using [apache fop](http://xmlgraphics.apache.org/fop/) to print pdf reports. The report must be defined using special xml format called [fo](http://xmlgraphics.apache.org/fop/fo.html). This format can be nicely combined with [templating engines](http://jsreport.net/learn/templating-engines) to dynamically assemble report definition.

`fop-pdf` is running on [playground](https://playground.jsreport.net) and [jsreport online](https://jsreportonline.net) but you need to explicitly install it for your on-prem jsreport server. To fullfill this you need to download [apache fop](http://xmlgraphics.apache.org/fop/) and java and also add its locations into your executable PATH environment variable.

You can find out example for `fop-pdf` recipe in [playground](https://playground.jsreport.net/#/playground/gkxJuycgR5).

Example of fo format:

```xml
<?xml version='1.0' encoding='utf-8'?>
<fo:root xmlns:fo='http://www.w3.org/1999/XSL/Format'>
  <fo:layout-master-set>
    <fo:simple-page-master master-name='A4' page-width='297mm' page-height='210mm' margin-top='1cm' margin-bottom='1cm' margin-left='1cm' margin-right='1cm'>
      <fo:region-body margin='3cm'></fo:region-body>
      <fo:region-before extent='2cm'></fo:region-before>
      <fo:region-after extent='2cm'></fo:region-after>
      <fo:region-start extent='2cm'></fo:region-start>
      <fo:region-end extent='2cm'></fo:region-end>
    </fo:simple-page-master>
  </fo:layout-master-set>
  <fo:page-sequence master-reference='A4' format='A'>
    <fo:flow flow-name='xsl-region-body'>
      <fo:block font-size="18pt" font-family="sans-serif" line-height="24pt" space-after.optimum="15pt"
                background-color="blue" color="white" text-align="center" padding-top="3pt">
        FOP Recipe to render precise Pdf using jsReport
      </fo:block>
      <fo:block>
        <fo:inline font-weight='bold'>jsReport recipe Fop allows you to create very precise
          pdf layouts using Apache™ FOP (Formatting Objects Processor).

          The goal is that template can use all the jsReport features like templating tags, helpers or extensions
          to assemble final FO xml that is afterwards transformed into pdf using Apache FOP. This
          means we can do the same fibonacci example also using FO. Tak a look:
        </fo:inline>
      </fo:block>
      <fo:block margin-top="1cm">
        <fo:table table-layout="fixed" border="1pt solid black">
        <fo:table-column column-width="1cm"></fo:table-column>
        <fo:table-column column-width="3cm"></fo:table-column>
        <fo:table-body font-size="10pt" font-family="sans-serif"  border="inherit">
        {{for ~fibSequence(10)}}
          <fo:table-row line-height="12pt"  border="inherit"  background-color="#DD66{{:#data.i}}0">
        <fo:table-cell  border="inherit">
          <fo:block text-align="start">{{:#data.i}}</fo:block>
        </fo:table-cell>
        <fo:table-cell  border="inherit">
        <fo:block text-align="end">{{:#data.val}}</fo:block>
        </fo:table-cell>
        </fo:table-row>
          {{/for}}
        </fo:table-body>
        </fo:table>
      </fo:block>
      <fo:block margin-top="1cm">
        <fo:inline font-weight='bold'>This was just very simple. For details options of FO xml see</fo:inline>
        <fo:basic-link color="blue" external-destination="http://xmlgraphics.apache.org/fop">http://xmlgraphics.apache.org/fop</fo:basic-link>
      </fo:block>
    </fo:flow>
  </fo:page-sequence>
</fo:root>
```