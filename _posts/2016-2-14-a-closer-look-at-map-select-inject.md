---
layout: post
title: "A Closer Look at Select, Map, and Inject: Avoiding Common Gotchas"
---

After learning the basic components of the Ruby syntax I started to use more advanced, sometimes Ruby specific methods. You know, the nifty ones.

Sometimes you find and awesome tool and start using it. It doesn't matter if this is a nail gun or a standard library method, I've come to the conclusion that if you don't know how to wield the tool properly it's

1. Harder to fix it when it breaks
2. Harder to know when to use it

It wasn't until someone pointed out that I didn't have a great grasp about how methods like `select`,`map`, and `inject` work that I realized this. After looking into exactly how blocs are implemented in Ruby using yield and doing a little experimentation I feel like the next debugging session won't be such a nightmare.

**`select`**

The ruby-docs definition of select is: “returns an array containing all elements of enum for which the given block returns a true value.” 

```
def select(array)    #Basic implementation

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

Like methods, the last line executed in the block is the block’s return value

```ruby
>> result = [1,2,3].select do |num|
>>   num > 2
>>   puts num  
>> end
=> []
```


**`map`**
 
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

This is very different from how `select` works.

```ruby
>> output = [1,2,3].map do |num| 
>>   num < 3
>> end
=> [true, true, false]
```

Even if you understand that the above code is incorrect, you must be very careful when using `select` like logic in your `map` block -  remember that every element will be modified the return of the block (or in other words the last line to execute during that iteration).

```ruby
>> output = [1,2,3].map do |num|
>>   if num == 3
>>     num = 'X'
>>   end
>> end
=> [nil, nil, ‘X’]
```

In the example above the block checks whether `num` is equal to 3. If a variable is equal to 1 and you enter:

```ruby
>>   if num == 3
>>     num = 'X'
>>   end
```

in your terminal then the return value is `nil`. Therefore each element that does not equal 3 will have a return value of nil. A way to address this is create a logic flow in your block where each element is positively evaluated.


```ruby
>>   if num == 3
>>     num = 'X'
>>   else
>>     num
>>   end
```

Or a shorter version 
`>>   num == 3 ? ‘X’ : num`

**`reduce`/`inject`**

Reduce yields each element in a collection plus an accumulator to the block. 

Basic implementation

```ruby
def reduce(array, ival=0)  #Basic implementation
  counter = 0
  sum = ival

  while counter < array.size
    current_element = array[counter]
    sum = yield(sum, current_element)
    counter += 1 
  end
  sum
end

array = [1, 2, 3, 4, 5]
```

The accumulator is set to the value returned by the block, and then passed on to the next yield. Therefore even if you set the default accumulator to an empty array, reduce will still work very differently than map or select.

```ruby
>> result = [1,2,3].reduce([]) do |acc, num| 
>>   num 
>> end
# => 3
```
In the example above when `1` is yielded to to the block, and is then set to the accumulator since num (1) is the expression evaluated. In the next yield the accumulator will be set to `2` and so on. Due to method definition reduce will not add each element returning a truthy value to an array unlike select unless specified in the block.

```ruby
>> result = [1,2,3].reduce([]) do |acc, num| 
>>   acc << num 
>> end
# => [1,2,3]
```

I'm sure I'll continue to haphazardly wield shiny things in the future. At least next time a may know more about what I don't know, so to speak. :)