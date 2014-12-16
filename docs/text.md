`text`  recipe simply returns plain files. This recipe is great for generating text files like `csv` or `xml` because there is no transformation required. Recipe can be configured to change `Content Type`, `File Extension` and `Content Disposition`.

- `Content Type` - any mime type like `text/xml` or `text/csv` can be set
- `File Extension` - output file extension like `xml` or `csv`
- `Content Disposition` - standard full http `Content-Disposition` header or just shorten `inline` or `attachment` string. It is useful to set for example `attachment` disposition for `csv` files to force browser to open csv in excel during preview.