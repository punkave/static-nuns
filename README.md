# habit

A simple static site generator based on nunjucks, LESS and markdown.

There is a sample site provided in the "test" folder:

```bash
cd test
node ../app.js
```

This creates a `_site` folder and copies and converts your files like this:

* `.md` files are compiled with markdown and converted to `.html` as described below. They must include metadata, at least enough to specify the title as shown below.
* `main.less` files are compiled with LESS and converted to `.css` as described below.
* Files and folders starting with `.` or `_` are ignored.
* Everything else is simply copied.

### Layout files for markdown

By default each markdown file is fed to the nunjucks template at `_layouts/default.html`. The HTML generated by markdown is passed as the `content` variable.

Your markdown file must begin with a metadata section, like this:

```
---
title: "This is the title of my page"
---
```

*Any metadata you provide is passed to the nunjucks layout template that renders your page.*

Metadata is parsed as [YAML](http://www.yaml.org/), so you may pass as much data as you like.

### Selecting a different layout

Just use metadata:

```
---
title: "This is the title of my page"
layout: alternate
---
```

This will use the nunjucks template `_layouts/alternate.html` instead.

Yes, you may use the nunjucks `extends` keyword to extend another layout, override blocks and all that cool stuff.

### Relative links in Markdown

To make relative links between pages just use the standard Markdown syntax. Keep in mind each file will have a `.html` extension.

### Absolute links within the site in Markdown

To make absolute links with in your site, always preface your URL with `{{ root }}`. This ensures that you can copy your `_site` folder into github pages or another environment where it will be in a subdirectory and not at the actual root of the site. If you know your content will be at the root you may ignore this.

### Building navigation

If you wish, `habit` can provide you with all the information you need to build navigation links such as breadcrumb trails, lists of child pages, and "previous" and "next" links.

To take advantage, you must:

* Ensure that every folder has an `index.md` page. This is considered the home page for that folder.
* Include a `children` property in the metadata of that page. That property should be a list of the filenames of the child pages, without the file extension, in the order you want them displayed.

Confused? Here's a simple example of a page with children:

```
    ---
    title: "Advanced"
    children:
      - flossing
      - mortgaging
      - dancing
    ---

## Advanced life skills

Here you'll learn how to really rock your life.
```

That page would be called `index.md`, and the same folder would contain `flossing.md`, `mortgaging.md`, and `dancing.md`.

Once you do this, the following additional variables are visible in your page templates:

`children` is an array of objects with information about the child pages, with `title` and `url` properties, in addition to other metadata.

`parent` is an object with information about the parent of the current page. For `flossing.md`, this would be `index.md`. For `index.md`, it would be the `index.md` file of the parent folder.

`ancestors` is an array of objects with information about all of the ancestors of the current page plus itself. `ancestors[0]` is the home page (`index.md` in the root directory of your proejct). The last entry in `ancestors` is the current page.

`previous` is an object with information about the previous page with the same parent, if any.

`next` is an object with information about the next page with the same parent, if any.

Note that all of these objects have complete information. You may loop over `ancestors` in your nunjucks template and then loop over the `children` of each ancestor to create accordion-style navigation.

**There is a complete example in the `test` folder.** Just check out `test/layouts/default.html` to see how all of these variables are used.

### LESS stylesheets

If there is a file called `main.less`, it is compiled with LESS, and the result is stored as `main.css`. Yes, you may `@import` more `.less` files from your `main.less` file. You may have multiple `main.less` files in separate folders, if you are really on fire to have separate LESS compilation for them for some reason.

You may also use plain old `.css` files, which are simply copied, like anything else that doesn't start with `_`.

### Static assets

Need static assets like fonts and images? No problem; they are automatically copied, because they are not `.less` or `.md` files.

### Building your own sites

Install `habit` globally:

```bash
npm install -g habit
```

Now you can just `cd` to a folder containing the appropriate content and type:

```bash
habit
```

To rebuild.

## Changelog

0.3.3: filename is reported when a nunjucks error occurs.

0.3.2: filename is reported when a markdown error occurs.

0.3.1: linked to github repository. No code changes.

0.3.0: linking to headings within a page is much nicer. Headings now contain anchor elements with the `anchor` class, as seen on github. The `name` of each heading is hyphenated, like a CSS class name. This automatically does the right thing whether the original name was "a sentence with spaces", "aCamelCaseFunctionName", or "an_underscored_function_name". If a heading contains `(`, that character and everything after it is ignored when constructing the name, allowing you to easily link to documentation for a function that includes the parameters in the header. However, if two headings would otherwise have the same `name`, `2` is appended to the second one, `3` to the third one and so on.

0.2.2: just documentation fixes.

0.2.1: The "next" and "previous" properties now allow you to traverse the entire site depth-first. That is, you can read through the entire "book" by continuing to click "next."

0.2.0: metadata and navigation-building data.

0.1.6: fixed `@import` in LESS.

0.1.3, 0.1.4, 0.1.5: renamed `habit`.

0.1.2: global install works.

0.1.1: introduced the policy of ignoring dotfiles and `_` files, filtering markdown and LESS, and just copying everything else. This makes much more sense than a long list of special cases. This change renames the `layouts` folder to `_layouts` so they won't be copied.


