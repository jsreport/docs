`client-html` recipe renders html using specified [javascript templating engine](/learn/templating-engines) on the **client side**. This is very significant difference form standard [html](/learn/html) recipe. 

The main purpose of `client-html` recipe is to make html reports more interactive and responsive. With client rendering you can interact with user by filtering or sorting input data and repainting just affected part of report. In this case it is no longer a report but rather an widget. In combination with jsreport [embedding extension](/learn/embedding) user can also in place customize widget in the embedded jsreport editor.

##Example

Following example can be run in jsreport studio as it is with selected `client-html` recipe. It will load some json data from a remote service and displays a table with it. User can interact with report by filtering data.

```html
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>

<label for="userFilter">Filter by user</label>
<input type="text" id="userFilter">
<input type="button" id="filter" value="Filter"/>

<table id="postsTable">
    <thead>
        <th>User</th>
        <th>Title</th>
    </thead>
    <tbody>
        {{for posts}}
            <tr>
                <td>{{:userId}}</td>
                <td>{{:title}}</td>
            </tr>
        {{/for}}
    </tbody>
</table>

<script>

     $(function() {
        $.getJSON("http://jsonplaceholder.typicode.com/posts", function(posts) {
            jsreport.reload("#postsTable", { posts: posts })
        });   
     });
     
     $("#filter").click(function() {
        var filter = $("#userFilter").val();
        $.getJSON("http://jsonplaceholder.typicode.com/posts?userId=" + filter, function(posts) {
            jsreport.reload("#postsTable", { posts: posts })
        }); 
     });
</script>
```

It is very similar to the standard `html` recipe except the templating engine runs in the browser. User interaction is handled by calling `jsreport.reload` reloading part of the report. Again at the browser side. 

##Rendering outside jsreport studio

You would soon realize `client-render` runs just in the studio and requests to render it on the server results into error with information it is a client side recipe. To actually run it outside jsreport studio you need to attach [jsreport embedded script](/learn/embedding) to page and call a function to render report. The example can look the following:

```html
<!DOCTYPE html>
<html>
<head lang="en">
    <!-- jquery is required for jsreport embedding -->
    <script src="//code.jquery.com/jquery-1.11.0.min.js"></script>
</head>
<body>
    <div style="height:100vh" id="placeholder"></div>
    <script>
        //jsreport will call jsreportInit function from global scope when its initialized
        jsreportInit = function () {          
            jsreport.render($("#placeholder"), "-JencUHkI");
        };
    </script>

    <script>
        //add jsreport embedding script, just change url to jsreport server
        (function (d, s, id) {
            var js, fjs = d.getElementsByTagName(s)[0];
            if (d.getElementById(id)) {
                return;
            }
            js = d.createElement(s);
            js.id = id;
            js.src = "http://local.net:2000/extension/embedding/public/embed.min.js";
            fjs.parentNode.insertBefore(js, fjs);
        }(document, 'script', 'jsreport-embedding'));
    </script>
</body>
</html>
```

After opening this example in any browser it will render particular template.

##Add common functions to the report

You can easily add common functions or data from a wrapper page. This can be used for example to specify common functions loading data and use them in all reports.

Adding a common function to the `client-html` recipe based report is done in the following way:
```js
jsreport.setClientContext({
	loadData: function(cb) {
		cb(["foo"];
	});
```

And then you can reach `jsreport.context` in the report
```js
jsreport.context.loadData(function(posts) {
	jsreport.reload("#postsTable", { posts: posts })
});
```