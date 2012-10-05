Source for a _Scala, briefly_ presentation, given at a meeting
of *Philly Coders*, on 6 June, 2012.

Requires the following software:

* [Ruby][]
* The [Rake][] build tool
* John MacFarlane's excellent [Pandoc][] tool
* The `lessc` command, to process the [LESS][] stylesheets.

Once you have all that stuff handy, simply run:

    $ rake

to build `slides.html`, a fully self-contained [Slidy][] slide show. Just
open the file in your browser, and away you go.

You'll find the source for the slides in `slides.md`, a Markdown file.
For more information on using [Pandoc][] for slide generation, see
<http://johnmacfarlane.net/pandoc/README.html#producing-slide-shows-with-pandoc>.

You can view a pre-built version of this presentation at
<http://www.ardentex.com/publications/the-play-framework/>.

[Ruby]: http://www.ruby-lang.org/
[Rake]: http://rake.rubyforge.org/
[Bundler]: http://gembundler.com/
[LESS]: http://lesscss.org/
[Pandoc]: http://johnmacfarlane.net/pandoc/
[PHASE]: http://scala-phase.org/
[Slidy]: http://www.w3.org/Talks/Tools/Slidy/
