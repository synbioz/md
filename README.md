MD
==

Description
-----------

MD is a [Thorfile](https://github.com/wycats/thor) to convert [Markdown](http://daringfireball.net/projects/markdown/syntax) files to HTML and PDF versions.

The real power of MD Thorfile is that it uses a customizable HTML layout and CSS.

Features:

- convert .md to .html with layout and CSS styling
- convert .md to .pdf with layout and CSS styling

Installation
------------

    git clone git://github.com/synbioz/md.git
    thor md:setup # Copy default layout and css

    thor install Thorfile

You can remove this clone since the Thorfile is installed system-wide.

Usage
-----

On the command-line:

    $ thor md:generate my_markdown_file.md # Generates "my_markdown_file.html"
    $ thor md:generate my_markdown_file.md -f pdf # Generates "my_markdown_file.pdf"

Other
-----

If you want to contribute or report bugs you should take a look at:

- [Homepage](https://github.com/synbioz/md)
- [Source Style](https://github.com/synbioz/guidelines/tree/ruby)
- [Ticket Guidelines](https://github.com/synbioz/md/wiki/TicketGuidelines)
- [Contributing](https://github.com/synbioz/md/wiki/Contributing)

Problems, comments, and suggestions are welcome on the [issue tracker](https://github.com/synbioz/md/issues).

Authors
-------

- Martin Catty (aka [\_fuse](https://twitter.com/_fuse))
- Nicolas Cavigneaux (aka [Bounga](https://twitter.com/Bounga))

Copyright (c) 2011 Synbioz, released under the [MIT license](http://creativecommons.org/licenses/MIT/).
