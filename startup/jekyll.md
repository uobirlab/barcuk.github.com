---
layout: default
title: Jekyll Tutorial
---

Here, you can find basic installation instructions, and some basic to get you
started with using Jekyll on your system. You can find more details at the [Jekyll homepage](www.jekyllrb.com).

## Installing Jekyll

Jekyll requires that your system have Ruby and RubyGems installed, and runs on
Windows, Mac OS X and Linux.

### Ubuntu Based Distributions

To install Ruby and RubyGems, use either the command line or your package
manager to install the `ruby1.9.1-dev`, `ruby1.9.1` and `rubygems` packages.

	sudo apt-get install ruby1.9.1 ruby1.9.1-dev rubygems

Once you have all these packages, you can install Jekyll via the package
manager, but it is recommended that you do it through rubygems, as that package
is likely to be more up to date. At the time of writing (June 2013), the apt
repositories for Ubuntu 13.04 contain version 0.11, while the gem package is on
version 1.0.3. Jekyll can be installed via rubygems with

	sudo gem install jekyll

This will download Jekyll along with some additional packages that are used for
code highlighting and various other extras.

## Using Jekyll

Once you have cloned or forked the
[website repository](https://github.com/barcuk/barcuk.github.com), you can start
updating it with Jekyll.

To build the site and update the `_site` directory, run `jekyll build` in the
top level directory of the site, `barcuk.github.com`. To preview the site, use
`jekyll serve` and then access `localhost:4000` in your web browser. When you
make changes to files, you will need to rerun the command to view the
updates. Alternatively, you can use `jekyll serve --watch` in the top level
directory, which will watch for changes in the directory and update the site
when changes are made.

If the site does not build correctly, you can use `jekyll build --trace` to see
where things went wrong. The most likely culprits are Markdown or Liquid syntax
errors.

You can find a short Markdown tutorial [here](/startup/markdown.html).
