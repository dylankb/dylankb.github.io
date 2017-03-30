---
layout: post
title: "A Beginner's Guide to Creating a Simple, Free Jekyll 3 Blog"
comments: true
related: ["Customizing your Jekyll Blog: Adding Comments, Sections, Custom Related Posts, and more!"]
---

The goal of this blog post is to allow anyone with at least some technical curiosity to set up a fast and free blog or personal website that doesn't look half bad.

1. **Fast** - We'll be using Jekyll, a static site generator, which does not use a database which is part of what will make our site [fast](http://blog.formkeep.com/why-you-should-use-a-static-site-generator/).
2. **Free** - The free is because we are going to use [Github Pages](https://pages.github.com/) to host our site for free.
3. **Not ugly** - The site isn't going to look terrible because we're going to use a Jekyll theme designed by the [creator](http://mdo.fm/) of Bootstrap.

Sounds pretty cool, right?

Well, despite there being a fair amount of blog posts covering how to set up a Jekyll blog, some of [the best tutorials](http://joshualande.com/jekyll-github-pages-poole/) are a little out of date and don't cover all the challenges someone could run into during set up. I had to refer to several other posts to patch together a solution while working on mine, which made the whole process take a little time.

I'll try to cover the main steps for setting up a working environment for Mac OSX and Linux Ubuntu 14.04 users, installing a theme, and deploying our site.

### Part I: Getting set up

For many this may be the most challenging part, but a lot of posts skip over this section. It requires some persistence, but it's really not that difficult.

I try to flag programs that aren't strictly required if you're not a programmer, but I don't always. You're making a Jekyll blog, though, so you're probably a bit of a hacker ;)

**<u>Mac users</u>**

1. Install Xcode Command Line Tools
2. Install Hombrew
3. Install Node.js
4. Setup Github and install command line tools
5. Install a text editor
6. Install a Ruby version manager (optional)

A great blog post on setting up a machine to do Ruby on Rails development over how to do steps 1, 2, 3, 5, and 6 is [here](https://launchschool.com/blog/how-to-install-ruby-on-rails-development-environment-for-mac-os-x). It does go deep into utilizing programming tools, so I include some alternative links and steps that you can follow below as well.

#### Install Node.js, Homebrew, and Xcode.

Fortunately [this](http://blog.teamtreehouse.com/install-node-js-npm-mac) nice blog post covers how to set up all of those programs. Additionally the post goes into the benefits for using Homebrew, and package managers more generally, to install your programs.

#### Plain text - Get a text editor

 To set up our Jekyll site we'll be using plain text files. Unlike platforms like Wordpress, there's no graphical user interface, website, or desktop program to build the site, so all configuration is done in text files. If you're a developer, this is so normal that you wouldn't even think about it, but if you're not that's the paradigm we're working in. Now that we realize we're working with text files, we need to be sure that the text files contain no strange formatting, which is why we need to avoid things like Microsoft Word or other word processors. If you don't have a plain text editor, I suggest using [Sublime](https://www.sublimetext.com/) (paid) or [Atom](https://atom.io/) (free).

**<u>Linux Ubuntu users</u>**

1. Install a Ruby version manager (optional) and install Ruby (required)
2. Set up Github account and install command line tools
3. Install a text editor

A companion blog to the Mac OSX guide for Linux users is [here](https://launchschool.com/blog/how-to-install-ruby-on-rails-development-environment-for-linux) for Linux users.

#### Git and Github

At this point we've installed the core programs required to run Jekyll. Next we need to install the program that's going to allow to track changes to our code: Git. We're choosing Github because it will not only allow us to track changes, but allow us to use "push" our code to a remote repository which will be the source for our free [Github pages](https://pages.github.com/) hosted site.

If you've never used Git this pretty much sums up my feelings on it. Or, at least, it was how I felt for the first year I was programming.

![placeholder](http://imgs.xkcd.com/comics/git.png)

Yeah, so there are whole books on mastering git fu, but for our purposes make sure you have an account created on [Github](www.github.com) and that you've installed the command line application for git. Directions for set up can be found [here](https://help.github.com/articles/set-up-git/). You can also use your package manager to install the CLI (command line interface).

Mac
`brew install git`

Linux
`sudo apt-get install git`

### (Optional) Set up rbenv to manage Ruby versions

If you're going to be using Ruby for web development, then it's highly recommended to install either [rvm](https://rvm.io/) or [rbenv](https://github.com/rbenv/rbenv) to manage your Rubies. This is because different projects you work on may require different versions of Ruby, and package managers make switching versions much simpler. I've always used rbenv, and it's served me well.

For Mac users that are only using Ruby, in order to get set up with Jekyll you probably don't need to go any further since most Macs have Ruby 2.0 preinstalled. However if when you type `ruby -v` into your terminal and get back something less than 1.9.3 or an error message then you'll need to install a 2.0 or greater version of Ruby.

If you do decide to set up rbenv, the only thing that should be noted is that all you need to do when installing it via Homebrew is run `brew install rbenv` as per the documentation [here](https://github.com/rbenv/rbenv#homebrew-on-mac-os-x). There's no need to install the rehash gem because it was deprecated; the functionality from this gem was added to rbenv core.

For Linux users you can likewise ignore cloning the rehash gem.

Remember to set your global Ruby version!

### Part II: Getting our theme

Finally we're going to start getting the blog rolling. Here is a demo of the [Poole](http://demo.getpoole.com/) theme that we're going to set up in this tutorial. If that's not to your liking, [here](http://getpoole.com/) are some other choices built using Poole, but I haven't tested these setup instructions with other themes.

The first step is to extract zip files you download from the [theme repository](https://github.com/poole/poole) into a folder.

Awesome, we're getting a lot closer. Now let's check out the `_config.yml` file. This is an important file for determining the setup of our blog. When you open it you'll see at the bottom these few lines.

```
gems:
  - jekyll-paginate
  - jekyll-gist
```

This means that we'll need to install these gems, along with the `jekyll` gem itself, in order to serve HTML & CSS using our Jekyll project. Run the line below to install these gems:

`gem install jekyll, jekyll-paginate, jekyll-gist`

Once this is complete, use your terminal to `cd` to the main folder of the project and run `jekyll serve`. You should see something similar to this:

```
# ... Omitted lines ...
Server address: http://127.0.0.1:4000/
Server running... press ctrl-c to stop.
```
Enter the server address into your browser and you should see content there! While running `jekyll serve` you should be able to make changes to the sample blog content using your text editor, and see those changes reflected on page refresh.

### Hosting our blog

In the previous section we built a blog that is able to build and render locally. Now let's host it so it's live.

1. Create a repository called `USERNAME.github.io`, replacing USERNAME with your Github username.
2. `git clone https://github.com/username/username.github.io`
3. Copy and paste all of the files (not the folder) from the Poole theme into your local folder for your github.io repository.
4. Use git to stage, commit, and push your new files to deploy your code.

If some of this is unclear, the [Github Pages site](https://pages.github.com/) provides a nice demonstration of how to set up your Github Page repo, start contributing code, and ultimately deploy a site.

In a few moments you should be able to see the live version of your blog! Try entering the name of the repository you created (USERNAME.github.io) into your browser and visiting that site.

### Other thoughts

Something that isn't explicity spelled out anywhere I've found is that when updating your site all you need to do now is this:

1. Make your changes and stage them.
2. Commit
3. Push to correct branch (default origin master)

There's no need to run `jekyll build` if you're using a Github Pages user organizations page with this configuration.

Finally, make sure to read some the [Jekyll](http://jekyllrb.com/) documentation to get a better handle on [how Jekyll works](https://jekyllrb.com/docs/usage/), the [configuration file](https://jekyllrb.com/docs/configuration/) system, and what [frontmatter](https://jekyllrb.com/docs/frontmatter/) is.

A shortcut to a solid understanding of all the moving parts in our Jekyll blog is to just open up one of the existing posts included in the `_posts` directory and start making edits to it to see how the system works. Don't forget to use `jekyll serve` preview your changes!

If something isn't clear, doesn't work right, or you have other suggestions then feel free to let me know. Also feel free to check out the project's [Github repository](https://github.com/dylankb/dylankb.github.io).
