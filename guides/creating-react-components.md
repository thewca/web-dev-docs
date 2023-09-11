---
title: Creating React Components
parent: Guides
layout: default
---

This documentation is very new, so you should feel free to suggest changes.

## Preamble

The website is historically purely Rails, with a lot of jquery to handle user interaction.
With the integration of Webpack to Rails, we started migrated our javascript and css codebase to Webpack,
but it's still very much in progress.
This means we serve our assets from two sources: sprockets (assets located in '/assets'), and webpack (assets located in '/packs').

New javascript developments should occur with webpack.

Packs can import javascript, stylesheets, or even images (Webpack will figure
out the dependencies and what to include when).

## Creating a new component

Let's assume you want to write a new component to display the website posts,
and you want to name it `PostsWidget`.

All of our components live under the aptly named folder `app/webpacker/components`.
You are essentially free to write your code there as you please, but please consider the following as rule of thumb: If it's a simple component that resides within one file, just create it as `PostsWidget.js` and have fun. If it's a complex setup with multiple interacting files, create a folder `components/PostsWidget` and create an `index.js` and other `js` files in there.

The `index.js[x]` file should contain the main component you want to export,
and declare itself in a manner that would work for normal pure-JS projects (i.e. `export`ing the component you want to use in Rails):
```javascript
import React from 'react';
import { registerComponent } from '../wca/react-utils';
function PostsWidget({
  msg,
}) {
  return (
    <div>My message: {msg}</div>
  );
}

export default PostsWidget;
```

## Integrating the component to the website

Now that we have a very basic component exported, we most likely want to include
it in some page of the website.
Once you've found a view where to include your component, you will have to:
  - tell the website you'll need the component
  - render the component somewhere

There is an application helper for that:
  - `react_component` will let you emit a registered component.

In our case, assuming we want to use the widget on the posts index page,
the `app/views/posts/index.html.erb` file would look like this:
```erb
<div class="container">
  <%= react_component("PostsWidget", {
    msg: "Hello world!",
  }, {
    id: "posts_widget",
  }) %>
</div>
```

The first mandatory argument to `render_react_component` is the name of the component,
the same name used in the JS `export` (actually, there is a JavaScript `require` context running under the hood, so it's literally doing JS `require` with whatever name you pass it).
The second argument is a keyword dict directly containing the properties you want to pass to the component.
The third, optional argument is a keyword dict with other HTML options. For example, `id` may be used to set a specific
id on the `div` holding the component.

In our example we use it to set the `msg` property.


## Writing UI

Writing a message may be fun, but it doesn't go very far.
We recently switched to [Fomantic UI (FUI)](https://fomantic-ui.com/) (which you may also know as Semantic UI)
as our main framework, it has React bindings through the `semantic-ui-react` package.
Documentation for all usable components is [here](https://react.semantic-ui.com/).

Basically if you want to include some components from FUI you would do something like that:
```
import {
  Button, Card, Icon, List, Pagination,
} from 'semantic-ui-react';
```

We don't import the whole FUI CSS by default.
If you're using some components that were not already used by the website, make sure to edit our [global_style.js](https://github.com/thewca/worldcubeassociation.org/blob/0d0137379b5ce1a62e05f0a4dc390fef7aa8292e/WcaOnRails/app/webpacker/packs/global_styles.js) to include the CSS-es relevant to these components.
If what you're using is extremely specific to the widget you're writing, you may consider only including the style in your widget's javascript file.

If you want to write custom (S)CSS that would be active only when that component is used, you can create a custom (s)css file in the same folder of your component and import it. Webpack will automatically include the style when loading the pack containing your component (again, see the real `posts_widget` example :)).

I won't go much more in details as the rest is just putting together React
components, which is not the scope of that documentation.


## Loading data from the website

Chances are that your new component will want to load some data from the website.
In the case of our `PostsWidget`, we would like to get the list of posts, to display them.
While we're using React to create the *View* for them, the *Model* and *Controller*
part are handled by Rails.

Therefore we'll need:
  - to make rails give us the list of posts
  - somehow load them asynchronously in our component.

### Making the server send json data

The first part is achieved by making the `index` method of the `posts_controller`
reply to us with a json containing the posts data.
Here is roughly what it could look like:
```ruby
def index
  respond_to do |format|
    format.json do
      render json: {
        posts: Post.visible.order(created_at: :desc),
      }
    end
    format.html do
      render :index
    end
  end
end
```

It basically tells rails to:
  - if the client request json, render the visible posts as json, sorted with the most recent first
  - if the client request html (as it would by visiting the index page), we just
render the html page without loading any data (because it will render the component
which itself will request the data as json).

You can see the actual implementation of that endpoint (with extra options as which
page to load, how many posts to load, ...) in `app/controller/posts_controller.rb`.

### Using these data in our component

Now that we have an endpoint to fetch data from, we need to use it from our component.
We have a hook that can be used to fetch data from a particular
endpoint.
The `useLoadedData` hook is available in the `requests/loadable` module, and takes one parameter: an url to fetch from.

To make it clear, let's take this example:
```javascript
const PostsWidget = ({
  initialData,
}) => {
  const { loadedData, loading, error } = useLoadedData(postsUrl());
  return (
    <>
      {error && (
        <Errored componentName="PostsWidget" />
      )}
      {loading && (
        <Loading />
      )}
      {!loading && loadedData && (
        <PostsList posts={loadedData.posts} />
      )}
    </>
  );
};
```

It would be then used like this: `<PostsWidget initialData={{}} />`.

In the code above, a lot of thing happen under the hood:
  - `postsUrl()` returns the route to the posts url (the route can be parametrize with additional data - such as a page number, a tag, or anything else).
  - `loadedData` will be filled by `useLoadedData`, and will be `null`
until the data are loaded. When they are, it will contain the server response.
In this case, it will contain a `posts` field with an array of posts.
  - `error` will also be provided by `useLoadedData`, and will be set only
if something wrong occurred when fetching the data.
  - `PostsList` is an hypothetical component rendering a list of posts.

As you can see in the example, I used the generic `Loading` and `Errored` component
to handle the cases where the data are loading or could not be fetched.

Note that you may choose to provide the component with some initial data.
For instance: `<PostsWidget id={} initialData={{posts: [/* some data */]}}/>`

Again you can see a detailed usage in the actual code of `PostsWidget` :)


## Writing data to the website

TODO: a similar `savable` hook is under development.

## I18n

Use the `I18n.t` function in place of `t` in Ruby. 
This needs to be imported from `i18n`, which is often `import I18n from '../../lib/i18n'`, although you may need to play with the path.

If the translation contains HTML, you can use the `I18nHTMLTranslate` component, which sanatizes first.

### Missing Translation
If you see `[missing "*" translation]`, you may need to add the specific yml path you want to use to `WcaOnRails/config/i18n.yml`.

