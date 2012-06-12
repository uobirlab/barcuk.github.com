---
layout: default
title: Using Intellij IDEA
---

This tutorial will show you how to setup Intellij IDEA for awesome
rosjava hacking. Note that `$` is the command line prompt.

Installing on Ubuntu 12.04
---------------------------

You need to use Oracle's Java rather than the OpenJDK when you're
using Intellij. Setting that up is pretty easy.

```sh
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get install  oracle-java7-installer oracle-jdk7-installer 
```

Go to the [JetBrains site](http://www.jetbrains.com/idea/) and
download the latest version of Intellij IDEA. At the time of writing,
this is version `11.1.2`.

Unpack the archive and move it somewhere convenient. I like to put
things under `/opt` but `/usr/local` is also common.

```sh
$ sudo tar xvf ideaIC-11.1.2.tar.gz /opt
```
If you want to be able to run it from any directory, add the
installation bin folder to your path. Assuming you use bash, open your
`.bashrc` file and add the Intellij path to your `PATH` variable.

```sh
export PATH=$PATH:/opt/idea-11.1.2/bin
```

If you want to add Intellij to the sidebar, you can follow
[this tutorial](http://www.hamaluik.com/?p=283) on how to do that. So
far, I've just been launching it from the command line.

Creating a Project
------------------

Intellij does things a bit differently than other IDEs. For example,
you can only have one project open at a time, but you can have many
modules in your project. I find it useful to create a project over the
[barc super-repository](https://github.com/barcuk/barc) and then make
each ROS package a separate module.

For example, I would have a module for `java_examples`, `subsumption`,
`braitenberg`. To make this happen, you simply need to add the
Intellij plugin to the Gradle build file. 

```groovy
apply plugin: 'idea'
```

There's a high chance that the plugin as already been added, but it
doesn't hurt to check.

Now, to make an Intellij project which you can import as a module,
simply run `gradle idea` from within the package. Gradle will generate
all of the necessary files so that you can import the module.

Odds & Ends
-----------

- You can install the Gradle plugin for Intellij to run all of your
  builds from within the IDE
  
- Make sure you add the rosjava sources so that you can step through
  them when debugging or if you just want to make sure how some piece
  of code works.  

<!-- 
LocalWords: rosjava  Intellij
-->
