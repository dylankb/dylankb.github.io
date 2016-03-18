---
layout: post
title: "Using Instance Variables in a Sinatra Webapp"
---

I'm trying to get into the habit of taking notes with code examples for concepts that aren't quite clear to me - think "rubber duck note-taking". I wasn't super clear about the uses of instance variables in Sinatra routes, so I went through this exercise here.

If you made a GET route like this:

```ruby
get "/" do
  @test = "This is a test"
  erb :intro
end
```
Then `@test` would be accessible in the `intro` view.

In the below post route:

```ruby
post "/test" do
  if some_condition
    @redirect_test = "Instance variable before a redirect"
    redirect "/"
  else
    @erb_test = "This is a test before calling an erb view"
    erb :intro
  end
end
```
`@redirect_test` will not be available on the `intro` view. A redirect will issue a new request, and our instance variable will not persist.

`@erb_test` will be available on the `intro` view. No new request is issued , only a call (`erb` is a method) to the view, so the instance variable will still be available to the view. However on refresh (another request), this value will not persist.

Based on my understanding of the above, it seemed liked you'd rarely set an instance variable inside a post route. However, I was seeing examples of setting instance variables in post routes out in the wild which tripped me up for a while. One of these was from a todo list app:

```ruby
# Edit an existing todo list
post "/list/:id" do
  id = params[:id].to_i
  @list = session[:lists][id]

  list_name = params[:list_name].strip
  error = error_for_list_name(list_name)
  if error
    session[:error] = error
    erb :edit_list, layout: :layout
  else
    @list[:name] = list_name
    session[:success] = "The list name has been edited"
    redirect "/lists/#{id}"
  end
end
```
The role of `@list` here on line 3 is just to get a handle on the on the correct list contained in the session. When `list_name` is assigned to `@list[:name]` this changes the title of the list. However, since this is all happening in the route we could make the instance variables local variables and they could perform the same function. Specifically I mean changing `@list` to `list` on line 4 and again on line 12. It's not too important, but in my mind this makes the example simpler.

If this project went on to require that we display the current list being edited, then we'd need to reconsider the above change we just made. In this case it's important to set an instance variable in the route so the view can access this variable as well, so we'd need to switch `list` back to `@list`, etc.
