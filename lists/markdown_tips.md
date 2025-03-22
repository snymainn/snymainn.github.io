## Simple way to use Markdown on GitHub pages

A nice feature of hosting web pages on GitHub pages is the ability to automatically process
Markdown file into html code. The processing is done when you push an update to the pages. 
You can look at the processing in the Actions tab on GitHub.

This will be a very short guide on how to control the look of the html pages produced.

The look is controlled by themes that can be given in a _config.yml file. 
If you do ==not== make this file a default theme is used, which currently seems to be [jekyll-theme-primer-0.6.0](https://github.com/pages-themes/primer). Instruction on how to use can be found on its GitHub page and also how it looks. But it is not necessary to follow all the instructions.

The steps I followed to get rid of the header and footer were:

### Modify default.html
Copy the _layouts/default.html from the [primer GitHub page](https://github.com/pages-themes/primer) into you own repository on the same location(i.e. _layouts/default.html) and modify to your liking.

### Add your own css statement
Create the file assets/css/style.scss and add this initial content:
```
---
---

@import "{{ site.theme }}";
```
It must be exactly this way. I have no idea what the `---` does, but it has to be there. And the import statement ensures that the default theme is imported before any of you modifying css statements. 
After the import statement you can add your own code like e.g. 
```
body {
  margin: 2% 2% 2% 2%;
  background-color: #D8D6D2;
}
```

## Use the generated html pages

Remember to link to html variants of your Markdown pages in e.g. index.html.
E.g. this page is name markdown_pages.md, but you must refer to markdown_pages.html to view it. The html file is generated every time you push an update and not a file you need to make manually.







