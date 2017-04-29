---
github_url: https://github.com/reinteractive-open/installfest_guides/tree/master/source/guides/installfest50/getting_started.md
---

# Getting Started
## Preparation

#### Prerequisites

1. A working version of Rails 5. To determine if you've got a working version of Rails 5, type `rails -v` into your command prompt, or ask a mentor.
2. [Sublime Text](https://www.sublimetext.com). If you prefer another text editor like [Vim](http://www.vim.org/download.php), [emacs](https://www.gnu.org/software/emacs/), [TextMate](https://macromates.com/) or Github's [Atom](https://atom.io/) that's fine too but these instructions will specifically mention Sublime.

#### Next steps

Open a command prompt.

To do this on Windows: Open the Command Prompt window by clicking the Start button, clicking All Programs, clicking Accessories, and then clicking Command Prompt.

To do this on Mac: Open Finder in the Dock. Select applications. Then choose utilities. Double click on Terminal.

## Setting up our Rails app

Enter the following command into your command prompt:

`rails new quick_blog -T`

This command tells Rails to generate a new application and begin to install dependencies for your application. This process may take a few minutes, so you should let it continue.

The `-T` is short for `--skip-test-unit`.  We won't be specifically covering testing just now, so we won't need the `test` directory that Rails normally provides for you when generating a new project.

Once it has finished type:

`cd quick_blog`

Entering this command will change you into the folder where your application is stored. If you look at the contents of this folder you'll see:

![The default Rails application structure](/images/guides/default_rails_structure.png)

This is the standard structure of a new Rails application. Once you learn this structure it makes working with Rails easier since everything is in a standard place.

Next we'll run this fresh application to check that our Rails install is working properly. Type:

`rails server`

Open your web-browser and head to: [http://localhost:3000](http://localhost:3000) you should see something that looks like:

![Rails default homepage](/images/guides/onrails5.png)

Now that you've created the Rails application you should open this folder using Sublime.

Open up Sublime (or your chosen text editor).

From there, select `file -> open folder...` and navigate to the quick_blog folder that was just generated.

Note: If you open the entire folder - rather than just a file - you will find it much easier to navigate your project.

## Creating basic functionality

Now we're ready to get started building an actual blog.

In your command prompt press `Ctrl-c` (hold down the `Control` key, and press `c`) to stop the Rails server, or use your second command prompt and navigate to your Rails application folder.

Then you can use a Rails generator to build some code for you:

`rails generate scaffold Post title:string body:text`

__Let's break this command down:__

We're asking `rails` to `generate` a `scaffold` (basic building blocks; think construction scaffolding) for a "thing" that we want to call a `Post` in our system. In Rails terminology this "thing" (Post) is called a "resource".

We want to give our `Post` two attributes:

* a `title`, which we want to be a `string`, and
* a `body`, which we want to be `text`.

A `string` is computer-speak for a short sequence of characters like `"hello"` or `"Are you having fun, yet?"`, and can usually be as long as your average tweet. Blog titles tend to be short, so we'll use a `string` for ours.

`text` is like a `string`, but longer, so we'll use it to have enough room to write as many paragraphs as we want in the `body` of our blog post.

After running your command, you'll be presented with something that looks like:

![Scaffolding posts](/images/guides/scaffolding_posts.png)

An important file that was generated was the migration file:
`db/migrate/20140528075017_create_posts.rb`

Note that, as this filename starts with a unique id including the date and time, yours will have a different set of numbers.

You can view this file by going to Sublime, navigating through the folders on the left side column, and clicking on `20140528075017_create_posts.rb`.

```ruby
class CreatePosts < ActiveRecord::Migration
  def change
    create_table :posts do |t|
      t.string :title
      t.text :body

      t.timestamps
    end
  end
end
```

This file is Ruby code that Rails uses to manage how your data is stored in the database.

You can see that this code is to create a table called `Posts` and to create two columns in this table, a title column and a body column.

Whilst we have generated this code to create our Posts table, the script has not yet been executed and therefore the table does not yet exist in our database.

We need to instruct Rails to apply this to our database. Returning to the command-line, type:

`rails db:migrate`

Once this command has run you can start up your Rails server again with `rails server` and then navigate to
[http://localhost:3000/posts](http://localhost:3000/posts) in your web browser to see the changes you've made to your application.

![Empty posts list](/images/guides/empty_posts_list.png)

From here you can play around with your application.

Go ahead and create a new blog post.

![Creating a new post](/images/guides/filling_out_posts_form.png)

You'll notice you can create new posts, edit or delete them.

We're going to add some functionality to our new Rails app to enforce a rule that every post must have a title.

In Sublime, open `app/models/post.rb` and add the following line to your code:

```ruby
validates_presence_of :body, :title
```

(Don't forget to save your file.)

Your `post.rb` file should look like:

```ruby
class Post < ApplicationRecord
  validates_presence_of :body, :title
end
```

We can check that this works by returning to our browser, editing our blog post, deleting the title and clicking `Update Post`.

You'll get an error informing you that you've attempted to break the rule you just created:

![Rails validation error](/images/guides/validation_errors.png)

## Changing the default look

Right now our [show post page](http://localhost:3000/posts/1) isn't looking very good.

In Sublime, open `app/views/posts/show.html.erb` and make it look like the following:

```erb
<p id="notice"><%= notice %></p>

<h2><%= link_to_unless_current @post.title, @post %></h2>
<%= simple_format @post.body %>

<%= link_to 'Edit', edit_post_path(@post) %> |
<%= link_to 'Back', posts_path %>
```

(Don't forget to save your file.)

At this point you can refresh the Show Post page in your browser to see the changes you've made.

We want to make our blog listing prettier too, and we'll use a Rails partial to achieve this. A partial is simply a reusuable block of HTML code which can be embedded into a web page.

We want our listing and the individual blog pages to look the same so first we'll create a new file using Sublime.

This file will be a partial file that will live in `app/views/posts/` and we will name `_post.html.erb`. (The underscore in front of the filename here tells Rails that this is a partial.)

We'll take:

```erb
<h2><%= link_to_unless_current @post.title, @post %></h2>
<%= simple_format @post.body %>
```

out of `app/views/posts/show.html.erb` 

and put it in our `_post.html.erb` file.

After that, change all three mentions of `@post` to be `post` instead.

This means your `_post.html.erb` file will be:

```erb
<h2><%= link_to_unless_current post.title, post %></h2>
<%= simple_format post.body %>
```

In our `show.html.erb` file we want to add in the partial that we just created.

Insert the code: `<%= render partial: @post %>` to make it look like:

```erb
<p id="notice"><%= notice %></p>

<%= render partial: @post %>

<%= link_to 'Edit', edit_post_path(@post) %> |
<%= link_to 'Back', posts_path %>
```

Save all these files and refresh the [show posts page](http://localhost:3000/posts/1).

This is to check that you haven't broken anything with those changes.

At this point, our index page still hasn't changed. So we're going to remove the table in there and replace it with the partial so we're re-using that code.

In Sublime, open the `index.html.erb` file up and make it look like:

```erb
<h1>Listing posts</h1>

<%= render partial: @posts %>

<%= link_to 'New Post', new_post_path %>
```

(Don't forget to save your file.)

## Access control

One huge problem with our blog is that absolutely anyone can create, edit and delete blog posts. This is not good so let's fix that!

We'll use HTTP Basic authenticate to put a password on actions we don't want just anyone accessing.

Open `app/controllers/posts_controller.rb` and add `before_action :authenticate, except: [ :index, :show ]` on line two, just below the class declaration.

At the bottom of your file, just before the final `end`, put the following code:

```ruby
  def authenticate
    authenticate_or_request_with_http_basic do |name, password|
      name == "admin" && password == "secret"
    end
  end
```

(Don't forget to save your file.)

Overall your `posts_controller.rb` should have the following code at the top and the bottom of the file. Note that all the methods are excluded here for brevity.

```ruby
class PostsController < ApplicationController
  before_action :authenticate, except: [ :index, :show ]
  before_action :set_post, only: [:show, :edit, :update, :destroy]

  ...
  # all your actions go in here
  ...

  private

  ...
  # there should be post_params and set_post methods in here.
  ...

  def authenticate
    authenticate_or_request_with_http_basic do |name, password|
      name == "admin" && password == "secret"
    end
  end
end
```

With that code in place you can try to [add a new post](http://localhost:3000/posts/new) and you'll be prompted to enter a username and password.

![image](/images/guides/authentication_required.png)

## Adding comments
#### Creating a database model and routing

No blog is complete without comments. Let's add them in.

In our command prompt, shut down your rails server by hitting `Ctrl-C` and then type in:

`rails generate resource Comment post:references body:text`

Don't forget to update your database here to reflect the schema change you've just made:

`rails db:migrate`

After this you'll need to inform Rails that your Posts will potentially have many Comments.

In Sublime, open `app/models/post.rb` and add the line: `has_many :comments` somewhere inside the class.

This should look like:

```ruby
class Post < ApplicationRecord
  has_many :comments

  validates_presence_of :body, :title
end
```

(Don't forget to save your file.)

The back-end for your comments is almost complete, we only need to configure the url that is used to create your comments.

Since comments belong to a post, we'll make the URL reflect this.

Right now you can see all the configured URLs by typing `rails routes` in your command prompt.

If you do this now you'll get something like the following:

```
      Prefix Verb   URI Pattern                  Controller#Action
    comments GET    /comments(.:format)          comments#index
             POST   /comments(.:format)          comments#create
 new_comment GET    /comments/new(.:format)      comments#new
edit_comment GET    /comments/:id/edit(.:format) comments#edit
     comment GET    /comments/:id(.:format)      comments#show
             PATCH  /comments/:id(.:format)      comments#update
             PUT    /comments/:id(.:format)      comments#update
             DELETE /comments/:id(.:format)      comments#destroy
       posts GET    /posts(.:format)             posts#index
             POST   /posts(.:format)             posts#create
    new_post GET    /posts/new(.:format)         posts#new
   edit_post GET    /posts/:id/edit(.:format)    posts#edit
        post GET    /posts/:id(.:format)         posts#show
             PATCH  /posts/:id(.:format)         posts#update
             PUT    /posts/:id(.:format)         posts#update
             DELETE /posts/:id(.:format)         posts#destroy
```

In Rails, your URLs (or routes) are configured in the file `config/routes.rb`.

Open this file in Sublime and remove the line `resources :comments`.

Re-run `rails routes` and you'll notice that all the URLs for comments have disappeared.

Update your `routes.rb`file to look like the following:

```ruby
Rails.application.routes.draw do
  resources :posts do
    resources :comments, only: [:create]
  end
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
end
```

(Don't forget to save your file.)

Because comments will be visible from the show Post page along with the form for creating them, we don't need to have URLs for displaying comment listings, or individual comments.

When you rerun `rails routes` you'll now see the following line:

```
post_comments POST   /posts/:post_id/comments(.:format) comments#create
```

Before we're completely finished with the backend for our commenting system, we need to write the action that will create our comments. (For more information on actions please read the Rails Guide on
[ActionController](http://guides.rubyonrails.org/action_controller_overview.html))

In Sublime, open `app/controllers/comments_controller.rb` and make your code look like the following:

```ruby
class CommentsController < ApplicationController
  def create
    @post = Post.find(params[:post_id])
    @comment = @post.comments.create!(comment_params)
    redirect_to @post
  end

  private

  def comment_params
    params.require(:comment).permit(:body)
  end
end
```

(Don't forget to save your file.)

#### Putting comments into your HTML view

So far you have:

* created the database model for your comments, 
* migrated your database, 
* informed Rails of the relationship between comments and posts, 
* configured a URL that lets you create your comments, and 
* created the controller action that will create the comments.

Now you need to display any comments that have been submitted for a post, and allow users to submit comments.

In Sublime, open `app/views/posts/show.html.erb` and make it look like:

```erb
<p id="notice"><%= notice %></p>

<%= render partial: @post %>

<%= link_to 'Edit', edit_post_path(@post) %> |
<%= link_to 'Back', posts_path %>

<h2>Comments</h2>
<div id="comments">
  <%= render partial: @post.comments %>
</div>
```

You'll now need to create a file called `app/views/comments/_comment.html.erb` with the following contents:

```erb
<%= div_for comment do %>
  <p>
    <strong>
      Posted <%= time_ago_in_words(comment.created_at) %> ago
    </strong>
    <br/>
    <%= comment.body %>
  </p>
<% end %>
```

(Don't forget to save these files.)

Back in `app/views/posts/show.html.erb` you need to add in the form for submitting a comment so add the following code to the bottom of that file.

```erb
<%= form_for [@post, Comment.new] do |f| %>
  <p>
    <%= f.label :body, "New comment" %><br/>
    <%= f.text_area :body %>
  </p>
  <p><%= f.submit "Add comment" %></p>
<% end %>
```

To access the `div_for` helper method used above, we need to add the `record_tag_helper` gem to our Gemfile.

Open `Gemfile` and add the following line anywhere in the file: `gem 'record_tag_helper', '~> 1.0'`

Whenever you make changes to your Gemfile, you need to run a `bundle install` so go back to your terminal and stop the server.

Then, making sure you have saved your changes, run `bundle install`.

Restart your server.

Comments are now working, so go ahead and browse to [your post](http://localhost:3000/posts/1) and add a new comment.

## Publishing your Blog on the internet

Heroku is a fantastically simple service that can be used to host Ruby on Rails applications. You'll be able to host your blog on Heroku on their free-tier, but first you'll need a Heroku account.

Head to [https://www.heroku.com/](https://www.heroku.com/), click 'Sign Up' and create an account.

The starter documentation for Heroku is available at: [https://devcenter.heroku.com/articles/quickstart](https://devcenter.heroku.com/articles/quickstart).

Once you've got an account you'll need to download the toolbelt from [https://toolbelt.heroku.com/](https://toolbelt.heroku.com/) and set it up on your computer.

### Make the application work on Heroku

Up until this point we've been using SQLite as our database, but unfortunately Heroku doesn't support the use of SQLite. So we're going to be running Postgres instead.

We need to do some other things to make our application work on Heroku. These changes are easy to make.

Open the `Gemfile` in Sublime and add `gem 'pg', group: :production` on line 6, immediately after the `sqlite3` gem.

It should look like:

```ruby
source 'https://rubygems.org'

gem 'rails', '~> 5.0.0', '>= 5.0.0.1'
# Use sqlite3 as the database for Active Record in development and test, and postgres in production
gem 'sqlite3', group: [:development, :test]
gem 'pg', group: :production
# Use Puma as the app server
gem 'puma', '~> 3.0'
# Use SCSS for stylesheets
gem 'sass-rails', '~> 5.0'
# Use Uglifier as compressor for JavaScript assets
gem 'uglifier', '>= 1.3.0'
# Use CoffeeScript for .coffee assets and views
gem 'coffee-rails', '~> 4.2'
# See https://github.com/rails/execjs#readme for more supported runtimes
# gem 'therubyracer', platforms: :ruby
gem 'record_tag_helper', '~> 1.0'

# Use jquery as the JavaScript library
gem 'jquery-rails'
# Turbolinks makes navigating your web application faster. Read more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
gem 'jbuilder', '~> 2.5'

group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platform: :mri
end

group :development do
  # Access an IRB console on exception pages or by using <%= console %> anywhere in the code.
  gem 'web-console'
  gem 'listen', '~> 3.0.5'
  # Spring speeds up development by keeping your application running in the background. Read more: https://github.com/rails/spring
  gem 'spring'
  gem 'spring-watcher-listen', '~> 2.0.0'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

(Don't forget to save your file.)

After this, run the command `bundle install --without=production` on your command line.

### Regarding version control

Heroku also requires that every application is placed under version control before it is deployed.

Simply run the following commands on the command prompt to make sure your application is properly controlled:

```ruby
git init
git add .
git commit -m "initial blog commit"
```

### Deploying your application

In the same command prompt you should be ready to deploy your application.

First we create our Heroku application:

`heroku create`

Now we push our application to Heroku:

`git push heroku master`

Finally we set up our database:

`heroku run:detached rails db:migrate`

The `detached` option runs the command in the background. It is there only to ensure the process will go through, even on faulty Internet connection. You can use `heroku logs` to view the output of the command.

To check that your blog has been deployed properly, browse to the URL that Heroku has gien you, remembering to append "/posts" to the end of the URL.
e.g. `https://peaceful-hamlet-7389.herokuapp.com/posts`

Note that you can also use the `heroku open` command to get to the root URL (and then append the "/posts" to that URL).

Welcome to Ruby on Rails! If you're this far along you might want to pause and catch your breath. Check out [WTF Just Happened? A Quick Tour of your first Rails App](https://reinteractive.com/posts/316-wtf-just-happened-a-quick-tour-of-your-first-rails-app) to recap.

After that, it's time to [head on over to Part 2](/guides/installfest50/finishing_a_basic_blog) which goes
more in depth with Rails and begins to add more features to the blogging engine.
