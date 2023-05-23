## Installation
```bash
npm install @jsreport/jsreport-html-embedded-in-docx
```

## Basics
The `html-embedded-in-docx` recipe takes the `html` output and embeds it into the docx based report. This is very straight forward way to generate docx/word reports from jsreport. However please note that such output docx file cannot be opened in some Office implementations like `Open Office`.

The word support for inlined html is limited. If you want to create native docx reports running in all office implementations, you should check the [docx](/learn/docx) recipe.

## Known problems

- images needs to be base64 encoded and provided as data uri to `img` tags. This can be implemented using a custom helper. See [demo](https://playground.jsreport.net/w/anon/QJZWNadk).

## Preview in studio
See general documentation for office preview in studio [here](/learn/office-preview).
