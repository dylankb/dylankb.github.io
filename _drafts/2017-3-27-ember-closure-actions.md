---
layout: post
title: "Creating a modal to learn basic ember, closure actions, and more: Pt I"
comments: true
---

In the course of building a simple Ember app with a modal, I'm going to attempt to show how and why you might use a couple different features available in Ember.js. These include:

- Ember closure actions
- One-way data binding
- Using mixins

I've never been able to understand how to do something unless I'm clear about why I need to do it. I'm no Ember expert, but I've recently picked up a few things that might be valuable to beginners. I also find that writing helps crystalize my learnings as well.

The end goal is to build a modal to update attributes on a given model. In our case, we'll be working with a users model. Here are the sections this blog will include:

- Toggling display
- Loading and editing data

## Toggling display

First, we'll start off by creating the ability to show an HTML form when we click a button. The steps to achieve this will be:

1. Create a new route and template
2. Create a property to toggle the display of the form

The fastest way to get started with step 1) is to run `ember g route users`

Our new `users.hbs` template should include a form with something like this.

```html
<form method="patch">
  <div>
    <label>Full Name</label>
    {{input value=fullName}}
  </div>
  <div>
    <label>Nickname</label>
    {{input value=nickname}}
  </div>
</form>
```

Read more about the `input` helper here in the [guides](https://guides.emberjs.com/v2.12.0/templates/input-helpers/) if you're interested

Additionally, we'll need a clickable HTML element linked to an action to reveal the form.

```html
<button {{action "toggleModal"}}>
	Edit User Info
</button>
```

The `ember g` command we ran above also updated the `router.js` file with a new line `this.route('user');`, which will allow us to visit `/users` path.

2) Create a property to toggle the display of the form

Since we have a `routes/users.js`, it might seem like we should just add a property there to track the current state of the form display. This will not work.

To quote someone from the learning team [here](https://discuss.emberjs.com/t/accessing-routes-properties/11709/5)

>In the Ember architecture, Routes aren't actually connected to the template, Controllers are.

>The job of a route is to set up the right template and controller, and then set the controller's model property with the return value of the `model()` hook.

Magic revealed. Let's create a `users` controller with a boolean property that we'll toggle with our `toggleMethod` action - the action we're hoping to fire in the `users.hbs` template.

```js
isShowingForm: false,

actions: {
  toggleModal() {
    this.toggleProperty('isShowingForm');
  }
}
```

Now that we have a way of saving the state of a displayed modal, we can wrap our form in an `if` [helper](https://guides.emberjs.com/v2.12.0/templates/conditionals/). This allows the template form to display depending on the current state of `isShowingForm`.

We should now have a form that we are able to toggle by clicking a button. Here's a [Twiddle](https://ember-twiddle.com/279e0b19b3322683546327657ef3d0e1?openFiles=templates.users.hbs%2C&route=%2Fusers) that demonstrates this simple functionality. As you can see this isn't really a modal, but we're getting there.

But wait, if we aren't using the user route handler to old our `isShowingForm` property, do we need it? The answer is no. Ember is smart enough to render the correct template if the template is the same as the route defined in the `router.js`. Therefore feel free to delete the file and confirm that everything still works correctly.

## Mocking and editing data



Note, we still have not created a route handler file.
