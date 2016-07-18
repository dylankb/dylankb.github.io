---
layout: post
title: "CSS stuff: All on the &lt;table&gt;"
comments: true
---

### Styling tables

Tables are an incredibly useful tool for displaying data. Your users will want their data displayed in a readable manner, and doing so requires a different approach from styling something like a `div`. Below I've gone over some basic fundamentals for how to control borders and create spacing in tables using HTML and CSS.

#### Border spacing

One of the first decisions to make when styling a table is to think about how you want your borders to stack. The `border-collapse` property controls this stacking behavior.

The values that `border-collapse` takes are:

1. `collapse` - collapse border widths to not stack
2. `separate` (default) - border widths will stack up
3. `inherit` 

Having borders that don't stack means that if you create a border around a table element such as a `td`, another `td`'s border width won't be added to the computed total width of the adjacent border (where the two borders meet). 

However, one interesting effect you can make use of if you keep the default value `border-collapse: separate` is that you can use `border-spacing`.

> `border-spacing` - determines space between borders. Only usable with `border-collapse: separate`

Here's an example of a table making use of `border-spacing`:

```css
table {
  border-collapse: separate;
  border-spacing: 4px;
}
table,
th,
td {
  border: 1px solid #cecfd5;
}
```

![](/assets/table-border-spacing.png)

[Codepen](http://codepen.io/shayhowe/pen/geoAh) and original [source](http://learn.shayhowe.com/html-css/organizing-data-with-tables/)

The `table` selector creates an outer border, while the `th, td` selectors make up the inner cell borders.

#### Styling borders (rows)

When styling a table you often want to create a unbroken border. However barring certain resets, the example below will not create a continuous bottom-border for a row.

```css
table {
  border-collapse: separate;
}
th,
td {
  border-bottom: 1px solid #cecfd5;
  padding: 10px 15px;
}
```

It will probably have spaces in between the `td`s like this:

![Screenshot showing white space between table cells](/assets/table-cell-spaces.png)

To create a continuous bottom border either:

- Change to `border-collapse: collapse` or
- Add `border-spacing: 0px`

Some resets include both (i.e Code Pen reset option), although some advise just using the `border-collapse: collapse` method.

**`tr` borders**

If you use `border-collapse: collapse`, you can now apply borders to `tr` elements.

#### Spacing with table cells

**text-indent**

`th` and `td` are `display: table-cell` elements, and therefore [cannot take a margin](https://developer.mozilla.org/en-US/docs/Web/CSS/margin) (`tr` can't either)

Solutions:

1) Use `text-indent` for pixelated control over text spacing in a `td` or `th`.

2) Creating dummy/spacer `td`, like this

```html
  </tr>
    <td class="spacer"></td>
    <td>
      <dl>
        <dt>Saturated Fat</dt>
        <dd>1g</dd>
      </dl>
    </td>
    <td>5%</td>
  </tr>
```

```css
tbody tr .spacer {
  width: 20px;
  border-top: none;
}
``` 

The second option is useful if you want get rid of a top border

#### Overriding table cell spacing

If you want to place two elements closer than normal table cell boundaries allow, such as this screenshot below...

![Two elements sitting close and inline off to the left in a table row](/assets/table-wrapped-sample.png)

I've found that you're in for a challenge using standard just `td` and `th` elements. Basic table markup like below makes it difficult to move the `td` elements because they are relatively fixed in place.

```html
<tr>
  <td>Total Fat</td>
  <td>8g</td>
  <td>12%</td>
</tr>
```

![Example HTML for a basic table](/assets/table-basic.png)

What are we to do? Using `colspan` with these elements doesn't seem to work. 

```html
<tr>
  <th scope="row" colspan="2">
  <td>Total Fat</td>
  <td>8g</td>
  </th>
  <td>12%</td>
</tr>
```

This code above renders like this:

![Bad table element html](/assets/table-error.png)

Some trickery with `dl` causes the `dl` to be rendered over two `col`s.

```html
<tr>
 <th scope="row" colspan="2">
  <dl>
    <dt>Total Fat</dt>
    <dd>8g</dd>
  </dl>
 </th>
 <td>12%</td>
</tr>
```
![Two elements sitting close and inline off to the left in a table row with inspector open](/assets/table-wrapped-inspector.png)

##### Centering images in a table element

Finally, it's helpful to know that `text-align: center` will center an image in a table cell.

![Centered image in a td](/assets/table-img-center.png)

*Uncited examples are mostly adaptations from the excellent curriculum at [LaunchSchool](https://launchschool.com), an online school for developers.*