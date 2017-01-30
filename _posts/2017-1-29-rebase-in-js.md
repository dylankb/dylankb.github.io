---
layout: post
title: "Reduce in JS: A Form Reduction Example"
comments: true
---

To demonstrate how `reduce` works, we'll go over a common task - gathering and manipulating form inputs.

A lot of projects that use JavaScript will grab data submitted by users via a form with JavaScript triggered by event handlers. If you're using jQuery, you might take advantage of `serializeArray()` to grab all this data at once. `serializeArray` returns an object for each  `input` field's data inside your event callback.

```html
<label> Starting Point
  <input type="number" name="startX" placeholder="x:">
  <input type="number" name="startY" placeholder="y:">
</label>

<label> Ending Point
  <input type="number" name="endX" placeholder="x:">
  <input type="number" name="endY" placeholder="y:">
</label>
```

```js
$(function() {

  // Called from inside form event callback
  function getFormObject($form) {
    var $inputs = $form.serializeArray();
    console.log($inputs);
  }
  ...
}
// [Object, Object, Object, ... ]
```

Each object looks something like this `{ startX: 'foo', value: 'bar' }`. This structure nests the data a bit too deeply for convenient access, so you might combine all these into a single object with unique key/value pairs; i.e `{ startX: 'foo', startY: ... }`.

A common, and arguably still fairly readable way would be to iterate through and 'decorate' a new object.

```js
...
var formObject = {};

$inputs.forEach(function(item) {
  formObject[item.name] = item.value;
});

return formObject;
```

But maybe we want to work with [pure functions](https://en.wikipedia.org/wiki/Pure_function), so this gives us a chance to learn about `reduce`. Here was my first attempt `reduce`:

```js
var formObject = $inputs.reduce(function(list, item) {
  formObject[item.name] = item.value;
  return formObject;
});

console.log(formObject);
```

However, my output looked like this:

```js
{ endX : "3",  endY : "4", name : "startX", startY : "2", value : "1" }
```

I expected that there should be a key `startX`, but instead there is a key `name`. The value of `1` should probably be the pair to the `startX` key. What's going on here?

As with many JS mysteries, the answer is revealed in the [MDN documentation on reduce](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce).

> `arr.reduce(callback, [initialValue])`

> The first time the callback is called, accumulator and currentValue can be one of two values. If initialValue is provided in the call to reduce, then accumulator will be equal to initialValue and currentValue will be equal to the first value in the array. If _no_ initialValue was provided, then *accumulator will be equal to the first value* in the array and *currentValue will be equal to the second*.
```

So I fell for the trap of assuming the accumulator would be one thing (an empty object) when it was actually another. Reduce returns an accumulator, which by default is the object that calls `reduce`. In this case it was the `startX` key-value pair; the first item in the `$inputs` collection. To correct this issue I simply supplied an empty object as the initial value.

```js
var formObject = $inputs.reduce(function(list, item) {
  formObject[item.name] = item.value;
  return formObject;
}, {});
```

Which formats our values correctly this time :)

```js
...
console.log(formObject);
// { endX : "3", endY : "4", startX: "1", startY : "2" }
```
