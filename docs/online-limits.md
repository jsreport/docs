The timeouts for specific jsreport tasks are not depending on the pricing plan. The following table lists the timeouts for the most important recipes and tasks. The `chrome-pdf` recipe timeout is for example `20s` but the `templating engine` rendering is limited with `10s`, afterwards it gets killed. The last `hard container timeout` is the fixed timeout for any task, after the whole docker container used to execute the task or recipe is restarted.

| task        | timeout |
| ----------  |:--------:|
| phantomjs         | 20s     |
| chrome         | 20s     |
| electron           | 20s     |
| templating engine | 10s     |
| scripts           | 20s     |
| hard container timeout | 50s |

Each jsreportonline has its own throttling limits for rendering or amount of stored entities. The rendering throttling is always measured in the 5 minutes window. If the time spent in the rendering containers reaches the limit,  the new requests are rejected until the 5 minutes window is closed. Each account has also limit for number of entities stored in each entity set.

| plan | render throttling | entity amount limit |
| -----|:------------------:|:-------------------:|  
| free   | 50s | 20 |
| bronze | 200s | 100 |
| silver | 600s | 300 |
| gold   | 1000s | 500 |

> NOTE: entity amount limit for **folders** is always 100 for each plan

The stored [reports](/learn/reports) are persisted only for limited time which varies based on the subscribed plan.

| plan | reports persistence limnit |
| -----|:------------------:|
| free   | day |
| bronze | week |
| silver | month |
| gold   | month |


Additionally there are the following miscellaneous limits you should be aware of.

| limit name | value |
| ------------- |:---------:|
| min scheduling interval | 5min |
| max parallel render requests | 5 |
| max render requests per second | 5 |
| maximum POST body size | 20MB |
