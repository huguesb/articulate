Articulate
==========

Articulate is a simple [Jekyll](http://jekyllrb.com) plugin that aims to make
long-form articles first-class citizens.

Canonical repository: <http://gitorious.org/articulate>


License
-------

Copyright &copy; 2013, Hugues Bruant <hugues@bruant.info>

Licensed under 2-clause BSD. See bsd.txt for details.


Dependencies
------------

* [Ruby](https://www.ruby-lang.org) >= 1.9
* [Jekyll](http://jekyllrb.com) >= 1.2


Source structure
----------------

    ├── _plugins    the actual plugin
    ├── _articles   sample article
    └── _layouts    sample HTML templates


Install
-------

* copy `_plugins/articulate.rb` to your the plugins dir of your Jekyll site
* copy `_layouts/article_section.html` to the layouts dir of your Jekyll site
  and adapt it to correctly integrate withyour existing layout.
  **NOTE:** you may want to copy the other article_\* layouts. Although they are
  not required, they provide a good starting point to create your own special
  pages.


Usage
-----

Articulate is based on three core concepts:

* A "section" is a part of a longer article that can occupy its own page. Each
  section has its own source file, which may be in any format supported by Jekyll.
* A "special page" is a generated page that does not correspond to a single
  source file. It is instanciated from a layout, using empty content but with
  access to all generated sections. A typical use case (in fact the reason that
  lead to the birth of Articulate) is to generate a print-friendly single-page
  version of the article.
* An "article" is a collection of sections and an arbitrary number of special
 pages.


In practice, Articulate takes input of the following form:

    _articles/
        foobar/
            _article.yml
            01-intro.md
            02-foo.md
            99-bar.md
        ...


Where each markdown file is a section.

The special `_article.yml` file is used to control special page generation and
to specify article-wide Liquid-accessible variables, such as the article title
(the only *required* variable).

Given the above input and the following `_article.yml`:

    title: Foobar
    synopsis: Rise and fall of the Foobar empire
    special: [full, index]


The output would be:

    articles/
        foobar/
            01-intro.html
            02-foo.html
            99-bar.html
            index.html
            full.html
        ...


The `index.html` and `full.html` pages being respectively generated from the
`article_index` and `article_full` layouts.


The YAML frontmatter of an article section differs slightly from that of a
regular page:

* the section title MUST be specified in the `name` variable
* the `title` variable is ignored: the `page.title` variable in Liquid
  is derived from the article and section titles
* the `layout` variable is ignored: `article_section` is used to generate
  single-section pages.


Sections are ordered within the article by the alphabetical order of the
source files, hence the use of numerical prefixes in the example above.
Each section is assigned a numerical index (0-based), Liquid-accessible as
`page.index`.

The following variable structure is Liquid-accessible in all article-aware
pages (i.e. articles sections and special pages):

    article:
        title: Foobar
        # any other value specified in _article.yml
        special:
            full:
                url: /articles/foobar/full.html
            index:
                url: /articles/foobar/index.html
            ...
        sections: [
            {
                url: /articles/foobar/01-foo.html
                path: ./_articles/foobar/01-foo.md
                name: Foo is magic
                title: Foobar : Foo is magic
                index: 1
                content: "..." # fully transformed section content
                # any other value specified in frontmatter
            },
            ...
        ]


Articulate honors the following _config.yml options:

* `articles`: path in which to look for input articles

  default: `./_articles`
* `articles_dest`: path in which to write the generated pages

  default: `./articles`

