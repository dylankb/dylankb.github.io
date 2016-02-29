---
layout: post
title: "A Beginner's Guide to Creating a Simple, Free Jekyll 3 Blog"
---

The goal of this blog post is to allow anyone with at least some technical curiousity to set up a fast and free blog or pesonal website that doesn't look half bad. 

1. **Fast** - We'll be using Jekyll, a static site generator, which does not use a database which is part of what will make our site [fast](http://blog.formkeep.com/why-you-should-use-a-static-site-generator/). 
2. **Free** - The free is because we are going to use [Github Pages](https://pages.github.com/) to host our site for free. 
3. **Not ugly** - The site isn't going to look terrible because we're going to use a Jekyll theme designed by the [creater](http://mdo.fm/) of Bootstrap.

Sounds pretty cool, right?

Well, despite there being a fair amount of blog posts covering how to set up a Jekyll blog, some of [the best tutorials](http://joshualande.com/jekyll-github-pages-poole/) are a little out of date and don't cover all the challenges someone could run into during set up. When I set up mine I had to refer to several other posts to patch together a solution, which made the whole process take a little time.

I'll try to cover the main steps for setting up a working environment for Mac OSX and Linux Ubuntu 14.04 users, installing a theme, and deploying our site.

### Part I: Getting set up

For many this may be the most challenging part, but a lot of posts skip over this section. It requires some persistance, but it's really not that difficult.

I try to flag programs that aren't strickly required if you're not a programmer, but I don't always. You're making a Jekyll blog, though, so you're probably a bit of a hacker ;)

**<u>Mac users</u>**

1. Install Xcode Command Line Tools
2. Install Hombrew
3. Install Node.js
4. Setup Github and install command line tools
5. Install a text editor
6. Install a Ruby version manager (optional)

A great blog post that goes over how to do steps 1, 2, 3, 5, and 6 is [here](https://launchschool.com/blog/how-to-install-ruby-on-rails-development-environment-for-mac-os-x). It does go depth into utilizing programming tools, so I include some alternative links and steps that you can follow below as well.

#### Install Node.js, Homebrew, and Xcode. 

Fortunately [this](http://blog.teamtreehouse.com/install-node-js-npm-mac) nice blog post covers how to set up all of those programs. Additionally the post goes into the benefits for using Homebrew, and package managers more generally, to install your programs.

#### Get a text editor

To set up our Jekyll site you will be working in code, so it's best to have a text editor at the ready. If you don't already have one I suggest using [Sublime](https://www.sublimetext.com/) or [Atom](https://atom.io/).

**<u>Linux Ubuntu users</u>**

1. Install a Ruby version manager (optional) and install Ruby (required)
2. Set up Github account and install command line tools 
3. Install a text editor

A companion blog installation blog post to the Mac OSX guide is [here](https://launchschool.com/blog/how-to-install-ruby-on-rails-development-environment-for-linux) for Linux users.

#### Git and Github

The core programs for running Jekyll required code are now installed. Next we need to install the program that's going to host our code. If you've never used Git this pretty much sums up my feelings on it.

![placeholder](http://imgs.xkcd.com/comics/git.png)

Yeah, so there are whole books on mastering git fu, but for our purposes make sure you have an account created on [Github](www.github.com) and that you've installed the command line application for git. Directions for set up can be found [here](https://help.github.com/articles/set-up-git/). You can also use your package manager to install the CLI.

Mac
`brew install git`

Linux 
`sudo apt-get install git`

### (Optional) Set up RBENV to manage Ruby versions

If you're going to be using Ruby for web development then it's highly recommended to install either RVM or RBENV to manage your Rubies. I've always used RBENV, and it's served me well. 

For Mac users that are only using Ruby in order to get set up with Jekyll you probably don't need to go any further since most Macs have Ruby 2.0 preinstalled. However if when you type `ruby -v` into your terminal and get back something less than 1.9.3 or an error message then you'll need to install Ruby.

Using Homebrew follow these instructions [here](https://github.com/rbenv/rbenv#homebrew-on-mac-os-x) to setup RBENV. Remember to set your global Ruby version!

Linux users will need to download Ruby. I recommend Linux users read [this](https://launchschool.com/blog/how-to-install-ruby-on-rails-development-environment-for-linux)
) blog post to get set up on RBENV for Ubuntu Linux. For our purposes you only need to follow step 3, although there is more good advice on setting up a nice development environment.

One thing to note, you can ignore running cloning the rehash gem. This gem is deprecated because this functionality was added to RBENV.

### Part II: Getting our theme

Finally we're going to start getting the blog rolling. Here is a demo of the [Poole](http://demo.getpoole.com/) theme that we're going to set up in this tutorial. If that's not to your liking, [here](http://getpoole.com/) are some other choices built using Poole, but I haven't tested these setup instructions with those themes.

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
Enter the server address into your browswer and you should see content there! While running `jekyll serve` you can make changes to your blog content and see them reflected live.

### Hosting our blog

In the previous section we built a blog that is available on locally. Now let's host it so it's live. 

1. Create a repository called `USERNAME.github.io` with your individual information filled in.
2. `git clone https://github.com/username/username.github.io`
3. Copy and paste all of the files (not the folder) from the Poole theme into your local folder for your github.io repository.
4. Stage your files, commit, and push to deploy your code.

If some of this is unclear [Github Pages site](https://pages.github.com/) provides a nice demonstration of how to set up your Github Page repo and starting deploying to it.

In a few moments you should be able to see the live version of your blog! Just enter the name of the repository (USERNAME.github.io) into your browser.

### Other thoughts

Something that isn't explicity spelled out anywhere I've found is that when updating your site all you need to do now is this:

1. Make your changes and stage them.
2. Commit
3. Push to correct branch (default origin master)

There's no need to run `jekyll build` if you're using a Github Pages user organizations page with this configuration.

Read the [Jekyll](http://jekyllrb.com/) documentation to get a better handle on what frontmatter is, and other sometimes Jekyll specific quirks, but you've got a working site now. Hooray!

If something isn't clear, doesn't work right, or you have other suggestions then feel free to let me know.
