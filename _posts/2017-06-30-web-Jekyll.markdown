---
layout: post
title: "web: Jekyll "
subtitle: "Jekyll: static site generator"
date: 2017-06-30
author: Sol
category: Web
tags: web jekyll static generator
finished: false
---
## links
[jekyll website](https://jekyllrb.com/)
[jekyll doc](https://jekyllrb.com/docs/home/)
[Hands on tutorial](http://jekyllrb.com/tutorials/)

## usefull cmd

* `jekyll --version`
* `gem update jekyll`
* `jekyll serve` $$ \Rightarrow $$ http://localhost:4000/


## 1. Intro

Jekyll is  simple, blog aware static site generator.

> When in doubt, use the `help` command to remind you all available options and usage, it also works with the `new`, `build` and `serve` subcommands, eg `jekyll help new` or `jekyll help build`. 

> `jekyll new --help`

To brows the docs on-the-go, install the `jekyll-docs` gem and run jekyll docs in your terminal.


### keywords:
* static site generator
* blog aware
* template
* markdown
* liquid renderer
* github page

## 2. Quick start

* Install jekyll and Bundler **gems** trough RubyGems  
``` ruby
gem install jekyll bundler
```
Usefull only the first time.   

* Create a new Jekyll site at ./myblog

```ruby
jekyll new myblog
```


* `bundler` is a gem that manages other Ruby gems. Makes sure your gems and gem versions are compatible, and that you have all necessary dependencies each gem requires.

* The `Gemfile` and `GemFile.lock` files informe Bundler about the gem requirements in your site.

* By default, the jekyll site installed by `jekyll new` uses a gem-based theme called **Minima**. With **gem-based-themes**, some of the directories and files are stored in the theme-gem, hidden from your immediate view.

* To start with a blank slate, use `jekyll new myblog --blank`

## 3. install

Best way: RubyGems at the terminal prompt.
```ruby
gem install jekyll
```
Install Jekyll's gem dependencies

## 4. Basic usage

The Jekyll gem mages a `jekyll` executable vailable to you in your Terminal window. 

```sh
$ jekyll build
# => The current folder will be generated into ./_site

$ jekyll build --destination <destination>
# => The current folder will be generated into <destination>

$ jekyll build --source <source> --destination <destination>
# => The <source> folder will be generated into <destination>

$ jekyll build --watch
# => The current folder will be generated into ./_site,
#    watched for changes, and regenerated automatically.
```

> Changes to `_config.yml` are not included during automatic regeneration

## 5. serve

Jekyll comes with a built-in develoment server.  

```sh
$ jekyll serve
# => A development server will run at http://localhost:4000/
# Auto-regeneration: enabled. Use `--no-watch` to disable.

$ jekyll serve --detach
# => Same as `jekyll serve` but will detach from the current terminal.
#    If you need to kill the server, you can `kill 
# -9 1234` where "1234" is the PID.
#    If you cannot find the PID, then do, `ps aux | 
# grep jekyll` and kill the instance.

$ jekyll serve --no-watch
# => Same as `jekyll serve` but will not watch for changes.
```

## 6. Directories structure 

Jekyll is, at its core, a text transformation engine. The concept behind the system is this: you give it text written in your favorite markup language, be that Markdown, Textile, or just plain HTML, and it churns that through a layout or a series of layout files. Throughout that process you can tweak how you want the site URLs to look, what data gets displayed in the layout, and more. This is all done through editing text files; the static web site is the final product.

A basic Jekyll site usually looks something like this:

```
.
├── _config.yml
├── _data
|   └── members.yml
├── _drafts
|   ├── begin-with-the-crazy-ideas.md
|   └── on-simplicity-in-technology.md
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   └── post.html
├── _posts
|   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
|   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _sass
|   ├── _base.scss
|   └── _layout.scss
├── _site
├── .jekyll-metadata
└── index.html # can also be an 'index.md' with valid YAML Frontmatter
```

* **_config.yml** $$ \Rightarrow $$ 
Stores **configuration** data. Many of these options can be specified from the command line executable but it’s easier to specify them here so you don’t have to remember them.

* **_drafts** $$ \Rightarrow $$ 
Drafts are unpublished posts. The format of these files is without a date: **title.MARKUP**.

* **_includes** $$ \Rightarrow $$ 
Partials that can be mixed and matched by your layouts and posts to facilitate reuse. The liquid tag `{% raw %}{% include file.ext %}{% endraw %}` can be used to include the partial in **_includes/file.ext**.

* **_layouts** $$ \Rightarrow $$ 
These are the templates that wrap posts. Layouts are chosen on a post-by-post basis in the YAML Front Matter, which is described in the next section. The **liquid tag**  `{% raw %}{{ content }}{% endraw %}` is used to inject content into the web page.

* **\_posts** $$ \Rightarrow $$ 
Your dynamic content, so to speak. The naming convention of these files is important, and must follow the format: **YEAR-MONTH-DAY-title.MARKUP**. The _permalinks_ can be customized for each post, but the date and markup language are determined solely by the file name.

* **_data** $$ \Rightarrow $$ Well-formatted site data should be placed here. The Jekyll engine will autoload all data files (using either the `.yml`,  `.yaml`, `.json` or `.csv` formats and extensions) in this directory, and they will be accessible via **site.data**. If there's a file `members.yml` under the directory, then you can access contents of the file through `site.data.members`.


* **_sass** $$ \Rightarrow $$
These are sass partials that can be imported into your `main.scss` which will then be processed into a single **stylesheet**  `main.css` that defines the styles to be used by your site.


* **_site** $$ \Rightarrow $$ 
This is where the generated site will be placed (by default) once Jekyll is done transforming it. It’s probably a good idea to add this to your `.gitignore` file.


* **.jekyll-metadata** $$ \Rightarrow $$
This helps Jekyll keep track of which files have not been modified since the site was last built, and which files will need to be regenerated on the next build. This file will not be included in the generated site. It’s probably a good idea to add this to your `.gitignore` file.


* **index.html** $$ \Rightarrow $$ 
Provided that the file has a **YAML Front Matter** section, it will be transformed by Jekyll. The same will happen for any `.html`, `.markdown`,  `.md`, or `.textile` file in your site’s root directory or directories not listed above.


* **_other files/folders_** $$ \Rightarrow $$ 
Every other directory and file except for those listed above—such as `css` and `images` folders,  `favicon.ico` files, and so forth—will be copied verbatim to the generated site. There are plenty of **sites already using Jekyll** if you’re curious to see how they’re laid out.

#### Directory structure of Jekyll sites using gem-based themes
Starting Jekyll 3.2, a new Jekyll project bootstrapped with `jekyll new` uses **gem-based themes** to define the look of the site. This results in a lighter default directory structure : `_layouts`, `_includes` and `_sass` are stored in the theme-gem, by default.

**minima** is the current default theme, and `bundle show minima` will show you where minima theme's files are stored on your computer.


## 7. Configuration 
These options can either be specified in a `_config.yml` file placed in your site's root directory, or can be specified as flags for the `jekyll` executable in the terminal.

### Configuration settings

#### Global configuration
The table below lists the available settings for Jekyll, and the various `options` (specified in the configuration file) `flags` (specified on the command-line) that control them.

[Jekyll's web site](https://jekyllrb.com/docs/configuration/)

## 8. Front Matter
 This is where Jekyll get really cool. Any file that contains a **YAML** front matter block will be processed by Jekyll as a special file. The front matter must be the first thing in the file and must take the form of valid YAML set between triple-dashed lines. Here is a basic example:

 ```yaml
---
layout: post
title: Blogging Like a Hacker
---
 ```

 Between these triple-dashed lines, you can set prdefined varibles or even create custom ones of your own. These variables will then be available to you to access using liquid tags, both further down in the file and also in any layouts or includes that the page or post in relies on.

> !! If you use **UTF-8 encoding**, make sure that no `BOM` header characters exist in your file or **very, very bad things** will happen to Jekyll. **Especilly on Windows**

> ProTip™: Front Matter Variables Are Optional: If you want to use Liquid tags and variables but don’t need anything in your front matter, just leave it empty! The set of triple-dashed lines with nothing in between will still get Jekyll to process your file. (This is useful for things like CSS and RSS feeds!)

### Predefined Global Variables

There are a number of predefined global variables that you can set in the front matter of a page or a post.

* `layout` $$ \Rightarrow $$ if set, this specifies the layout file to use. Use the layout file name without the file extension. Layout files must be placed in the `_layouts` directory.

* `permalink` $$ \Rightarrow $$ if you need your processed blog post URLs to be something other than the site-wide style (default `/year/month/day/title.html), then you can set this variable and it will be used as the finl URL.

* `published` $$ \Rightarrow $$ set to false if you don't want a specific post to show up when the site is generated

> ProTip™: Render Posts Marked As Unpublished
To preview unpublished pages, simply run `jekyll serve` or `jekyll build` with the `--unpublished` switch. Jekyll also has a handy drafts feature tailored specifically for blog posts.

### Custom Variables

Any variable in the front matter that are not predefined are mixed into the data that is sent to the **Liquid templating engine** during the conversion. For instance, if you set a title, you can use that in your layout to set the page title:

```html
<html>
    <head>
        <title> {% raw %}{{ page.title}}{% endraw %} </title>
    </head>
    <body>
        ...
```

### Predefined Variables for Posts
These are available out-if-the-box to be used in the front matter for a post.

* `date` $$ \Rightarrow $$ A date here overrides the date from the name of the post. This can be used to ensure correct sorting of posts. A date is specified in the format `YYYY-MM-DD HH:MM:SS +/-TTTT`; hours, minutes, seconds, and timezone offset are optional.

* `category` $$ \Rightarrow $$ Instead of placing posts inside of folders, you can specify one or more categories that the post belongs to. When the site is generated the post will act as though it had been set with these categories normally. Categories (plural key) can be specified as a YAML list or a space-separated string.

* `tags` $$ \Rightarrow $$  Similar to categories, one or multiple tages can be added to a post. Also like categories, tags can be specified as a **YAML list** or  space-separated string.

> Protip: Don't repeat yourself
> If you don't want to repeat your frquently used front matter variables over and over, just defines **defaults** for them and only override them when necessary (or not at all). This works both for predefined and custom variables.


## 9. Writing posts

If you write articles and publish them online, you can publish and maintain a blog simply by managing a folder of text-files on your computer. Compared to the hassle of configuring and maintaining databases and web-based CMS systems, this will be a welcome change!

### Posts folder

The `_posts` folder is where the blog posts lives. Generally Markdown or HTML, but can be other formats with the proper converter installed. All posts must have YAML Front Matter, and they will be converted from their source format into an HTML page that is part of your static site.

### Creating Post Files

Create a file in the `_posts` directory. **How you name files in this folder is important:** 
```
YEAR-MONTH-DAY-title.MARKUP
```
* `YEAR` $$ \Rightarrow $$ is a four-digit number
* `MONTH` $$ \Rightarrow $$ two-digit number
* `DAY` $$ \Rightarrow $$ two-digit number
* `MARKUP` $$ \Rightarrow $$ file extension of the format used

Examples of valid post filenames:

```
2011-12-31-new-years-eve-is-awesome.md
2012-09-12-how-to-write-a-blog.md
```

### A typical post

```
---
layout: post
title:  "Welcome to Jekyll!"
date:   2015-11-17 16:16:01 -0600
categories: jekyll update
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `bundle exec jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.
```

Everything in between the first and second --- are part of the YAML Front Matter, and everything after the second --- will be rendered with Markdown and show up as “Content.”


### Displaying an index of posts

Creating an index of posts on another page (or in a template) is easy, thanks to the Liquid template language and its tags. Here’s a basic example of how to create a list of links to your blog posts:

```html
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
```
Note that the `post` variable only exists inside the `for` loop above. If you wish to access the currently-rendering page/posts’s variables (the variables of the post/page that has the `for` loop in it), use the `page` variable instead.


## 10. Tags

### Includes

If you have small page snippets that you want to include in multiple places on your site, save the snippets as include files and insert them where required, by using the `include` tag:

```
{% raw %}{% include footer.html %}{% endraw %} 
```

Jekyll expects all include files to be placed in an `_includes` directory at the root of your source directory. In the above example, this will embed the contents of `_includes/footer.html` into the calling file.

### Using variables names for the include filePermalink

The name of the file you want to embed can be specified as a variable instead of an actual file name. For example, suppose you defined a variable in your page’s front matter like this:

```yaml
---
title: My page
my_variable: footer_company_a.html
---
```
You could then reference that variable in your include:

```yaml
{% raw %}{% include {{ Page.my_varible }}{% endraw %}
```

### Passing parameters to includes

Passing parameters to includes is especially helpful when you want to hide away complex formatting from your Markdown content.

For example, suppose you have a special image syntax with complex formatting, and you don’t want your authors to remember the complex formatting. As a result, you decide to simplify the formatting by using an include with parameters. Here’s an example of the special image syntax you might want to populate with an include:

```html
<figure>
   <a href="http://jekyllrb.com">
   <img src="logo.png" style="max-width: 200px;"
      alt="Jekyll logo" />
   <figcaption>This is the Jekyll logo</figcaption>
</figure>
```

You could templatize this content in your include and make each value available as a parameter, like this:

```html
<figure>
   <a href="{{ include.url }}">
   <img src="{{ include.file }}" style="max-width: {{ include.max-width }};"
      alt="{{ include.alt }}"/>
   <figcaption>{{ include.caption }}</figcaption>
</figure>
```
This include contains 5 parameters:

* url
* max-width
* file
* alt
* caption

Here’s an example that passes all the parameters to this include (the include file is named `image.html`):

```
{% raw %}{% include image.html url="http://jekyllrb.com"
max-width="200px" file="logo.png" alt="Jekyll logo"
caption="This is the Jekyll logo." %}{% endraw %}
```

Overall, you can create includes that act as templates for a variety of uses — inserting audio or video clips, alerts, special formatting, and more. **However, note that you should avoid using too many includes**, as this will slow down the build time of your site. For example, don’t use includes every time you insert an image. (The above technique shows a use case for special images.)

Passing param










# A CLASSER

The `_config.yml` master configurtion file contains global configuration and variable destinations that are read once at execution time. Changes made to it during automatic regeneration are not loaded until the next execution. 

> note: **Data Files** are included and reloaded during automatic regenerations