---
layout: post
title: "Creating code snippet screenshots with the Atom text editor"
comments: true
---

While I was preparing for a presentation, I decided I wanted to up the quality of my code screenshots game a bit this time. There are some great existing tools out there like [Carbon](https://carbon.now.sh). However, I had a somewhat tricky requirement that lead me to instead play around with some Atom settings to get the setup I wanted. Below I go over how I set up my editor to hide editor features such the wrap guideline, indentation guides, and the cursor in order to improve my screenshots.

To start off, the tricky requirement I mentioned earlier was the ability to highlight code by manually selecting it.

#### Highlighting selected sections of code

While going through larger code screenshots I needed to be able to highlight smaller sections of code at a time to help the audience follow along. I could of course use some other software to process these highlights, but Atom's selection highlighting feature should work fine and be would probably be faster. Since the highlighting functionality comes with Atom, there was only one tweak I was going to need to do to get it to work.

**Hide Atom's cursor**

By default, Atom displays a blinking cursor which I would need to deal with if I wanted screenshot without a cursor. I searched Google for how to hide a cursor, but the only results that turned up concerned how to stop the cursor from blinking and [just display](https://discuss.atom.io/t/how-to-stop-the-blinking-of-cursor/22790) (remain solid).

Editing Atom

Fortunately since I also wanted to stop the blinking in a sense but just leave it hidden, I was able to modify code for having a solid cursor for my purposes. To do this I opened the customizable stylesheets by clicking Atom > Stylesheets to access the `styles.less` file. You can also reference [this page](https://flight-manual.atom.io/using-atom/sections/basic-customization/#style-tweaks) for instructions on how to access this file.

After doing this, all I did was modify a snippet of code used to stop the blinking aspect of the cursor. However instead of setting `opacity: 1`, I changed the code to `opacity: 0`. If you want the same functionality, feel free to copy and paste the code snippet below into the bottom of the `styles.less` file.

```
// Hides Atom's cursor
atom-text-editor, atom-text-editor::shadow {
  .cursors .cursor {
    opacity: 0;
  }
}
```

When you want your cursor functionality back, all you need to do is comment out the CSS!

#### Hide wrap guideline

Optional change since obviously none of your lines of code are over 80 characters ;) If you do want to make this change, go to Packages > Settings View > Packages and enter 'wrap-guide'. If you have this default Atom package still installed, you can now temporarily disable the package.

#### Hide indentation guides

To open the `config.cson` file that controls these settings, go to Atom > Config and then change the `showIndentGuide` key's value to `false`.

You can also access these options by going directly to the file. On the Mac OS it's located at `/Users/PROFILE/.atom/config.cson`

### Results

My slides had a white background, so I chose the Base16 Tomorrow Light as my syntax theme. Here's a screenshot I took with the above settings.

![node-tcp-server](/assets/tcp-node2.png)

If the above changes sounded like too much of a hassle, here's some similar settings for the [Carbon](https://carbon.now.sh/?bg=rgba(171,%20184,%20195,%201)&t=base16-light&wt=none&l=auto&ds=true&dsyoff=20px&dsblur=68px&wc=false&wa=true&pv=0px&ph=0px&ln=true&fm=Hack&fs=18px&si=false&es=2x&wm=false) tool.
