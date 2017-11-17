---
layout: post
title: "A Closer Look at <code>select</code>, <code>map</code>, and <code>reduce</code>: Avoiding Common Gotchas"
comments: true
---

After learning the basic components of the Ruby syntax I started to use some of the more complicated methods that make Ruby such a fun language. You know, the nifty ones in [Enumerable](http://ruby-doc.org/core-2.3.0/Enumerable.html).

Sometimes you find and awesome tool and just immediately start using it. Often times that's fine, but whether it's a nail gun or a standard library method if you don't know how the tool actually works it's:

1. Harder to fix it when it breaks
2. Harder to know when to use it

It wasn't until someone asked me what would happen if they put a stray `puts` inside a `select`,`map`, or `inject` that I realized I didn't 100% grasp how these method worked internally. After looking into exactly how blocs are implemented in Ruby using yield and doing a little experimentation, I feel like the next debugging session won't be such a nightmare.

To make sure we're on the same page, I'd recommend first reading another blog post of mine [here]({{ site.url }}{% link _posts/2017-10-10-block-basics.md %}) that goes over 1) how blocks work at a basic level, and 2) how to create methods that use blocks.

### Looking at select, map, and reduce

Below I've included basic implementations of the methods `select`, `map`, and `reduce`to illustrate how these methods work internally. I won't use these implementations in the example snippets (although they work just fine) to make it less confusing and easier to run the snippets on your own computer. Let's start with ...

### 1) **`select`**

The ruby-docs definition of select is: “returns an array containing all elements of enum for which the given block returns a true value.”

```ruby
def select(array)    #Basic implementation of select
  result = []
  counter = 0

  while counter < array.size
    current_element = array[counter]
    result << current_element if yield(current_element)

    counter += 1
  end
  result
end
```

Here's an example that you'd probably never see in real life, but I think it's a good example of behavior that can seem weird at first glance.

```ruby
>> result = [1,2,3].select do |num|
>>   0
>> end
=> [1,2,3]
```

If I saw this code before trying these experiments, I'd probably frown and mutter something about trying to memorize this weird behavior. By creating my own `select` method though, I find it way easier to understand what's going on here. Let's go over the `select` method we defined above's the moving pieces to understand it a bit better.

**A question of "truthiness"**

Arguably a more precise way of phrasing this definition is to say it "returns an array of elements of enum for which the given block returns a *"truthy"* value. When we say truthy value, we mean that all Ruby objects can be evaluated to a `true` or `false` boolean value[^1]. While [not a great coding practice](https://github.com/bbatsov/ruby-style-guide#no-bang-bang) in general, you can test any object with the double bang (`!!`) to see how it will evaluate.

```ruby
>> !!0 # 0 is "truthy" in Ruby
=> true
>> !!nil
=> false
```

Let's break down. The `while...end` and the incrementing `counter` allows us to loop through each element in the collection. Then line 7 of our `select` method definition adds the `current_element` to the `results` collection if the value passed into the block with `yield(current_element)` is evaluated as truthy. In the example above `0` is a truthy value so each return value from yielding to the block is truthy, therefore each array element gets passed to `result`.

**Pay attention to the return value**

Like methods, the last line executed in the block is the block’s return value

```ruby
>> result = [1,2,3].select do |num|
>>   num > 2
>>   puts num  
>> end
=> []
```

Working through the lines of our implementation we can see why the return value of the method call was an empty array. `puts` always returns a `nil` value, and `nil` (along with false) are "falsey", so therefore no elements in the array of numbers were pushed to the new array.

### 2) **`map`**

Map returns an array of the results of running the block on each value in a collection. I'll also switch to `each` as the iteration mechanism to shorten up the method a bit.

```ruby
def map(arr) # Basic implementation
  count = 0
  output = []

  arr.each do |element|
    output << yield(element)
    count += 1
  end
  output
end
```

This is very different from how `select` works, as demonstrated below.

```ruby
>> output = [1,2,3].map do |num|
>>   num < 3
>> end
=> [true, true, false]
```

Again, referencing the the `map` method implementation line 6 you can actually see the method taking in each value the collection, setting that value to the result of the block, and then pushing that item to a new array.

**Keep paying attention to the return value**

I think the above code demonstrates that you must be very careful when using `select` like logic in your `map` block because every element will be modified by the return value of the block. In other words, each element in the map array is whatever the last line of the block returns during that iteration.

Let's pretend we're just getting started with Ruby, and decided to write a method that changes `num` values equal to 3 to `X` while leaving the other values unchanged.

```ruby
>> output = [1,2,3].map do |num|
>>   if num == 3
>>     num = 'X'
>>   end
>> end
=> [nil, nil, 'X']
```

Again, weirdness. We're getting `nil` values for all values not equal to 3. Let's check why this is happening by testing out a snippet in the terminal.

```ruby
>>  num = 2
>>  if num == 3
>>    num = 'X'
>>  end
=> nil
```

The return value is `nil`, and therefore each element that does not equal 3 will have a return value of `nil`. A way to address this is to use logic the enables your block to positively evaluate each element.

```ruby
>>   if num == 3
>>     num = 'X'
>>   else
>>     num
>>   end
=> [1, 2, 'X']
```

Ah, this gets us to a more normal behavior. We can also shorten a couple lines of our code by using a ternary operator.

```ruby
num == 3 ? 'X' : num
```

### 3) **`reduce`/`inject`**

Reduce yields each element in a collection plus an accumulator to the block.

```ruby
def reduce(array, ival=0)     #Basic implementation of reduce
  counter = 0
  sum = ival

  while counter < array.size
    current_element = array[counter]
    sum = yield(sum, current_element)
    counter += 1
  end
  sum
end
```

The accumulator is set to the value returned by the block, and then passed on to the next yield. Therefore even if you set the default accumulator to an empty array, reduce will still work very differently than `map` or `select`.

```ruby
>> result = [1,2,3].reduce([]) do |acc, num|
>>   num * 2
>> end
=> 6
```
In the example above `1` is the first value for `num` and the first to be yielded to the block. The accumulator object `acc` is then set to `1`. In the next yield the accumulator will be set to `2` and so on until the last value `3` is multipled by two to return `6`. Due to its method definition, `reduce` will not add each element returning a truthy value to an array unlike `select` unless specified in the block.

```ruby
>> result = [1,2,3].reduce([]) do |acc, num|
>>   acc << num * 2
>> end
=> [2,4,6]
```

**Should we pay attention to the return value?**

The accumulator is able to carry the accumulator (a given value or collection) across iterations because the accumulator is returned at the end of each block. Below the correct accumulator is not returned.

```ruby
>> result = [1,2,3].reduce([]) do |acc, num|
>>   acc << num * 2
>>   puts 'hi'
>> end

=> 'hi'
=> # Error: Undefined method '<<' for nil:NilClass
```

This is easier to tell by looking at this line `sum = yield(sum, current_element)`. In this example, `puts` returns `nil`, so if we pass `nil` and the the current element into the block, it will try to do `nil << 2 * 2`, which throws an error.

I'm sure I'll continue to haphazardly wield shiny things in the future. At least next time I may know more about what I don't know, so to speak. :)

[^1]: http://phrogz.net/ProgrammingRuby/language.html#truthvalues
