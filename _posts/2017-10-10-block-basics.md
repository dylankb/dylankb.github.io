---
layout: post
title: "Building Blocks: A Fundamental Part of Ruby"
comments: true
---

After disappearing into the world of JavaScript for a quite a spell, I found I needed to go back and review how Ruby blocks work. At this point I hadn't seen a good, freely accessible explanation for how blocks worked so I thought it would be nice to do a bit of a write up.[^1],

Here are the two points I'm going to try and explain:

1. How blocks work at a basic level.
2. How to create methods that use blocks.

### Block basics

Below is a common use case for blocks - calling a block on a Ruby standard library methods such as `each`.

```ruby
>[1, 2, 3].each do |num|
   puts num
  end
1,2,3
```

There are three parts to this method call:

1. `[1,2,3]` - a collection of elements that implements Enumerable, such as an array or hash
2. `.each` -  a method that can utilize a block  
3. `do...end` - everything within `do ... end` is the block that is passed to the method  

```ruby
do |num|
  puts num
end
```

So that's the anatomy of a block in action, but the simple syntax of Ruby seems to be hiding some details from us. How exactly are these three parts of a block call working together?

#### Methods, blocks, and yields

All Ruby methods can technically take a bloc. Here's the simplest example of a method taking a block.

```ruby
def hello_world
  puts "Hello world"
end

hello_world { puts "World replies: Hey!" }
> "Hello world"
```

Hmm? So while our `hello_world` method can take a block, it's not exactly doing anything with it. Let's start changing that:

```ruby
def hello_world_conversation
  puts "Hello world"
  yield
end

> hello_world_conversation { puts "World replies: Hey!" }
"Hello world"
"World replies: Hey!"
```

If you aren't familiar with the Ruby `yield` keyword, it's in effect saying this: "Hey, at this point in my method I want a block to run. I don't really know what block, blocks are like a parameter so I can't know the exact contents ahead of time, but if someone wants to pass me a block when they call the method `hello_world_conversation`, that's cool."

#### Passing parameters to a block

Another important point to using method and blocks is that methods can pass parameters to blocks through `yield()`.

```rb
def hello_world_conversation(reply)
  puts "Hello world"
  yield(reply)
end

> hello_world_conversation('Goodbye!') do |reply|
    puts "World replies: #{reply}"
  end
"Hello world"
"World replies: Goodbye!"
```

To summarize, our method takes a parameter, yields to the block, the block prints out a statement using that argument string. Now that we're familiar with some of the building "blocks" (pardon the pun), let's move on to using iteration with blocks.

#### Iteration is the name of the game

What if we wanted the our "World" entity to reply a couple of times? The basics are already there - we just need to figure out how to do iteration inside our method.

```js
> replies = [ 'Hey!',
            'How\'s it going?',
            'Code here often?',
            'Goodbye!' ];

> hello_world_conversation(replies) do |reply|
    replies.length.times do |index|
      puts "World replies: #{replies[index]}"
    end
  end

"Hello world"
"World replies: Hey!"
"World replies: How's it going?!"
"World replies: Code here often?"
"World replies: Goodbye!"
```

This is pretty cool, but we should probably think about what the relationship should be between our method and our block. Right now our method takes in parameters and the block iterates over them and decides how to print. Since each time this method is called it will iterate through the conversation, let's move that logic into the method and out of the block.

```ruby
def hello_world_conversation(replies)
  puts "Hello world"
  replies.length.times do |index|
    yield(replies[index])
  end
end

> hello_world_conversation(replies) do |reply|
    puts "World replies: #{reply}"
  end
```

Ok, believe it or not while trying to add some simple bits of additional functionality to our `hello_world_conversation` method, we more or less reimplemented how `each` works!

```ruby
> replies.each { |reply| puts "World replies #{reply}"}
"World replies: Hey!"
"World replies: How's it going?!"
"World replies: Code here often?"
"World replies: Goodbye!"
=> ['Hey!', 'How\'s it going?', 'Code here often?', 'Goodbye!' ]
```
Note that the code above does not print out `"Hello World"`.

If the `Enumerable#each` method was ever confusing to you, I think what we learned so far gives us a good framework for describing how `each` works internally. Let's go over the high level steps below:

- `each` starts iterating through the of the items in the collection (`replies`)
- it yields the current element to the block
- returns the original collection

If you've never tried before, I would go ahead and try and code up your own version of `Enumerable#each`. For simplicity's sake, I would recommend just passing in the collection to iterate over like this: `each(collection)`. If you need some inspiration, I have another post goes over more complicated `Enumerable` methods [here]({{ site.url }}{% link _posts/2016-2-14-a-closer-look-at-map-select-reduce.md %}).

 There's a lot more you can do with blocks, but hopefully this above covered some of the basics. I've found it's often not a bad place to start. :)

[^1]: You never notice what actually exists until you look for it :) I've since found several good posts on the subject, including [here](https://www.skorks.com/2009/09/using-ruby-blocks-and-rolling-your-own-iterators/) and [here](http://radar.oreilly.com/2014/02/why-ruby-blocks-exist.html)
