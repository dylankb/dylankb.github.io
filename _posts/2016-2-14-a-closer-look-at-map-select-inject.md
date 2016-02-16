---
layout: post
title: "A Closer Look at Select, Map, and Inject: Avoiding Common Gotchas"
---

After learning the basic components of the Ruby syntax I started to use more advanced, sometimes Ruby specific methods. You know, the nifty ones in [Enumerable](http://ruby-doc.org/core-2.3.0/Enumerable.html).

Sometimes you find and awesome tool and just immediately start using it. Often times that's fine, but whether it's a nail gun or a standard library method if you don't know how the tool actually works it's

1. Harder to fix it when it breaks
2. Harder to know when to use it

It wasn't until someone asked me what would happen if they put a stray `puts` inside a `select`,`map`, or `inject` that I realized I didn't 100% grasp how these method worked internally. After looking into exactly how blocs are implemented in Ruby using yield and doing a little experimentation, I feel like the next debugging session won't be such a nightmare.

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

Arguably a more precise way of phrasing this definition is to say it returns an array of elements of enum for which the given block returns a “truthy” value.

```ruby
>> result = [1,2,3].select do |num|
>>   0
>> end
=> [1,2,3]
```

If I saw this code before trying these experiments I'd probablyfurrow my brows and mutter something about trying to memorize this weird behavior. By creating my own `select` method though, I find it way easier to understand what's going on here. Looking at line 8 you can actually see the method taking in each value the collection, and if value passed into the block is evaluated as truthy by way of `yield(current_element)` then that value is pushed to the new array of values.

Like methods, the last line executed in the block is the block’s return value

```ruby
>> result = [1,2,3].select do |num|
>>   num > 2
>>   puts num  
>> end
=> []
```

Working through the lines of our implementation we can see why the return value of the method call was an empty array. `puts` always returns a `nil` value, and `nil` (along with false) are "falsey", and therefore not pushed to the new array.

### 2) **`map`**
 
Map returns an array of the results of running block on each value in a collection. 

```ruby
def map(arr, default=0) # Basic implementation
  count = 0
  output = []

  while count < arr.size
    output << yield(arr[count])
    count +=1
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

Again, using the implementation and looking at line 6 you can actually see the method taking in each value the collection, setting that value to the result of the block, and then pushing that item to a new array.

I think the above code demonstrates that you must be very careful when using `select` like logic in your `map` block -  remember that every element will be modified by the return of the block (or in other words each new element is whatever the last line of the block returns during that iteration).

```ruby
>> output = [1,2,3].map do |num|
>>   if num == 3
>>     num = 'X'
>>   end
>> end
=> [nil, nil, 'X']
```

Again, weirdness. In the example above the the method is supposed to change `num` values equal to 3 to `X` and leave the rest unchanged. However we're getting `nil` values for all values not equal to 3. Let's check why this is happening by testing out a snippet in the terminal.

```ruby
>>  num = 2
>>  if num == 3
>>    num = 'X'
>>  end
=> nil
```

The return value is `nil`, and therefore each element that does not equal 3 will have a return value of `nil`. A way to address this is create a logic flow in your block where each element is positively evaluated.

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
>>   num 
>> end
# => 3
```
In the example above `1` is the first value for `num` and the first to be yielded to the block. The accumulator object `acc` is then set to `1`.




In the next yield the accumulator will be set to `2` and so on. Due to its method definition, `reduce` will not add each element returning a truthy value to an array unlike `select` unless specified in the block.

```ruby
>> result = [1,2,3].reduce([]) do |acc, num| 
>>   acc << num 
>> end
# => [1,2,3]
```

I'm sure I'll continue to haphazardly wield shiny things in the future. At least next time I may know more about what I don't know, so to speak. :)