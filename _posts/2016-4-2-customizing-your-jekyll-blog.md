---
layout: post
title: "Customizing your Jekyll Blog: Adding Comments, Sections, Custom Related Posts, and more!"
comments: true
related: [
"A Beginner's Guide to Creating a Simple, Free Jekyll 3 Blog"
]
---

In a previous [post]({{ site.url }}{% link _posts/2016-2-29-a-beginners-guide-to-jekyll.md %}) on setting up a Jekyll blog we were able to set up our development environment, install a nice looking theme, and take that theme live for free. However, to make this site just a bit more functional I added a couple more features. Below I'll cover either what customizations I made myself or the resources I referred to.

### New Sections: Archive and Menu

To create a list of all the posts in the `_posts` directory, I followed Joshua Landon's [blog post](http://joshualande.com/jekyll-github-pages-poole/). His post also goes over how to create the top side menu system so that you have navigate between your About page, Archive, or any other section you've set up.

### Comments

I also followed the basic outline in Joshua's blog post to set up Disqus comments, so I encourage you to read that section to get started. It's important to note that the format for code Disqus wants you to put on your site has changed. The purpose of this code is so that Disqus can create [unique ids](https://help.disqus.com/customer/en/portal/articles/2158629) for each of your pages using the Disquss service.

I do not believe the Disqus site has any documentation about how to best implement use Jekyll for the new format, so I used the following [post](https://joshuacox.github.io/jekyll/2015/11/27/disqus-and-jekyll/) as a guide.

### Short URLs

By default URLs in Jekyll may contain date and tag info, but I wanted a URL that only used the blog post title. There are solutions where you can set short URLs in the frontmatter, but you should use the power of the `_config` file to save you some time by setting defaults.

Specifically [this](https://jekyllrb.com/docs/configuration/#front-matter-defaults) section of the Jekyll docs will be your friend.

In my case this is how I used defaults to set short urls.

```
defaults:
  -
    scope:
      path: "" #
      type: "posts"
    values:
      permalink: 'archive/:title'
```

Basically all this is saying is that all posts should have a permalink value of 'archive' + post title. Doing this had the added benefit of ensuring all the URLs inside my Archive section of the blog were consistent with the actual post URLs.

### Custom Related Posts

By default our theme includes three random blog posts. I wanted to take out the random related posts feature and be able to manually create the list of related posts, or hide this section if there were none.

If you're interested in creating a similar system for related posts, I used this [blog post](https://lauris.github.io/blog/jekyll-related-posts) as a guide.

You can see from the comment I left on the blog post that I don't actually have a great grasp on the liquid templating language that Jekyll uses, yet :)

I'll probably continue making changes, but once I got to this point I was pretty happy with the simple writing platform I had made.

As always let me know if something was unclear in the comments below, and feel free to checkout the project's [Github repository](https://github.com/dylankb/dylankb.github.io).

Happy Jekylling!
