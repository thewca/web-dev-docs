---
layout: default
parent: Rails Resources
title: Rails Cheatsheet


## Commands
Generate a model: `bin/rails generate model {model_name} {property_name:property_type}`
  - {model_name} is singular, and capitalised (eg, "User")
  - Property types: `string`, `text`
  - After property names, you can use {other_model_name:references}, eg `user:references`
  - This creates the following:
    - db/migrate/{migration}
    - app/models/{model_name}.rb 
    - test/models/{model_name}\_test.rb  = Testing harness for the comment model
    - test/fixtures/{model_name}.yml  = Samples of the model for use in testing
  - if the model you created references another model, update the model file of the associated model with a `has_many` clause

Generate a controller: `bin/rails generate controller Comments`
  - This creates the following:
    - app/controllers/{controller_name}\_controller.rb = controller file
    - app/views/comments = stores views for the controller
      - Note that a controller is a file, but its views are in a folder. One controller can have many views (eg, to execute different CRUD operations on a model)
    - test/controllers/{controller_name}\_controller_test.rb = tests for controller
    - apps/helpers/{controller_name}\_helper.rb = helper file for the view (not really sure what this does yet)


## Route definitions

Routes determines which _controller_ to pass an HTTP call to.

Example definition:
	`get "/articles", to: "articles#index"`
	{HTTP-call} "{url-suffic}", to: "{controller}#{function}"

	`root "#articles#index"` - sets homepage 
	root "{controller}#{function}"


## Static Pages
Static pages are handled using "implicit rendering" - this means, only a route and view need to be defined to create a static page.
- Route definition (for an "About" page):  `get 'about' => 'static_pages#about'`
- View defined in `/views/static_pages/about.html.erb`

