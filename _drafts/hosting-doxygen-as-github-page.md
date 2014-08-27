---
layout: post
title:  "HowTo: Host Doxygen as Github page using git submodule"
date:   2014-08-27
categories: 
tags: git, github pages, submodule, doxygen, how-to
---

For a [recent project](https://github.com/MartinHH/SPAOP), I wanted to use the Github project page of the repository to host the documentation I generated from the source code using [Doxygen](http://www.doxygen.org/). So since a Github-page's content has to be placed in the root directory of the `gh-pages` branch, the task was to find an elegant way to generate the html documentation from the `master` branch into the `gh-pages` branch.

I decided to include the `gh-pages` branch as a submodule of the `master` branch. Since I could not find anything similar on the web, I decided to write a brief summary on how that can be done[^1].

## Disclaimer

Including the Doxygen-Github-pages as a submodule is not the ultimate solution for the problem. Submodules can be confusing if you're not used to them and I'm not sure if I'd recommend it. The following is just a guide how to get it done in case you want to give it a try.

## Pre-Conditions

Suppose the following things are set up:

1. Your Github project is set up. For this example we assume its url is `git@bitbucket.org:exampleUser/exampleProject.git`.
2. Your local and remote master branches are up-to-date and don't have any uncommited changes.
3. You already have Doxygen set up and use it to render the html documentation into a subdir of your project (we assume your Doxygen output directory is `./Doxygen/html`).

With that at hand, we can start setting everything up.


## Initial setup of the submodule

So here's what we need to do. First, let's remove the old Doxygen output (and commit that):

{% highlight bash %}
# go to the root directory of your project:
$ cd exampleProject	
# delete the html dir:
$ rm -R Doxygen/html
# add and commit:
$ git add -A
$ git commit -m "Removed html dir (to be re-added as submodule)"
{% endhighlight %}

Next, we add the submodule, placing it in the position of the Doxygen output folder:

{% highlight bash %}
$ git submodule add git@bitbucket.org:martinh2/spaop.git Doxygen/html
{% endhighlight %}

Now, the project's `master` branch should automatically be cloned into that folder. We go there, create the gh-pages branch and check it out:

{% highlight bash %}
$ cd Doxygen/html
$ git checkout -b gh-pages
{% endhighlight %}

Now that branch still contains the whole `master` branch. For the `gh-pages` branch, we want to get rid of all of that:

{% highlight bash %}
# delete everything within that folder:
$ rm -R *
# add and commit:
$ git add -A
$ git commit -m "Deleted everything"
{% endhighlight %}

Within that directory, there is no use for any other branch but the `gh-pages` branch, so let's remove the `master` to avoid possible confusion:

{% highlight bash %}
# delete master branch:
$ git branch -d master
{% endhighlight %}

This is a good moment to add a README.md that explains what that branch is about. For this tutorial, we'll keep the README's content short:

{% highlight bash %}
# create file:
$ touch README.md
# add content:
$ echo "This is the Github page branch" > README.md
# add and commit:
$ git add README.md
$ git commit -m "Added branch-specific README.md"
{% endhighlight %}

Usually when you push a `gh-pages` branch, Github will automatically run Jekyll to generate html from markdown files. That's a nice feature for pages like this blog, but it can cause trouble for Doxygen: Jekyll will cause files starting with an underscore to be ignored, but certain Doxygen-generated html files start with an underscore. Hence, we must deactivate Github's Jekyll rendering by adding an empty file named `.nojekyll`:

{% highlight bash %}
# create file:
$ touch .nojekyll
# add and commit:
$ git add README.md
$ git commit -m "Added .nojekyll file"
{% endhighlight %}

Finally, we're ready to re-render the Doxygen output. Do that just like you're used to.

Once the Doxygen output is re-rendered, it's time to commit and push it:

{% highlight bash %}
# still in the same directory as above:
$ git add .
$ git commit -m "Initial Doxygen commit"
$ git push -u origin gh-pages
{% endhighlight %}

Finally, we commit the changes of the submodule to the `master` branch:

{% highlight bash %}
# go to the root dir of the master branch:
$ cd ../..
## add, commit and push:
$ git add .
$ git commit -m "Set up html directory as gh-pages submodule"
$ git push -u origin master
{% endhighlight %}

That's it.

## Updating the Doxygen Github page

Once everything is setup, whenever you want to update the Doxygen page, just re-render your Doxygen output and repeat the last few steps described after re-rendering the Doxygen output: add, commit and push all changes to the `gh-pages` branch and commit the changes of the submodule to the `master` branch (or create some script that does that...).

[^1]: special thanks to [Ricky Foos who explains the basics without the use of a submodule](http://rickfoosusa.blogspot.de/2011/10/howto-use-doxygen-with-github.html))