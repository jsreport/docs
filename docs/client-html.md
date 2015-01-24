> `client-html` recipe renders html using specified [javascript templating engine](/learn/templating-engines) on the **client side**. This is very significant difference form standard [html](/learn/html) recipe. 

The main purpose of `client-html` recipe is to make html reports more interactive and responsive. With client rendering you can interact with user by filtering or sorting input data and repainting just affected part of report. 

The output of the template rendering based on the `client-html` recipe is an html page which wraps the template definition and render it on the client side when it's loaded. The wrapping page also links jsreport `embed.js` and exposes template definition in the javascript object `jsreport.template`. This is very handy for making self editable and exportable reports.

Note that `client-html` rendering really runs in the browser which means other jsreport server extensions like adding images or running scripts won't be invoked.

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


##Rendering template into an iframe

It is very common to render template based on `client-html`  recipe into an iframe as a part of the external application. In this case the template can use the parent page javascript functions and also share the authenticated session under condition iframe is loaded from the same domain. Therefore it is recommended to route the template rendering through your application server to avoid cross domain iframe problems. 

Then you can register any function in the main application global context:

```js
function loadData(cb) {
	$.getJSON(....);
}
```

and use it inside `client-html` template to load data under the authenticated user:
```js
window.top.loadData(function(posts) {
	jsreport.reload("#postsTable", { posts: posts })
});
```