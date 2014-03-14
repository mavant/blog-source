Title: GitHub Pages Publication with Git Hooks
Date: 2014-03-08
Category: Projects
Tags: github pages, pelican, git, blogging
Slug: github-pages-publication-git-hooks
Author: Amy Hanlon

As I [wrote](http://mathamy.com/migrating-to-github-pages-using-pelican.html) at length a couple weeks ago, this blog is hosted on [GitHub Pages](http://pages.github.com/) and generated by [Pelican](http://docs.getpelican.com/en/3.3.0/). Generally the integration between the two is quite blissful, except for managing two separate repositories - [blog-source](https://github.com/amygdalama/blog-source) for my blog's source content, configuration files, and theme, and [amygdalama.github.io](https://github.com/amygdalama/amygdalama.github.io) for my Pelican-generated site.

Lately I've been pushing to these two separate repositories manually, so my workflow looks something like:

    :::bash
    $ cd blog/      # root directory for my blog
    $ make devserver    # automatically re-generates site and hosts site locally
    # change something in content or settings
    $ git add --all
    $ git commit -m "committing blog source"
    $ git push origin master    # pushes to my blog-source repo on GitHub
    $ cd output/    # pelican-generated output folder
    $ git commit -am "committing pelican-generated site content"
    $ git push origin master    # pushes to my amygdalama.github.io repo

But alas! I have figured out how to automatically push any changes committed in my root blog directory to my blog-source repo and then automatically add, commit, and push changes to my GitHub Pages repo. Now my workflow looks like:

    :::bash
    $ cd blog/
    $ make devserver
    # change something in content or settings
    $ git add --all
    $ git commit -m "committing blog source"
    # everything else happens automatically! no more typing! yay!

The key is to use [Git Hooks](http://githooks.com/). For a particular .git repo, you can add an executable file in `.git/hooks` which will automatically execute before or after an event like `commit`. The available types of hooks can be found [here](http://githooks.com/).

For this specific automation task, I used a post-commit hook. To do this, first create the file `.git/hooks/post-commit`:

    :::bash
    $ cd blog/
    $ subl .git/hooks/post-commit

Add these lines to the file:

    :::bash
    #!/bin/bash
    git push origin master
    cd output/
    git add --all       # sub-optimally will add all even if you didn't add all to blog-source
    git commit -m "automatic commit"    # add whatever commit message you want
    git push origin master

Then, to make the file executable, type:

    :::bash 
    $ chmod a=r+w+x .git/hooks/post-commit

And that's it! Joyful automation!

##Edit

*2014-03-14*: Hacker Schooler Matthew Avant has a [superior](http://www.mavant.com/4.html) method using a pre-push hook (rather than a post-commit hook). I'm using his method now :)