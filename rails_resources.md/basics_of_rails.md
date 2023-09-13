---
layout: default
parent: Rails Resources
title: Basics of Rails
---

Ruby on Rails has two components:
- Ruby syntax
- Rails conventions

# Ruby Syntax

* Similarities/differences to Ruby compared to other languages:
	* [Python](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/to-ruby-from-python/)
	* [C/C++](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/to-ruby-from-c-and-cpp/)
	* [Java](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/to-ruby-from-java/)
	* [Perl](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/to-ruby-from-perl/)
	* [PHP](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/to-ruby-from-php/)
 * [Ruby major language features](https://www.ruby-lang.org/en/documentation/ruby-from-other-languages/) - read from "Important Language Features and some Gotchas")


# Rails Conventions

## Cheatsheets
* [Basic rails cheatsheet](https://gist.github.com/mdang/95b4f54cadf12e7e0415)
* [More complex/comprehensive](https://dev.to/ericchapman/my-beloved-ruby-on-rails-cheat-sheet-50pi)

## Quick Notes
- View columns/attributes in the db: `db/scema.rb`, search for the lowercase, plural version of the model name (eg `Article` -> `articles`) and you'll find the table definition

## Rails Magic: How model, view and controller interact
### Passing data to the view (from model)

Let's say we've got the following components in a Rails blog app:
* `controllers/articles_controller.rb` controller file
* `views/articles`  folder containing views
* `model/article.rb` model, which inherits from ActiveRecord
	* ActiveRecord is the ORM - the object that has a bunch of methods which facilitate interaction with the database
		* This is why `article.rb` doesn't have any methods - it inherits all of them from `ActiveRecord`
	* Thus, `models/article.rb` can be thought of as an ActiveRecord with its scope limited only to the Articles database table which was created when we created the Article model.

Now let's say we want to pass the View all of the articles saved in the Articles database table, so that it can display them. 
* _TL;DR: The `Controller` creates an instance variable with the full contents of the `articles` database table. This instance variable is then available to the view. Embedded Ruby in the view will then render everything._
* A browser requests the page which shows all of the articles (a GET request on `/index` in this case)
* `routes.rb` sees that a `GET` on `/index` gets routed to `articles#index` - the `index` function of the `articles` controller
* When the controller gets passed the request by `routes.rb`, it does two things:
	* The function which `routes.rb` called defines which view the controller will ask to be rendered. In this case, the `index` function being called means that `views/articles/index.html.erb` will be rendered.
	* The controller will request data which the view will need. This processi s described below. 
* The Controller is responsible for passing data to the View - so when it gets passed the request by the route, it needs to request any data from the Model which the view will need.
	* For example, to request all articles in the articles database: 
		 ```ruby
		def index
			 @articles = Article.all
		end
		```
	* In this example, the code in `index` will be executed because that is the controller function which `routes.rb` will have called.
	* `@articles` is an instance variable of the controller. **All instance variables of the controller are available to the view** - you can think about them like two methods in the same class, where one defines a variable of the class, and the other one will then have access to it.
		* The *name* of `@articles` doesn't matter - Rails isn't doing any magic based on the name of the instance variable.
		* `Article` is a reference not just to the model object, but the ORM which talks to the `Articles` database table. You can think about it like `ActiveRecord.Article.all` (even though this isn't what's happening at a technical level)
		* `.all` is just an ActiveRecord method which gets an array of all the items in the relevant database table
* The View code in `views/articles/index.html.erb` could then look like the following:
	```ruby
	<li>
	  <% @articles.each do |article| %>
	    <ul>
	      <%= "#{article.title}, by: #{article.author}" %>
	    </ul>
	  <% end %>
	</li>
	```

### Passing data to the controller (from the URL)

When data is added to the URL, a `params` local variable is made available to the controller. This is a hash. Values are accessed using the key, usually defined in routes.

For example:
	`GET "/article/:id", "articles#show"` is a route which passes the `id` parameter to the `show` method of the `articles` controller.
	The code for displaying the article requested in the URL would be: 
```ruby
	  def show
		  @article = Article.find(params[:id])
	  end
```

### Passing data to the database (from the view)

This is achieved with forms, and there are a _bunch_ of steps involved: 
1. User visits data input page (eg, "Create a new blog post" - `/new` route)
2. Controller passes an empty instance of the model to the view
```ruby
	# articles_controller.rb
	def new
		@article = Article.new
	end
```
3. In the view, a `form` is built to present to the user (see more detail in "Form Building")
4. Once the user fills in form data and clicks "submit", the now-filled-in instance variable (`@article`) is sent to a function in the controller whose purpose is to create a new entry to the database by interacting with the model
```ruby
# articles_controller.rb

def create

end

```
5. The form contents are first validated against a set of allowable inputs. To do this, we create a set of allowable parameters, and pass those to the Article model.
```ruby
# articles_controller.rb

def create
	@article = Article.new(article_params)

private # Private method for seucrity reasons
	def article_params
		params.require(:article).permit(:title, :body)
```

	I found this a bit tough to wrap my head around. A few important things to kno:
	* `article_params` returns a hash that MUST contain the :article model, along with the :title and :body paramters.
		* If :article, :title, or :body AREN'T present, an error will be raised.
		* If anything ELSE is present, it won't be included in the returned hash
	* Also, the reason that the `create` method creates a new `@article` instance variable (instead of just using the one from the view) is for security reasons.

1. Then (optionally) their contents are validated against validation rules for the form contents. These validations are defined in the model as follows:
```ruby
	# models/article.rb
	validates :title, presence: true
	validates :body, presence: true, length: {minimum: 10}
```
1. If these validations fail, an error message can be displayed to the user on the form. This is handled automtically by Rails - we just need to add code to display it in the view:
```ruby
# app/views/articles/new.html.erb
<div> 
	<%= form.label :title %><br>
	<%= form.text_field :title %>
	<%= @article.errors.full_message_for(:title).each do |message| %>
</div>
```
3. If those validations pass and the form data is saved successfully, the controller function will redirect the user to a success page
```ruby
# articles_controller.rb
def create
	@aricle = Article.new(article_params)

	if @article.save # Saves the @article data to the databse
		redirect_to @article
	end
```
4. If validations/data saving fails, the controller redirects the user to a failure page (validations are only checked when we call `@article.save`)
```ruby
# articles_controller.rb#create

	if @article.save # Saves the @article data to the databse
		redirect_to @article
	else:
		render :new, status :unprocessable_entity
	end
```

To update an entry, it is basically the same - except instead of calling `@article =  Article.new`, you get the existing article with `@article = Article.find(params[:id])`. And in order to update the article with new values from the user form, you call `@article.update(article_params)`.

##### Form Building
- Initiate a form with the below code. It creates a form object, which we access through the `form` variable (denoted by the pipes).
```ruby
# app/views/articles/new.html.erb

<%= form_with model: @article, do |form| %> 
```
	? I'm unclear whether the `@article` is related to an instance variable received from the controller
* Create fields for each form value. `form.label` creates the "title" for the form field, `form.text_field` creates the input field.
```ruby
# app/views/articles/new.html.erb

<div> 
	<%= form.label :title %><br>
	<%= form.text_field :title %>
</div>
```
	? Why is there a <br> between the label and text field?
* Add a submit button 
```ruby
# app/views/articles/new.html.erb

<div>
	<%= form.submit %>
</div>
```


## Creating associations in models

This is how you set up relationships between data in Rails - for example:
* parent/child: having a `Comment` object which is the child of an `Article` object. 
* many-to-many: eg, one article could have many `tags`, and the same `tag` may appear on multiple articles.

### Creating hierarchial (parent-child/many-to-one relationships)
1. Create the Model you want to have an association for, specifying `<model_name>:references`, where `<model_name` is the name of an existing model you want to create an association with.
```bash
bin/rails generate model Comment commenter:string body:text article:references
```
	* This is a pretty standard `generate` command. The new thing added is the `article:references` at the end. Note that it's a little counterintuitive that you give the model name you're referencing and then specify `references`.
	* This creates the following Model:
```ruby
# models/article.rb
class Comment < ApplicationRecord
	belongs_to :article #Note the singular - only belongs to one article
end
```
2. Run the database migration to create your new Model in the database
3. Edit the Model(s) you created an association to, with an association to your newly-created Model
```ruby
class Article < ApplicationRecord
	has_many :comments # Note the plural - can have many comments
end
```
4. Add a route where you can access the new Model. In this case, the Model only exists relative to its parent model - so it is nested within its parent route
```ruby
#config/routes.rb
resources :articles do
	resources :comments
end
```
5. Generate a controller for the Model, eg:
```bash
bin/rails generate controller Comments
```




## General Notes
### link_to with helpers
A line of code like this: `<%= link_to article.title, article %>` in a view, where `@articles` is an instane variable of the controller, will automtically link to the `show` page for the given `article`, IF `resources :articles` is defined in `routes.rb`. 

How? `resource :<resource>` automatically creates helper methods that help `link_to` find the link to an item in a model. 

### We recommend the following resources for learning Rails: 

- Diagram of overall architecture: https://se-education.org/learningresources/contents/ruby/mvc.jpeg
- This rails guide is phenomenal at showing you the conventions of Rails, and giving you a taste of its power: https://guides.rubyonrails.org/getting_started.html
- (This stackoverflow summary)[https://stackoverflow.com/questions/5205002/summary-of-ruby-on-rails-fundamental-concepts]
- The Odin Project has a great course
- This video on [seeing through "Rails magic"](https://www.youtube.com/watch?v=rssgWqJq-14)



