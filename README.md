# Zingy

Most. Zen. Static. Website. Generator. Ever.

## Why another one? C'mon, you must be kidding...

I just wanted to set up a very simple website (just a few pages) with Jekyll and it didn't feel right. I didn't want a blog.

I checked other projects but they were incomplete, cumbersome or solved the wrong problem (blogs, blogs everywhere). I wanted a zen-like experience. Just a layout and some Markdown files as pages with unobstrusive structure and configuration.

Yes, it is another NIH but... I think Zingy is a different kind of beast. I admit that I probably overlooked some projects.

### Where is the difference?

1. Gophers. There is [Hastie](https://github.com/mkaz/hastie) too. If you want a blog.
2. Markdown only. I really like [Mou](http://mouapp.com/).
3. Just a loop. Zingy just loops over all .md and .html files (oh, I told a little lie in #2, my bad - please, bear with me) in current directory (and subdirectories), ignoring all any other file (including dot-files).
4. Your imagination as limit. Zingy has a simple extension mechanism based in subcommands. Do you really need to handle a blog with zingy? Install/create a new extension and do it!
5. Unobstrutive structure, no '_' files. More in [Usage](#usage) section.

## Usage

Just do:

    $ zingy init

A .zingy directory will be created with sane defaults. Put your layout in .zingy/layout.html and you are ready.

    $ zingy

Yes. Enough. Your delightful site is on .zingy/deploy. Enjoy.

## Configuration and extension

Zingy is like water. It can flow or it can cr... Nevermind, Zingy doesn't crash (please fill an issue if it does).

Everything is configurable at .zingy/config.yml. It is created with default vaules everytime you create a repository. Beware, it is created every time you execute init.

To extend Zingy functionality you can use and create plugins. Developers, you can develop them in any language (not only in Golang) thanks to Unix magic. And more gophers.

### Plugins

Any prefixed by "zg" or "mzg" is a potential Zingy plugin. All plugins are treated as Zingy subcommands.

For example, we invoke an imaginary plugin called 'zghello' as subcommand:

    $ zingy hello
    Hello!
    
    $ zingy hello World
    Hello World!

That's all. Any command line argument after subcommand name is passed to "zghello" command.

#### Ok, what's the deal with "mzg" prefix? (a.k.a. MIME types plugins)

These are MIME type plugins. Zingy uses embed tags to allow easy integration beyond command line. Any MIME type can be configured in .zingy/config.yml under mimetypes section.

    mimetypes:
      text/markdown: markdown
      text/yaml+myplugin: myplugin

If Zingy find an embed tag with type attribute set to "text/yaml+myplugin", it will invoke "mzgmyplugin". Zingy expects to process plugin's stdout as HTML. It also pipes stderr to user's shell. Any plugin will be called passing current file's path as argument.

    <embed src="navigation.md" type="text/markdown" />

Maybe you are asking to yourself: "Where is mzgmarkdown?". Nowhere! It is an special case, where Zingy calls an exported method Markdown. Anyway, I wanted to allow to anyone to override internal Markdown processing if they wish.

If you develop a new plugin, please contact me and I will list it here :) Please, keep in mind: make it [idempotent](http://en.wikipedia.org/wiki/Idempotence).

## Building sites

Your site layout will look like this:

    $ ls
    $

Just kidding. A normal site would be:

    $ ls -laR
    total 8
    drwxr-xr-x   5 Dario  staff   170 30 mar 16:18 .
    drwxr-xr-x   6 Dario  staff   204 30 mar 13:17 ..
    drwxr-xr-x  13 Dario  staff   442 27 mar 20:05 .git
    drwxr-xr-x   3 Dario  staff   102 30 mar 13:18 .zingy
    -rw-r--r--   1 Dario  staff   941 30 mar 16:19 about.md
    -rw-r--r--@  1 Dario  staff  1645 30 mar 15:31 index.md
    drwxr-xr-x   4 Dario  staff   136 30 mar 16:20 section
    
    [...]
    
    ./.zingy:
    total 0
    drwxr-xr-x  4 Dario  staff   136 30 mar 16:22 .
    drwxr-xr-x  7 Dario  staff   238 30 mar 16:19 ..
    -rw-r--r--  1 Dario  staff    29 30 mar 13:18 config.yml
    -rw-r--r--  1 Dario  staff  2438 30 mar 16:22 layout.html
    
    ./section:
    total 0
    drwxr-xr-x  4 Dario  staff  136 30 mar 16:20 .
    drwxr-xr-x  7 Dario  staff  238 30 mar 16:19 ..
    -rw-r--r--  1 Dario  staff  718 30 mar 16:19 index.md
    -rw-r--r--  1 Dario  staff  991 30 mar 16:20 more.md

All .md files will be converted to HTML and copied in .zingy/deploy using .zingy/layout.html as layout and copying any other files and their structure. This is also true for .html files.

Keep in mind that any file will be treated as a Go text template before any further processing. You have access to this fields and methods from anywhere:

* **{{.Body}}**: the file itself in HTML.
* **{{.Title}}**: autodetected title (first H1 header in file).
* **{{.Path}}**: file's path (also valid as URL).
* **{{.Site.BaseURL}}**: URL where this site will be deployed, e.g. http://example.com (without final slash).
* **{{.Site.Image}}**: URL to main image. Useful for Open Graph and Twitter meta tags.
* **{{.Page}}**: YAML map from first HTML comment (in Markdown and HTML files). It is optional.
* **{{.URL}}**: full URL for this file.
* **{{.Extra "/path/"}}**: map holding .zingy/config.yml as it is. You can access to any value with its full path. E.g. BaseURL is also available as "/site/baseurl".

### What about layout.html?

It is plain HTML. No frills. Just add a placeholder {{.Body}} in your template.

First header level 1 from Markdown files will be made available as {{.Title}}.

### But... I want to do pages beyond post-like format

No problem! Just use our old friend \<embed\>. Imagine \<layout\> is a valid tag.

    <layout>
    	<nav>
    	    <embed src="navigation.md" type="text/markdown" />
    	</nav>
    	<article>
	    	<embed src="section/index.md" type="text/markdown" />
	    </article>
    </layout>

What does it mean? It means you can have .html files with embedded markdown files.

They will be rendered replacing embed tag if and only if they have type attribute set as "text/markdown".

## Roadmap

Currently there are no new features planned. Feel free to open an issue if you think Zingy should do something specific in its core.

## Contact me

If I can help you, you have an idea or you are using Zingy in your projects, don't hesitate to drop me a line (or a pull request): [@im_dario](https://twitter.com/im_dario)

## About

Written by [Dario Castañé](http://dario.im).

## License

Zingy is under [AGPL v3](http://www.gnu.org/licenses/agpl-3.0.html) license.
