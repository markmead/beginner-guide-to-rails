# Rails Tutorial

Convention over configuration

Single responsibility for controllers

## Notes for the course/README

- [Name] = Whatever you have created
- I've had to use "Work" instead of "Portfolio" for my `resource generator` (can't use the same name as the app)

## General

`git checkout <filepath/filename>` - will remove changes (discard changes in Github Desktop)

## Creating App

### rails -h

Returns all the options you can use when creating a new Rails app

### .keep

Stops Git from removing the empty directory

## Rails Generators

To override the generator add this to your `application.rb` file (can customise)

```ruby
config.generators do |g|
  g.orm             :active_record
  g.template_engine :erb
  g.test_framework  :test_unit, fixture: false
  g.stylesheets     false
  g.javascripts     false
end
```

[See more generator options](https://guides.rubyonrails.org/generators.html)

To customise the look of the generated `html.erb` you create a `template/erb/scaffold` directory group in the `lib` folder and then create a file of the `action`/`file` you want to overwrite (e.g `index`, `show` etc)

Changes in the `lib` will not update on refresh, you should make the changes before running the `generator`

[Default Rails scaffold generator views to override](https://github.com/rails/rails/tree/master/railties/lib/rails/generators/erb/scaffold/templates)

### Scaffold Generator

`rails g scaffold [Name] [field]:[data-type]`

- Creates an empty [Name] JS file
- Creates an empty [Name] SCSS file
- Creates Scaffold SCSS file with default styles (delete as you’ll see odd behaviour if kept)

### Controller Generator

`rails g controller [Name] [action_name/pages]`

- The name of the controller is plural

### Model Generator

`rails g model [Name] [field]:[data-type]`

- Only focuses on the model, doesn't create views or controllers
- The name of the model is singular
- Creates a `database migration` and a `[name].rb` model file
- Need to run `rake db:migrate` to add it to the `schema.rb`

### Resource Generator

`rails g resource [Name] [field]:[data-type]`

- It's a minimalistic `scaffold` - doesn't have the views or the pre-filled controller
- Really nice balance of generating the files needed without all the bloat (e.g if you didn't want a 'show' page)

### Migration Generator

`rails g migration describe_the_migration_here attribute:data-type:option`

#### The naming is very important:

- Using the word `add` tells Rails we want to add items to a database table
- Last word is the name of the table you want to change (e.g blogs, works etc)

`attribute` - is the attribute you want to add (e.g slug, title etc)

`data-type` - is the type of the data you want to add (e.g string, text etc)

`option` - this is where you add additional information for the database (e.g to make it unique use `:uniq`)

The `attribute`, `data-type` and `option` are all optional, you can make the changes in the migration file

#### uniq

adds an `add_index :blogs, :slug, unique: true` in the migration file, this tells the database that the value is unique and also means it's very fast to look up

## Controllers

- All controllers inherit from the `application_controller`
- Gives the ability to directly communicate between the model, views and routing system
- `index`, `show`, `new`, `edit`, `create`, `update` & `destroy` are reserved words in a controller
- `@[name]` is an 'instance variable' which can be used in the view

If we had a blog we could call the blog data by calling the model/querying the database with `Blog.all` which will return all blogs. This can then be used in the controller actions view by calling the instance variable attached to `Blog.all`

### before_action

- Goes through and calls a method on each controller action/method passed through in the `only: []` array
- If you have duplicated code, add it to a method and call it in a `before_action`

### respond_to

Tells the controller what should happen once the database record change occurs

### .limit(X)

Limit the database query to `X` amount

### [name]\_params

- Tells the controller what database fields to permit/look for when calling an action
- If you didn't have this you would have security floors, as nothing was whitelisted

## Model/Database

When adding an image to the database use the `text` data-type as you want to store the record to the image, not the image itself. Using `text` instead of `string` as the character limit of the string data-type might cause issues.

## CRUD (Create, Read, Update & Destroy)

### New

- Creates a new instance and takes you to the form that will pass data to the `create` method
- It cannot create a new [Name] as it does not have any params passed

### Create

- Takes data in through the params and creates a [Name]
- Will save if the params passed are valid, else it will show an error in the view

### Edit

- Makes the edit form available by taking you to the edit page

### Update

- Takes the changes that were made on the edit page and passes them as params to the [Name]
- If the params are valid it can save, else it will show an error in the view

### Destroy

- Will delete the blog by calling `[Name].destroy`
- Uses a `:delete` method on the `link_to`

#### Delete vs Destroy

##### Delete

- Go in the database and delete, don't care about validation or callbacks
- Doesn't execute callbacks

[https://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-delete](Read more on the docs)

##### Destroy

- Is more careful, will return false and stop if there is an error
- For example if a record was being used elsewhere in the app it would throw an error

[https://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-destroy](Read more on the docs)

## Routes

- `rake routes` shows all Rails routes in application
- The `prefix` + ‘\_path’ is the URL created that you can use to direct users
- Controller actions such as `update` & `create` don’t have routes as their actions are handled behind the scenes
- `get 'pages/home'` looks for the `def home; end` in the controller and the `home.html.erb` in the views, therefore the names have to match
- `rake routes | grep [name]` will return items with only the '[name]' in it
- The URI pattern looks at the params (typically the `id` param)
- Pages without the CRUD functionality don't need the `resources :[name]` in the routes
- Anytime you make a change you need to restart the server

### \_path

`name_path` will return `/name`

- Returns relative path to the URL

### \_url

`name_url` will return `https://www.yourwebsite.com/name`

- Using `_url` allows you to use a `subdomain:`
- If you are sending a link in an email

### Resources

Encapsulates all of the popular routes applications use - `index`, `show`, `new`, `edit`, `create`, `update` & `destroy`

### Custom

`get 'pages/about'` - auto sets it to the `pages controller about action` and gives it the path of 'pages/about'

However, you can set the path to just 'about' by changing it to `get 'about', to: 'pages#about'`

Can also set the the path to anything you want (e.g - `get 'lead/marketing/inquiry' to: 'pages#contact'`) however this will give you a very long name to call on your `link_to`. Therefore, you can use `get 'lead/marketing/inquiry', to: 'pages#contact', as: 'marketing_lead'` to summarise/shorten the `link_to` helper to just `marketing_lead_path`

### Root Path

You can set the root of your website by using `root to: 'pages#home'` in your `routes.rb` file

### Nested

Use `namespace` to group routes that you want nested

```ruby
namespace :admin do
  get 'dashboard/main'
  get 'dashboard/user'
  get 'dashboard/blog'
end
```

This will give an error as it looks for a capital 'Admin'... using `rake routes` shows that it's looking for the controller action `admin/dashboard#main`

What this means is that the `dashboard_controller` needs to be in an 'admin' directory/folder (e.g `app/controllers/admin/dashboard_controller.rb`). You also have to update the `dashboard_controller` class name to be informed with `Admin::`

```ruby
class Admin::DashboardController < ApplicationController
  def main
  end

  def user
  end

  def blog
  end
end
```

You will also need to move the `app/views/dashboard` directory into a `app/views/admin/dashboard` directory

### Globbing

`get 'posts/*missing', to: 'posts#missing'`

Using the `*` catch all routes

Positioning in the `routes.rb` file is important, if you have your `glob` above your `resources :posts` then it will render the `posts#missing` action for paths such as `posts/new`

#### Correct

```ruby
resources :posts
get 'posts/*missing', to: 'posts#missing'
```

#### Incorrect

```ruby
get 'posts/*missing', to: 'posts#missing'
resources :posts
```

### Dynamic

If Rails sees the a path like `get 'query/:something', to 'pages#something'` then it doesn't look at the word after the `:`, it treats it as a dynamic value and says to the controller "you should have the ability to pull in whatever the user types in"

The value passed after the `:` doesn't have to match the controller action name

You can have more than 1 dynamic name in a path (e.g `get 'query/:something/:name/', to 'pages#something'`) but paths with just 1 dynamic name will not work

#### Get Value from View

```ruby
def something
  @query = params[:something]
end
```

## Views

- The name of a view file has to much that of the controller action
- `<%= %>` will render ruby and `<% %>` will not (use the later for variables, code blocks etc)

## Caching

Compiles it all into fast, easy to access file (on the browser side)

```
<% cache do %>
  <% render @blogs %>
<% end %>
```

- On first hit the `cache` is initiated
- Be picky with where you set this as you wouldn't want to wrap the entire site in it as users might see different versions of the HTML
- Does not fix a slow database query as that's something that appears server side, whereas caching handles the browser

## .inspect

Shows the entire object

## String Interpolation

Have to use `"#{ruby stuff}"` double quotes as `''` single quotes wont work

## Config

- `application.rb` is the master/root file of the app
- `secrets.yml` is not checked into Github, therefore API keys can be stored here

### Initializers

- Run before the application starts up
- `inflections.rb` allows you to overwrite Rails auto plural/singular words settings - this is how Rails knows to change something like "Blog" to "Blogs"

## Errors

Look towards the top of the error for the clearer description of what caused the error

### Bundler Error

Always check that you are using the correct Ruby version for the project

## Debugging

Use `byebug` which stops the system and let's you ask questions to the system

`blog = Blog.friendly.find_by(params[:id])` - runs a database query, you can then change the params on `blog` but this will update the database, it is not scoped to testing

- `params` returns all the data available to the action/method

## MVC

The `routes` capture the path from the user, which gets passed to the `controller`, the `controller` can then communicate with the `model` if it needs to and then communicate and pass data to the `view`

## Rails Console

- Run it with `rails c`
- Direct connection to the database
- `[Name].find(5)` - will find the `[Name]` item with the id of 5
- `[Name].find_by('title': 'Hello friend')` - will find the `[Name]` item with the title of 'Hello friend' (only returns 1 item)
- `[Name].where('title': 'Hello again')` - will find all the items with a title of 'Hello again' and push them to an array
- `restart!` - restart the console after changes to the model

`exit` to leave

### Create

- [Name].create!([field]: [value], [field]: [value])
- `!` = bang - throws error if mistake happens (e.g wrong type of data or attribute, don't use on production database)

### Query Database

`[Name].all` returns all [Name]'s that have been created/are on the database

## Git/Github

Quickly merge branches with `git checkout master` and then `git merge [branch_name]`

## Lib

External source of customisation for your app

## ActiveRecord

### ::Relations

This means it's an array of items, therefore you can use the `.each` method to grab each item

## Asset Pipeline

`*= require_tree *` - get the entire set of files in the folder (delete if you don't want it doing that)

## Heredoc

`nav_links = <<SOMETHING`

End with `SOMETHING`

`SOMETHING` - whatever text you want to pass

## <<

Use `<<` to append into something:

```ruby
greeting = “Hello”
greeting << “ Friend”
greeting # will equal “Hello Friend”
```

## Flash Message

`flash[:alert]`

Flash messages are stored in the session until they are shown

### Options

`alert`, `notice`, `error`

The values for these are set in the controller

---

# Data Flow

## Seeds File

### Creating Data

```ruby
10.times do |blog|
  Blog.create!(
    title: "Blog #{blog}",
    body: "Lorem ipsum..."
  )
end

5.times do |skill|
  Skill.create!(
    title: "Rails #{skill}",
    skill_rating: 50
  )
end

6.times do |work|
  Work.create!(
    title: "Work #{work}",
    subtitle: "Checkout my work on this project",
    body: "Lorem ipsum...",
    main_image: "https://via.placeholder.com/1920x1080",
    thumb_image:  "https://via.placeholder.com/400x400"
  )
end
```

#### This will create

- 10 blog posts titled "Blog 0" through to "Blog 9"
- 5 skills titled "Rails 0" through to "Rails 9"
- 6 work items titled "Work #{0}" through to "Work 5"

You can check this in the view or the `rails console`

### Running

Run with `rake db:seed`

Can run with `rake db:setup` which destroys all previous data, creates a new database, runs migrations and calls the seeds file, only do this when working in development (really useful when updating tables as you can quickly edit the seeds file rather than updating each record on the database individually)

Use [placeholder.com](https://via.placeholder.com/1920x1080) for images

## Controller form Scratch

### Index Action

#### Minimum

This has no data, just looks for an `index` view template

```ruby
def index; end
```

#### Data

This takes data from a model and attaches it to an `instance variable` which can then be used in the view

```ruby
def index
  @work_items = Work.all
end
```

### New & Create Action

#### New

```ruby
def new
  @work_item = Work.new
end
```

#### Create

This creates a new instance of `@work_item` unique to the `create` action where it permits the database params `title`, `subtitle` and `body`

If it can save it then `redirect_to` the `index` page

```ruby
def create
  @work_item = Work.new(params.require(:work).permit(:title, :subtitle, :body))

  respond_to do |format|
    if @work_item.save
      format.html { redirect_to works_path, notice: 'Work item was successfully created.' }
    else
      format.html { render :new }
    end
  end
end
```

### Edit & Update Action

#### Edit

Requires an `id` in the URI pattern (URL) as it's updating a database record, not creating

```ruby
def edit
  @work_item = Work.find(params[:id])
end
```

#### Update

Will populate the form with the content from the database record if the form item (`:title`, `:subtitle` etc) matches what's on the database

```ruby
def update
  @work_item = Work.find(params[:id])

  respond_to do |format|
    if @work_item.update(params.require(:work).permit(:title, :subtitle, :body))
      format.html { redirect_to works_path, notice: 'Work item was successfully updated.' }
    else
      format.html { render :edit }
    end
  end
end
```

### Destroy Action

Doesn't need a view as it deletes the record and redirects/renders something else

```ruby
def destroy
  @work_item = Work.find(params[:id])

  @work_item.destroy

  respond_to do |format|
    format.html { redirect_to works_url, notice: 'Work item was successfully destroyed.' }
  end
end
```

#### View Link

`<%= link_to 'Destroy', work_path(work), method: :delete, data: { confirm: 'Are you sure?' } %>`

`work_path(work)` - this won't render the show page as we pass in the `method: :delete` which uses the `DELETE` request, by default it passes the `GET` request

`method: :delete` - tells the system we want to delete this record

`data: { confirm: 'Are you sure?' }` - is JavaScript that popups up to confirm the action

## Custom Routing

- `root to: 'pages#home'` - sets the root path of the app to the `pages controller home action`
- `get 'about', to: 'pages#about'` - sets the URL for the 'about' page to '/about' rather than '/pages/about'
- `get 'about-us', to: 'pages#about'` - this will still work even though there isn't a `about-us action` in the `pages - controller`

### Resources Override

- `resources :works, except: [:show]` - you can tell `resources` to exclude certain routes (e.g the 'show' page)

```ruby
get 'work/:id', to 'works#show', as: 'work_show'
```

This will get the path of `work/:id` and know to map it to the `works controller show action`, this also has the `as: 'blog_show'` which sets up the helper method `work_show_path()` which you can use in the views (the default `work_path(work)` would still try the plural 'blogs' causing an error)

## Friendly Routes

Use the [friendly_id gem](https://github.com/norman/friendly_id)

### friendly_id

Can set "reserved words" in the initializer file to stop certain words being used in the URL

#### Model

Update the model to use `friendly_id`

```ruby
class Blog < ApplicationRecord
  extend FriendlyId
  friendly_id :title, use: :slugged
end
```

#### Testing

Open up the `rails console` and run `Blog.create!(title: 'Testing if this has a friendly id', body: 'Will it work?')`

This correctly returns:

```shell
<Blog id: 11, title: "Testing if this has a friendly id", body: "Will it work?", created_at: "2019-09-19 20:17:14", updated_at: "2019-09-19 20:17:14", slug: "testing-if-this-has-a-friendly-id">
```

#### Update Controller

`@blog = Blog.friendly.find(params[:id])`

This pulls in the `params` and maps the `slug` value to the `:id` and then finds it

#### Update Existing URLS

`Blog.find_each(&:save)` - finds every blog and the re-saves

`friendly_id` updates the slug for each time the record is saved

## Enum

- Allows to change state very quickly in data
- Works with integers

### Toggle Draft/Published

`rails g migration add_post_status_to_blogs status:integer`

`enum status: { draft: 0, published: 1 }` - set in the model (default value of status is set to '0' therefore they'll always be in draft till changed)

To set the blog to published write `Blog.last.published!` in the `rails console` (not limited to 'last')

Find all published blogs by querying the database in the `rails console` with `Blog.published` (same with draft but replaced 'published' with 'draft')

Check if published or draft use `Blog.last.draft?` or `Blog.last.published?` (not limited to 'Last')

#### Building

##### Custom Route

To add a custom routes put it in a `do` (block)

```ruby
resources :blogs do
  member do
    get :toggle_status
  end
end
```

##### Controller Action

Check if the `@blog` is in draft, if true switch to published and vice versa

```ruby
def toggle_status
  if @blog.draft?
    @blog.published!
  else
    @blog.draft!
  end
  redirect_to blogs_url, notice: 'Post status was successfully updated.'
end
```

Additional note, formatting like the below code will not work as the conditionals counter each other

```ruby
def toggle_status
  @blog.published! if @blog.draft?
  @blog.draft! if @blog.published?
  redirect_to blogs_url, notice: 'Post status was successfully updated.'
end
```

---

# The Model

## Data Validation

`validates_presence_of :title, :body` in `blog.rb` - this stops the creation of a new blog that does not have a title or a body

## Data Relationships

Hierarchy and connection between different models and tables in the application

### Creating Blog Topics

`rails g model Topic title:string`

Add validation for the `title` after running the above (do it straight away to avoid potential issues in the future)

#### Topic has Blogs or Blogs has Topic?

Answer is **Topic has Blogs**... If it was the other way round it would look like this

```
Blogs:
ID    Title
1     Top 10 Football Goals
2     Football Funnies
3     Best Warframe

---

Topics:
title: football, blog_id: 1
title: gaming, blog_id: 3
```

Which has a clear issue because the `football` topic will have to have multiple `blog_ids` as both blog 1 and 2 are football topics

The correct way, being **Topic has Blogs** would look like this

```
Topics:
ID    Title
1     football
2     gaming

---

Blogs:
title: Top 10 Football Goals, topic_id: 1
title: Football Funnies, topic_id: 1
title: Best Warframe, topic_id: 2
```

#### Adding Reference

`rails g migration add_topic_reference_to_blogs topic:references`

This creates the following:

```ruby
class AddTopicReferenceToBlogs < ActiveRecord::Migration[5.0]
  def change
    add_reference :blogs, :topic, foreign_key: true
  end
end
```

`foreign_key` - this item is not just a regular integer, it is also referencing another table

And in the `schema.rb` it will add `t.integer "topic_id"` to the `blogs` table

#### Updating the Blog/Topic.rb

##### Blog

Needs to know it is **owned** by a topic

`belongs_to :topic`

##### Topic

Needs to know it owns **blog** posts

`has_many :blogs`

#### Testing in Console

`Blog.create!(title: 'Football Goals', body: 'Goooooooal!', topic_id: Topic.first.id)` - the `topic_id` will be 1
`t = Topic.first`
`t.blogs` - this will return the blogs that associated with the topic (uses ActiveRecord::Associations)
`Blog.last.topic` - returns the topic that the blog belongs to

## Custom Scope

A custom database query

`Work.where(subtitle: 'Ruby on Rails')` - this will return the work items that match 'Ruby on Rails' as the subtitle

You can use this in the controller like so, however it's not the best method and instead should be replaced with a custom scope

```ruby
def index
  @work_items = Work.where(subtitle: 'Ruby on Rails')
end
```

### Creating Scope

#### Class Method

`self` - reference the current version of the model, (e.g a particular 'work item' when it's called)

```ruby
def self.javascript
  where(subtitle: 'JavaScript')
end
```

Then in the controller you can use

```ruby
def index
  @work_items = Work.javascript
end
```

### Use Scope

```ruby
scope :javascript, -> { where(subtitle: 'JavaScript') }
```

`-> {}` - lambda (a way of encapsulating an entire process in ruby)

### Set Default Value

#### With Migration

Use this when you know the value won't change, for example when creating an `enum`

`add_column :status, :integer, default: [default value goes here]`

### In the Model

`after_initialize :set_defaults`

- `after_initialize` is one of Rails callbacks and will run on the `new` controller action
- `:set_defaults` is an action/method name (it doesn't have to be called 'set_defaults')

```ruby
def set_defaults
  self.main_image ||= "https://via.placeholder.com/1920x1080"
  self.thumb_image ||= "https://via.placeholder.com/400x400"
end
```

`||=` - is a conditional that checks if the `self.main_image` equals `nil`, if it does then it adds the default value, else it skips

## Concerns

When ever you have functionality that doesn't belong in a model, or should be shared across multiple models then **maybe** use a `concern`

If the code has nothing to do with data, use the `lib` directory, **not** a concern

### Usage

Create a file in the `concerns` directory called `[name].rb` (e.g 'placeholder.rb' in this case)

```ruby
module Placeholder
  extend ActiveSupport::Concern

  def self.image_generator(height:, width:)
    "https://via.placeholder.com/#{height}x#{width}"
  end
end
```

`extend ActiveSupport::Concern` - this loads helper modules which allow for you to do more things (**needs more info**)

`def self.image_generator` - is the class name which can then be called with `Placeholder.image_generator` (see below)

```ruby
def set_defaults
  self.badge ||= Placeholder.image_generator(width: "250", height: "250")
end
```

## Nested Attribute

### Building Work Technology

`rails g model Technology name:string work:references`

Rails will name the table `technologies` due to its ability to do smart auto-pluralisation

Add `has_many :technologies` to `work.rb`

Doing it this way will auto add the `belongs_to :work` in the `technology.rb` file (does not add the `has_many :technologies` to the `work.rb` file)

### Creating Technology Item from Parent

`p = Work.last` - grab single work item
`p.technologies.create!(name: 'JavaScript')` - by default add the work id of `p` to the `technology record`

## Configure Nested Attributes

### Model

`accepts_nested_attributes_for :technologies` - this will get the basics working, but will have issues if the user passes blank data for `technologies`

To stop blank data passing through you can catch it with a `reject_if:` call (see below)

```
accepts_nested_attributes_for :technologies,
                              reject_if: lambda { |attrs| attrs['name'].blank? }
```

This stacked layout (indentation) is a style in Rails that most developers will use

#### Console Testing

```shell
Work.create!(title: 'Testing Nested Technologies', subtitle: 'Test', body: 'Just a test', technologies_attributes: [{name: 'Rails'}, {name: 'JavaScript'}, {name: 'SCSS'}])
```

`technologies_attributes` - is not a variable, it is required to be exactly like that (`[table_name]_attributes`)
`[{name: 'Rails'}, {name: 'JavaScript'}, {name: 'SCSS'}]` - has to be an array to tell Rails it _might_ be a collection, inside it has hashes/objects to group the key/value pairs (relevant to the technology table in the schema `name:string`)

Running the console will return the SQL code inserting the new work item as well as the code inserting the technologies

```sql
SQL (0.2ms)  INSERT INTO "technologies" ("name", "work_id", "created_at", "updated_at") VALUES ($1, $2, $3, $4) RETURNING "id"  [["name", "SCSS"], ["work_id", 12], ["created_at", "2019-09-26 08:18:39.485024"], ["updated_at", "2019-09-26 08:18:39.485024"]]
```

`work_id` - the id of the created work item

Test it worked with `[Name].last.technologies` ('name' is 'work' in my case)

### View

#### Controller

```ruby
def new
  @work_item = Work.new
  3.times { @work_item.technologies.build }
end
```

`3.times { @work_item.technologies.build }` - creates 3 fields for `technologies`

This is the hardcoded way and is not what you would do in production or in modern development (will be updated in a future episode to use JavaScript)

```ruby
def create
@work_item = Work.new(params.require(:work).permit(:title, :subtitle, :body, technologies_attributes: [:name]))
end
```

Tell the strong params to accept an attribute that is a `:name` in the `technology table`

#### HTML

Rails dictates that nested attributes are a form within a form (`fields_for`)

```html
<ul>
  <%= f.fields_for :technologies do |ff| %>
  <li>
    <%= ff.label :name %> <%= ff.text_field :name %>
  </li>
  <% end %>
</ul>
```

The 3 fields created in the controller are what is being called on the `ff.fields_for :technologies`

`:technologies` - is available to the form as the `Work` model has `accepts_nested_attributes_for :technologies`

---

# SQL

[https://guides.rubyonrails.org/active_record_querying.html](Active Record Querying)

`book.author.name` - an author has many books, but from the book you can call the author and the attributes attached to the author object

`Book.where(title: "The Force").author`

- will throw an error as `where` brings back a collection (no matter if only 1 result)
- the class is an `Book::ActiveRecord_Relation`

`Book.find_by_title("The Force").author`

- will return the data you want as it's returning a single result
- the class is actually a book object (so you can call a method on it)

## Dynamic Methods

Rails will look at your `schema.rb` file and will dynamically create a method, hence why `find_by_title` is available

These will only return 1 item, to get a collection/array of items then use `where`

## Calculating

Sum of how many books have been sold by an author - `[author_variable].books.sum(:sales)`

Average overall book sales - `Book.average(:sales).to_f` (`.to_f` will return a float number)

Find highest selling book sales number - `Book.maximum(:sales)`

Find highest selling book - `Book.order('sales DESC')` (this will return all books in order, to get the highest selling book add `.first` to the end of the query)

## Connecting Tables

```
Genre has_many Books
Author has_many Books
```

The table `Books` is a middle man that references 2 tables, therefore you can use Books to connect the tables `Author` and `Genre`

### Connecting Tables

```ruby
class Author < ApplicationRecord
  has_many :books
  has_many :genres, through :books
end
```

```ruby
class Genre < ApplicationRecord
  has_many :books
  has_many :authors, through :books
end
```

Even though `authors/genres tables` doesn't have a `genres/author id` the `books` table does, therefore you can build a relationship

### Testing in Console

`author = Author.first` - returns first author
`author.genres` - will return all the genres associated to the author

## Pluck Data

`Author.pluck(:name)` - returns an array of the author names rather than an ActiveRecord response (returns the specific attributes)

## Book.all vs Book.includes

This creates **A LOT** of database queries which will massively slow down on a large database, therefore you should use:

`Book.includes(:author, :genre)` - the `:author` & `:genre` are the referenced tables that `Books` belongs_to (these are needed in the view as the tables are referenced)

### Example App

In the example app the `Book.all` queries the database 8 times for and took a total of 96.3ms

`Book.includes(:author, :genre)` queried the database 3 times and took a total of 32ms

---

# Authentication

## Install Devise Gem

You can get the `devise gem` from [https://rubygems.org/gems/devise](rubygems.org)

1. Add the `gem devise ~> [version]` to your Gemfile and run `bundle install`
2. run `rails g devise:install`
3. Follow along with the guide that is printed to the console
4. run `rails g devise [model_name]` - 'model_name' is whatever you want to call it (typically 'User')

From here you can edit the migration file, run `rake db:migrate` and then `rails s` to check your code works

If it does, then going to `/users/sign_up` will return the sign up page. Here you can create an account and check it exists in the `rails console` using `User.last`

### Devise Dependencies

Devise has many dependencies, some of the important ones are:

- bcrypt (password encryption for secure passwords)
- orm_adapter (another way to connect to the database)
- warden (many tools, such as storing session data)

### Devise Model Options

- `:confirmable` - user cannot access app until they confirm they are a human being (typically confirm via email)
- `:lockable` - lock a user out if they match criteria (e.g too many failed login attempts)
- `:timeoutable` - can timeout a user (log them out after x amount of time)
- `:omniauthable` - allow for third party login (e.g facebook/google login)

Both `:confirmable` and `:lockable` will need to be un-commented out in the migration file before running `rake db:migrate`

### Config Devise in Migration

You can edit the migration file `devise` generates before you run `rake db:migrate`

In this example I've added `t.string :name` to the migration file

### Adding Personal (from) Email

In the `devise.rb` file search for `config.mailer_sender = 'please-change-me-at-config-initializers-devise@example.com'` and then change to whatever you wish. This will be the email address the user sees when they requests a password reset for example

## Devise Custom Routes

To add custom routes, change `devise_for :users` in the `routes.rb` file to the following

- inside 'path_names' you target the action and then give it a custom route name
- ':users' is the plural for [model_name] I used when running `rails g devise User` (won't always be ':users/User' as it's unique to the app I'm building)

`devise_for :users, path: '', path_names: { sign_in: 'login', sign_out: 'logout', sign_up: 'register' }`

## Devise Logout/Login/Register Button

### Logout

`<%= link_to "Logout", destroy_user_session_path, method: :delete %>`

### Login

`<%= link_to "Logout", new_user_session_path, method: :delete %>`

### Register

`<%= link_to "Logout", new_user_registration_path, method: :delete %>`

### Dynamic Buttons

```
<% if current_user %>
  <%= link_to "Logout", destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to "Register", new_user_registration_path %>
  <%= link_to "Login", new_user_session_path %>
<% end %>
```

Checks if there's a `current_user` session and renders the buttons based on this value

#### How to Find Paths

`rake routes | grep user` - 'user' is unique to the app I'm building, it's not a strict value

## Custom Devise Attributes

Fields added to migration don't auto get added in the views

Before you start with custom attributes, make sure you've run a migration to add the fields you require (if you have not run a migration already)

`protect_from_forgery with: :exception` - is not something added for this step

### Using ApplicationController (Bad Practice)

1. Edit the views to include the new fields in the form

```
<div class="field">
  <%= f.label :name %><br />
  <%= f.text_field :name, autocomplete: "name" %>
</div>
```

2. Update your `application_controller.rb` file

```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception

  before_filter :configure_permitted_params, if: :devise_controller?

  def configure_permitted_params
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name])
    devise_parameter_sanitizer.permit(:account_update, keys: [:name])
  end
end
```

### Using Concern (Good Practice)

#### Class should have a single responsibility, if you have to use the word "and" it should be split

**Class** - Rails specific like an `application_controller` or `model`
**Module** - is something you wanna pass around and share (think modular)

1. Edit the views to include the new fields in the form

```
<div class="field">
  <%= f.label :name %><br />
  <%= f.text_field :name, autocomplete: "name" %>
</div>
```

2. Create a new controller concern `devise_whitelist.rb`

```ruby
module DeviseWhitelist
  extend ActiveSupport::Concern

  included do
    before_filter :configure_permitted_params, if: :devise_controller?
  end

  def configure_permitted_params
    devise_parameter_sanitizer.permit(:sign_up, keys: [:name])
    devise_parameter_sanitizer.permit(:account_update, keys: [:name])
  end
end
```

Module name has to match the file name (every word has to be a capital in the module name)

3. Update the ApplicationController

```ruby
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  include DeviseWhitelist
end
```

`before_filter :configure_permitted_params, if: :devise_controller?` - this is saying, run the action `configure_permitted_params` before all controller actions are called, but only if it is a `devise_controller`

`devise_parameter_sanitizer.permit(:sign_up, keys: [:name])` - tells devise to permit `keys` for a specific `action`

- The action called has to use the original devise name, not one that's been updated in the `routes.rb` file
- You do not need to pass in the pre-exsiting params (e.g `:email` & `:password`)

## Virtual Attribute

An attribute we can call on some models that isn't actually a column name in the database

### Creating First & Last Name

```ruby
validates_presence_of :name

def first_name
  self.name.split.first
end

def last_name
  self.name.split.last
end
```

The `:name` needs to be validated as we are calling methods on it

`self.name` - the name of the specific user record
`split` - is a Ruby method that splits a string into an array (`"John Smith".split` = `["John", "Smith"]`)
`first` & `last` - calls the first & last items in the array

### Update in Development

`User.update_all(name: 'John Doe')` - do not do this in production (in production you'd use some logic to generate name based on email address)

## Using Bcrypt to Implement Encryption

Install Pry - `gem install pry`

Install BCrypt - `gem install bcrypt`

### Usage

`require bcrypt` - gives access to the `bcrypt` library

#### Testing BCrypt

`ssn = BCrypt::Password.create("55555555")`

- connects with the BCrypt password module (
- `ssn` will equal an encrypted string

However, you can still check if `ssn` equals the original string by using `==`:

```ruby
sss == "55555555" // true
sss == "555555556" // false
```

The `==` method is being overwritten by BCrypt, it runs another encryption and compares if the old encryption matches the new (not sure if this is a 100% accurate description)

#### Faster Encryption

`ssn = BCrypt::Password.create("55555555", cost: 4)` - `cost: 4` is the lowest level of encryption available, therefore it would be easier to hack but faster to create

---

# Controllers

## Data Flow

1. **Router** gets the request
2. Stores the **params** in a hash
3. Pass params hash to the **controller** which (typically) calls the model and stores value in an instance variable
4. Render the content from the instance variable in the **view**

## Application Controller

- Parent of all other controllers in the app
- Has access to every controller in the app

## Sessions

Creating a session that tracks where the user came from through a query in the URL

### Creating Session

```ruby
before_action :set_source

def set_source
  session[:source] = params[:q] if params[:q]
end
```

This is added the the `application_controller`

There's nothing special about the name `:source` it's just relevant to the code we're building

It's saying "Set the something on the `session` called `source` equal to the `params[:q]` value"

Storing something in the Rails session means you can call it whenever/wherever you need, for example if you change page the value in the `session[:storage]` will still be present, even without the `?q=value` in the URL

#### Security

Session data is very easy to hijack so do not include any sensitive data in there

#### Use Case

E-commerce site to track what is in the cart

### URL Query

`localhost:3000/blogs?q=twitter`

This will be stored in the session `query string` as well as the `params` ("q" => "twitter")

There's nothing special about "q" it's just a value that we can grab in the code to get the value

### Refractoring Session Tracker

Create a `set_source.rb` file in `controllers/concern`

```ruby
module SetSource
  extend ActiveSupport::Concern

  included do
    before_filter :set_source
  end

  def set_source
    session[:source] = params[:q] if params[:q]
  end
end
```

## Null Object Pattern

`<%= current_user.first_name if current_user %>`

Inline `if` statement is a defensive style of code (considered a bad practive)

```
<% if current_user %>
  <p>Hello <%= current_user.first_name %></p>
<% else %>
  <p></p>
<% end %>
```

This is also a considered a bad practice but is no longer a defensive style of code

### Usage for current_user

```ruby
def current_user
  super || guest_user
end

def guest_user
  OpenStruct.new(
    name: "Guest User",
    first_name: "Guest",
    last_name: "User",
    email: "gues@example.com"
  )
end
```

Add this as a concern and call the concern in the `application_controller.rb` file

Override the `current_user` method to be available even when no user is logged in (stop it being limited to `devise`)

If `current_user` is available then use `current_user` else use the method `guest_user`

#### Super

Whenever you are overriding a method `super` tells it not to change anything, it wants the exact same behaviour in the method that we are overriding

#### OpenStruct

Returns a mini data structure which acts a lot like a database query

```
guest = OpenStruct.new(name: "Your Name", first_name: "Your", last_name: "Name", email: "youremail@email.com")
guest.first_name // will equal "Your"
```

#### Refractor Views

#### From:

`<h1>Hello, <%= current_user.first_name if current_user %></h1>`

#### To:

`<h1>Hello, <%= current_user.first_name %></h1>`

---

#### From:

```
<% if current_user %>
  <p>
    <%= link_to "Logout", destroy_user_session_path, method: :delete %>
  </p>
<% else %>
  <p>
    <%= link_to "Register", new_user_registration_path %>
    /
    <%= link_to "Login", new_user_session_path %>
  </p>
<% end %>
```

#### To:

```
<% if current_user.is_a?(User) %>
  <p>
    <%= link_to "Logout", destroy_user_session_path, method: :delete %>
  </p>
<% else %>
  <p>
    <%= link_to "Register", new_user_registration_path %>
    /
    <%= link_to "Login", new_user_session_path %>
  </p>
<% end %>
```

Now that `current_user` always exists we need to check the `class` - `current_user.is_a?(User)`

---

# Views

## Setting HTML Values

### Update HTML

`<title><%= @page_title %></title>`
`<meta name="keywords" content="<%= @seo_keywords %>" />`

Update these in the `application.html.erb`

### Create Concern

```ruby
module DefaultPageContentConcern
  extend ActiveSupport::Concern

  included do
    before_filter :set_page_defaults
  end

  def set_page_defaults
    @page_title = "My Portfolio"
  end
end
```

Add `include DefaultPageContentConcern` to the `application_controller.rb`

### Updating Controllers

#### Blog

```ruby
def index
  @blogs = Blog.all
  @page_title = "My Blog"
end

def show
  @page_title = @blog.title
end
```

#### Work

```ruby
def index
  @work_items = Work.all
  @page_title = "My Work"
end

def show
  @page_title = @work_item.title
end
```

## Yield

Renders the HTML file that you are calling

## Multiple Layouts

Create a new layout in the `app/views/layouts` folder (`blog.html.erb` for example)

Change the stylehseet and JavaScript links to match the layout (if it's a `blog` layout, use the `blog` CSS/JS)

Add `layout "blog"` at the top of the controller - go into the layout folder and find the file named "blog"

Update the `assets.rb` file with `Rails.application.config.assets.precompile += %w( blogs.scss )` - this will typically be shown in the error

## Partials

A partial is a piece of code that can be shared by view elements

`_[partial_name]` - the underscore tells Rails it's a partial

### Collection Rendering

Create a partial file named `_blog.html.erb` this is where the rendered HTML/ERB code will go

`<%= render @blogs %>` - this calls a collection of `blogs`, iterates over them and prints out the code

Passes in the instance variable from the controller `index` action

### Layout Spacer

`<%= render partial: @blogs, spacer_template: "blog_rules" %>`

`partial:`- is required when using `spacer_template`

`spacer_template` - this calls the file which will house the code to create the spacer (e.g `<hr/>`)

Using this method means the last item that is rendered will not have the spacer, whereas using just HTML the last item will have a spacer component which is not ideal on some designs

#### What/Why

The collection rendering code is doing this:

```
<% @blogs.each do |blog| %>
  <div>
    <p><%= blog.title %></p>
    <p><%= blog.body %></p>
  </div>
<% end %>
```

Because this happens so often the Rails developers built a shorthand method with the collection rendering

#### Custom Collections

It looks for the model name so `@work_items` wouldn't work as the model is called `Work`

Rails doesn't know that we want a different naming convention from what is already setup

##### The Fix

`<%= render partial: 'work_item', collection: @work_items %>`

- Don't need to pass a directory path (e.g `works/work_item`)
- Tells Rails what the collection type is going to be
- Needs the `partial:` tag

### Sending Data

`<%= render 'form', blog: @blog %>` - don't have to pass `locals: {}` for one item (Rails 5+)

## View Helpers

- can call from any view file
- written in Ruby rather than HTML
- keep data in the controller

```ruby
module ApplicationHelper
  def sample_helper
    "<p>Sample Helper</p>".html_safe
  end
end
```

Need `.html_safe` as Rails sanitizes the code to stop potential malicious scripts being added by users (doesn't render HTML unless `.html_safe`)

The point of a view helper isn't purely to render HTML as that would be pointless

Whenever you need conditional logic with HTML, especially useful if the logical code is used more than once

Wanna leave conditional logic outside of the view directly

### View Helper vs Partial

If the logic being implemented has conditionals, or the majority of the logic is based on Ruby code then use a view helper

Use a partial for HTML heavy code

### Refactoring Login/Signup Code

```ruby
module ApplicationHelper
  def login_helper
    if current_user.is_a?(User)
      link_to "Logout", destroy_user_session_path, method: :delete
    else
      (link_to "Register", new_user_registration_path) +
      " / ".html_safe +
      (link_to "Login", new_user_session_path)
    end
  end
end
```

And then call it with `<%= login_helper %>`

#### Weird Return

```ruby
(link_to "Register", new_user_registration_path) +
(link_to "Login", new_user_session_path)
```

This is needed as Rails will return the last value, so wrapping the code in `()` and concatenating the code results in everything being returned rather than the last value

## Content Helper

`content_tag`
`content_for`

### Using content_tag

```ruby
def sample_helper
  "<p>Sample Helper</p>".html_safe
end
```

This is ugly/bad code as it uses the `.html_safe`, a better thing to do would be to use `content_tag`

```ruby
def sample_helper
  content_tag(:p, "Sample Helper")
end
```

#### Refactoring Session Source HTML

```ruby
def session_source_helper(layout_name)
  if session[:source]
    greeting = "Thank you for visiting from #{session[:source]}"
    classname = "source-notice source-notice--#{layout_name}"
    content_tag(:p, greeting, class: classname)
  end
end
```

`layout_name` - is an argument you can pass through from where the helper method is called

`<%= session_source_helper("application") %>` - how you call the content helper method (and pass the argument)

## ActionView Helper Method

### distance_of_time_in_words

`<%= distance_of_time_in_words(blog.created_at, Time.now) %>`

count from `blog.created_at` to `Time.now` and display it in words (e.g '12 days', 'less than a minute')

### number_to_phone

`<%= number_to_phone "5555555555" %>`

will return `555-555-5555`

### number_to_currency

`<%= number_to_currency "150" %>`

will return `$150.00` - you can change the currency return

### number_to_percentage

`<%= number_to_percentage "80.4" %>`

will return `80.400%` - can limit this to `80.4%`

### number_with_delimiter

`<%= number_with_delimiter "23412425435" %>`

will return `23,412,425,435`

### Why Use These?

It's considered bad practice to hardcode when you could use a helper method instead

These can also take arguments making them more attractive than hard coding

[ActionView number helper methods](https://api.rubyonrails.org/classes/ActionView/Helpers/NumberHelper.html)
[ActionView text helper methods](https://api.rubyonrails.org/v5.2.3/classes/ActionView/Helpers/TextHelper.html)

---

# Debugging

## Utilize Puts

- old school way of debugging
- shows in the terminal
- limited as testing is manual

`puts @blogs.inspect` - is hard to read in the terminal, therefore use:

```ruby
puts "*" * 100
puts @blogs.inspect
puts "*" * 100
```

[I am a puts debuggerer blog](https://tenderlovemaking.com/2016/02/05/i-am-a-puts-debuggerer.html)

## Byebug

- is run only in the development and test enviroment
- a working breakpoint where `byebug` is called
- let's you know at what stage an error is occuring
- gives you access to everything when breakpoint is active
- type `continue` to step forward
- have a plan when testing with `byebug`, don't just put it anywhere
- ships with Rails

## Pry

`gem 'pry-byebug'`

`<% binding.pry %>` - in views
`binding.pry` - in `.rb` files

- has better formatting in the terminal over `byebug`
- can set multiple `binging.pry`
- `next` allows you to step to the next line of code
- can navigate from record returns in the terminal with `j (down)` and `k (up)` keys (`q` quits out)
- get the last output with `_`
- check out a method call with `show-method <method name>`
- close a `pry` session even with more `pry`s to come use `!!!`
- to run code on a different line from where `binding.pry` is then use `play -l <line number>.`

## Error Management

### Bad Practice

Don't do this, it hides all errors for the method

```ruby
def method_name
  begin
    run_method
  rescue
  end
end
```

### Good Practice

If an error does occur then return what the error is, it's best to pass in the [error type](https://ruby-doc.org/core-2.2.0/Exception.html) so it's clear what the error is incase there are many

```ruby
def api_connect
  begin
    contact_api
  rescue <error type> => e
    flash[:error] = "Error connecting to API: #{e}"
  end
end
```

---

# Ruby Gems

- never change your `gemfile.lock`
- `gem` names must be unique

`~> <version number>` - this version is fine if it's not too far away from latest version
`>= <version number>` - use this version or newer
`<version number>` - this exact version

## Building a Gem

Build a `gem` that handles the websites copyright data

### Tips

- Start basic
- Add a `module` into whatever file you want to call the `gem` from

### First Build (in App)

```ruby
module EasyCopyrightHelper
  class Renderer
    def self.copyright name, message
      "&copy; #{Time.now.year} | <strong>#{name}</strong> #{message}".html_safe
    end
  end
end
```

Created `module` with a class of `Renderer` that has a method called `copyright` which will generate the code for us

Call in the file it's written with:

```ruby
before_action :set_copyright

def set_copyright
  @copyright = EasyCopyrightHelper::Renderer.copyright("Mark Mead", "All rights reserved")
end
```

`EasyCopyrightHelper::Renderer.copyright` - calls the method created in the `module`

### Build from Scratch

Check for conflicts in names on the [RubyGems](https://rubygems.org/gems/) website

I will link to the [quick_copyright_text](https://github.com/markmead/quick_copyright_text) repo rather than typing it all out

- `bundle gem <gemname>`
- update the `.gemspec` file so that it compiles and has the correct info
- add `*.gem` to the `.gitignore` file otherwise it will error as you should not have the actual `gem` on Github
- `gem build <gemname>.gemspec`

### Publishing to RubyGems

Only do this if you want the rest of the world to use it

Setup a [RubyGems](https://rubygems.org/gems/) account and follow the steps

`bundle exec rake release` - this might throw errors so follow what is said in the terminal

## Adding Pagination with Kaminari

Get the `kamniari gem` from [RubyGems](https://rubygems.org/gems/kaminari)

[Kaminari GitHub](https://github.com/kaminari/kaminari)

As the docs don't match the guide I am taking here is the link to where I add it to the blogs in the [setup pagination pull request](https://github.com/markmead/learning-portfolio/pull/7)

---

# Authorisation

Set permissions for what particular user groups can do on your website

## Petergate Gem

`gem 'petergate', '~> 1.6', '>= 1.6.3'` - install the [petergate](https://github.com/elorest/petergate) gem and follow the README guide

### Confilct with OpenStruct

**Implementing Null Object Pattern**

Petergate expects to call Devise methods which OpenStruct does not have, therefore we need to refractor:

#### Create GuestUser Model

Create a `guest_user.rb` model file that inherits the `user` model

```ruby
class GuestUser < User
  attr_accessor :name, :first_name, :last_name, :email
end
```

`attr_accessor :name, :first_name, :last_name, :email` - getters and setters (provides ability to have data)

#### Remove OpenStruct from Concern

```ruby
def guest_user
  guest = GuestUser.new
  guest.name = "Guest User"
  guest.first_name = "Guest"
  guest.last_name = "User"
  guest.email = "guest@example.com"
  guest
end
```

The `login_helper` needs to be updated as `GuestUser` is inheriting from `User`, so it should be refactored to:

```ruby
if current_user.is_a?(GuestUser)
   (link_to "Register", new_user_registration_path) +
   " / ".html_safe +
   (link_to "Login", new_user_session_path)
else
  link_to "Logout", destroy_user_session_path, method: :delete
end
```

### Setting User Roles

`access all: [:show, :index], user: { except: [:new, :edit, :create, :update, :destroy] }, site_admin: :all`

Set this in the `<name>_controller` that you want to add roles to (edit where necessary)

If you haven't already, update the user `role` value in the `rails console`

### Conditional Render in Views

Remove the admin specific methods from the views by using `if logged_in?(:site_admin)`

---

# JavaScript

## Adding Drag & Drop

1. `rails g migration add_position_to_work position:integer` - add a value that can be used to sort & update the work items
2. `Work.order("position ASC")` - order work items based on the position value, order from lowest to highest

### Add Scope in Model

```ruby
def self.by_position
  order("position ASC")
end
```

`@work_items = Work.by_position` - then use this in the controller

3. Add [html5sortable JS](https://github.com/lukasoppermann/html5sortable) & [jquery-ui-rails gem](https://rubygems.org/gems/jquery-ui-rails)

Not entirely sure `jQuery UI Rails` is needed as `html5sortable` is now written in vanillaJS

### Building the Frontend

- add a class to the parent element div
- call `.sortable()` in the correct JS file

### Building the Backend

- add `data-id` which represents the id of the current work item (allows us to distinguish it and grab it in the JS code)
- use `$.ajax` to make a put request to a specific path and then update the value in the database

```
$.ajax
  type: "PUT"
  url: "/works/sort"
  data: order: updated_order return
```

`type` - type of method (`puts` is used to update a value)
`url` - where we are communicating
`data` - params hash that can be accessed from the controller (take `updated_order` array and pass it into `orders hash`)

#### Routing Error

Currently there will be a routing error, so we need to add the route

```ruby
resources :works, except: [:show] do
  put :sort, on: :collection
end
```

`on: :collection` - whenever you see `portfolio/sort` go do something else

#### View Error

Now the error should be saying it doesn't have a view, but we don't want it to have a view so we need to give it some actions

```ruby
def sort
  params[:order].each do |key, value|
    Work.find(value[:id]).update(position: value[:position])
  end

  head :ok
end
```

Find the work item with the `id` passed in from the `orders params`, then update the `position` value based on what is in the `orders param`

`head :ok` or `render nothing: true` - bypasses the traditional Rails "look for a view" (only database, no view)
`render nothing: true` - has been deprecated in Rails 5.1+ so use `head :ok`

### Add Auth to Drag & Drop Functionality

Add the `sort` action into the `user except` hash in the work controller

`<%= "sortable" if logged_in?(:site_admin) %>` - it's always best practice to also limit the front end as well

---

# jQuery & Coffescript

Coffescript is used as it closely resembles Rails code, it is also shipped with it by default with Rails

## jQuery + Coffescript

```
$ ->
  alert("Hello")
```

Shorthand method to run `document.ready`

```
$ ->
  $("#myButton").click ->
    alert("Hello")
```

Shorthand method to call the `on click` event

## Ajax

`type: "POST"` - create
`type: "PUT"` - update

Get the `URL` to post to for Ajax by using `rake routes` and looking for the matching `type` (e.g for `POST` look for the `POST` URL)

You have to pass `data` as Rails would read it (e.g `blog[title]=Here is my new blog create with Ajaz`)

---

# Adding Images

## Asset Pipeline Video

Add a `video` folder which will hold all the videos for your website

```
<%= video_tag(
  video_url_goes_here.mp4,
  id: "video",
  autoplay: true,
  loop: true
  muted: true,
  poster: "poster_image_goes_here.png"
)%>
```

## Image Uploader

Bad practice to store images in the database

### Install & Configure Carrierwave

Add these `gems` and run `bundle install`

```ruby
gem 'carrierwave', '~> 2.0', '>= 2.0.2'
gem 'mini_magick', '~> 4.9', '>= 4.9.5'
gem 'carrierwave-aws', '~> 1.4'
gem 'dotenv-rails', '~> 2.7', '>= 2.7.5'
```

### Generate Uploader

`rails generate uploader Work`

Uncomment the whitelist method so that it doesn't cause filetype errors in the HTML

```ruby
def extension_whitelist
  %w(jpg jpeg gif png)
end
```

### Linking Image in Model

```ruby
mount_uploader :thumb_image, WorkUploader
mount_uploader :main_image, WorkUploader
```

`mount_uploader` - tells the `Work` class to call `CarrierWave`
`thumb_image` - uploader is gonna apply to the thumb_image
`WorkUploader` - the uploader to use (in theory it overrides everything and sets the field as a CarrierWave field)

### Editing the Form

`<%= f.file_field :main_image %>`
`<%= f.file_field :thumb_image %>`

Update the controller if the params `:main_image` and `:thumb_image` are missing from the strong params

These will be stored in the `public` folder and the folder/name is set in the `work_uploader.rb` `store_dir` method

### Connecting to AWS

#### ENV

Create a new file named `.env` and add the `AWS` variables

```
S3_BUCKET_NAME=
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_REGION=
```

**Remember to include the `.env` file in your `.gitignore`**

Check this by running `git status` to see if it's an "untracked" file

#### CarrierWave

Add the `carrierwave.rb` file to the `config/initializers` and paste in the [configuration code](https://github.com/sorentwo/carrierwave-aws)

#### AWS

Update `storage :file` to `storage :aws` in the `work_uploader.rb` file

---

`blog[title]` - is the way to insert items and pass them as parameters to Rails

# Forms

## form_for

Uses `puts`

Knows when to use `puts` or `post`

Doesn't require the hardcoded has value (e.g `:title` = `blog[title]` in a `form_tag`)

## form_tag

Uses `post`

This will create a new blog even if you are editing it (`create` action uses `post`)

Never use for CRUD functionality

Requires you to hardcode hash values (e.g `blog[title]`)

Use when they don't have a direct connection with the model (e.g to search)

## Authenticity Token

Validate the user session

## Nested Form (Cocoon)

[RubyGems cocoon](https://rubygems.org/gems/cocoon)

`gem 'cocoon', '~> 1.2', '>= 1.2.14'`

[Docs](https://github.com/nathanvda/cocoon)

`//= require cocoon`

### Adding

`<%= link_to_add_association "Add Technology", f, :technologies %>` - saying bring me a new technology field (`link_to_add_association` is a cocoon thing)

### Deleting

`allow_destroy: true` in `accepts_nested_attributes_for :technologies` - gives permission to `work` to delete the nested attribute (technologies)

Pass `technologies_attributes: [:id, :name, :_destroy]` into the `params` for the `work model` (`_destroy` is a special attribute needed by cocoon)

## Form Alerts (Gritter)

[RubyGems gritter](https://rubygems.org/gems/gritter)

`gem 'gritter', '~> 1.2'`

`//= require gritter`
`*= require gritter` or `@import "gritter";`

### Creating Alert

`<%= js add_gritter(flash[:alert], title: "Please pay attention!") %>`

- need to add `js` otherwise it will not render correctly
- the text in `title` renders above the text from the `flash` message

### Adding Errors to Form

```
<% if blog.errors.any? %>
  <% blog.errors.full_messages.each do |error| %>
    <%= js add_gritter(error) %>
  <% end %>
<% end %>
```

### Refractor as Helper Method

```ruby
def alerts
  alert = (flash[:alert] || flash[:error] || flash[:notice])
  if alert
    js add_gritter(alert)
  end
end
```

`alert` - check for any `flash` message, if a message exists then set `alert` to the `flash` message value

Check if `alert` exists and then render a `gritter` message with the `alert` value

#### Issue

This will not work with the forms as the form errors are not stored in the `flash` message

### Refractor the Refractor

Still in the `application_helper.rb`

```ruby
def alerts
  alert = (flash[:alert] || flash[:error] || flash[:notice])
  if alert
    alert_generator(alert)
  end
end

def alert_generator(msg)
  js add_gritter(msg)
end
```

`def alerts` became too complex to work with as it was handling the check and render, therefore using `def alert_generator` for the rendering helped lower the complexity

#### View

```
<% if blog.errors.any? %>
  <% blog.errors.full_messages.each do |error| %>
    <%= alert_generator(error) %>
  <% end %>
<% end %>
```

## Building a Form

`action` - where the form should post data to
`method` - use `put` for edit, use `post` for create, use `get` for search

### CSRF Token Authenticity

Stops the form being sent by a user that is not recognised on the system

`<input type="hidden" value="<%=form_authenticity_token %>" name="authenticity_token" />`

- Creates new token on each refresh
- Will only let the user post the form if the authenticity token exists and if it matches something on the server side to verify it

---

# Lib Directory

Place to put isolated code components that can live outside of the rest of the application

Good place to work on a code that could become a `gem`

By default Rails will not load what's in the `lib` directory, therefore you need to call is in the `application.rb` file

```ruby
module Portfolio
  class Application < Rails::Application
    config.eager_load_paths << "#{Rails.root}/lib"
  end
end
```

## Rendering Tweets

### Twitter Gem

[RubyGems twitter](https://rubygems.org/gems/twitter)

[Documentation](https://github.com/sferik/twitter)

`gem 'twitter', '~> 6.2'`

```ruby
module SocialTool
  def self.twitter_search
    client = Twitter::REST::Client.new do |config|
      config.consumer_key        = ENV.fetch("YOUR_CONSUMER_KEY")
      config.consumer_secret     = ENV.fetch("YOUR_CONSUMER_SECRET")
      config.access_token        = ENV.fetch("YOUR_ACCESS_TOKEN")
      config.access_token_secret = ENV.fetch("YOUR_ACCESS_SECRET")
    end

    client.search("#rails", result_type: "recent").take(6).collect do |tweet|
      "#{tweet.user.screen_name}" - "#{tweet.text}"
    end
  end
end
```

`self` - means you call it on the module

`SocialTool.twitter_search`

### Twitter API

Register an app that will use the Twitter API [here](https://developer.twitter.com/en/apps)

Add your keys and tokens into the `.env` file

### Rendering in View

1. create route
2. call `@tweets = SocialTool.twitter_search` in a controller action
3. render `@tweets` in the matching named view file

### Making Links Clickable

Currently links in the tweets are not clickable

#### Building a Parser View Helper

Regex to find links
gsub (gloal substitution)
URI

```ruby
module PagesHelper
  require 'uri'

  def twitter_parser tweet
    tweet = "These videos on the new Apple Privacy page are so cool 🤩https://apple.com/privacy/."
    tweet.gsub(URI.regexp(%w(http https))) do |url|
      url = url.chomp(".")
      "<a href='#{url}' target='_blank'>#{url}</a>"
    end.html_safe
  end
end
```

---

# Action Cable

Users on the same page will see live data updating without refreshing the page

## Overview

## Redis

- Little database that is incredibly fast
- Cannot connect to database tables
- Key/value database

[RubyGems redis](gem 'redis', '~> 3.3', '>= 3.3.1')

`//= require cable` - in `application.js`

## Creating Comment

`rails g resource Comment content:text user:references blog:references`

Connected to `users` and `blogs`

A `user` can have many comments and a `blog` can have many comments

Add `has_many :comments, dependent: :destroy` to the `blog` and `user` model

`dependent: :destroy` - when a user deletes their account or a blog is deleted, it will delete all their comments

Because we used the resource generator and passed the references, Rails auto attaches the `comment` class to the `user` and `blog` model

```ruby
class Comment < ApplicationRecord
  belongs_to :user
  belongs_to :blog

  validates :content, presence: true, length: { minimum: 5, maximum: 1000 }
end
```

Validate that the comments content is present and reaches the min/max length

### Comment Controller

```ruby
class CommentsController < ApplicationController
  def create
    @comment = current_user.comments.build(comments_param)
  end

  private

  def comments_param
    params.require(:comment).permit(:content)
  end
end
```

The way `current_user` works, it will grab the current user and auto associate the `user` with a `comment`

### Blogs Controller

Override the `show` action to use `includes` to improve performance as it calls the `blog` and the `comment` rather than having to hit the database multiple times

```
def show
  @blog = Blog.includes(:comments).friendly.find(params[:id])
  @comment = Comment.new
end
```

`@comment = Comment.new` - because there will be a form to create a new comment

### Create Broadcast

Think like a TV, a broadcast has channels

#### Routes

Can delete `resources :comments` as we are using the `ActionCable` server with `mount ActionCable.server => "/cable"` which opens up a web socket connection and gives a direct feed into our `redis` database

#### Model

`after_create_commit { CommentBroadcastJob.perform_later(self) }`

`perform_later` - perform this whenever you have a chance (if taking a lot of requests you will run into fewer errors)

#### Jobs

```ruby
class CommentBroadcastJob < ApplicationJob
  queue_as :default

  def perform(comment)
    ActionCable.server.broadcast "blogs_#{comment.blog.id}_channel", comment: render_comment(comment)
  end

  private

  def render_comment(comment)
    CommentsController.render partial: "comments/comment", locals: { comment: comment }
  end
end
```

`queue_as :default` - create a list and serve them in order
`ActionCable.server.broadcast` - tie into `ActionCable`, it's a server connection and then broadcast channel
`"blogs_#{comment.blog.id}_channel"` - is the channel we are creating (can vary depending on what blog the user is on)
`CommentsController.render partial: "comments/comment", locals: { comment: comment }` - call the comments controller and render a partial

### Implementing Live Data

#### Created Channel

```ruby
class BlogsChannel < ApplicationCable::Channel
  def subscribed
    stream_from "blogs_#{params['blog_id']}_channel"
  end

  def unsubscribed
  end

  def send_comment(data)
  end
end
```

`subscribed` and `unsubscribed` are from `ActionCable` and are required

`unsubscribed` - user left the page where the channel is live

`subscribed` - what channel they are subscribed to

#### Connection

ActionCable and web sockets don't have the same access points as HTTP connects

Have access to different params because how data flow works

Need to recreate `current_user`

```ruby
module ApplicationCable
  class Connection < ActionCable::Connection::Base
    identified_by :current_user

    def guest_user
      guest = GuestUser.new
      guest.id = guest.object_id
      guest.name = "Guest User"
      guest.first_name = "Guest"
      guest.last_name = "User"
      guest.email = "guest@example.com"
      guest
    end

    def connect
      self.current_user = find_verified_user || guest_user
      logger.add_tags "ActionCable", current_user.email
      logger.add_tags "ActionCable", current_user.id
    end

    private

    def find_verified_user
      if verified_user = env["warden"].user
        verified_user
      end
    end
  end
end
```

`logged.add_tags "ActionCable"` - see what is happening in the terminal
`env["warden"].user` - as we don't have access to all the `current_user` functionality, so `warden` can bypass that
`guest.object_id` - is a object in memory that is very quick and easy to access, should also be unique

---

# Best Practice Updates

## Scope for Most Recent Blogs

`scope :newest_first, -> { order("created_at DESC") }`

## Show Published Blogs

`.published` - possible because of the `enum status: { draft: 0, published: 1 }`

## Hide Blog Show Page When Unpublished

`if logged_in?(:site_admin) || @blog.published?` - only show the pages when the admin or the blog is published

## Add Topic Dropdown to Blog

1. require the `topic_id` for the blog
2. pass `topic_id` as a strong param
3. add form element using the `collection_select` Rails helper

---

# Heroku

## Installing Redis

1. Add `redis cloud` in the `heroku addons`
2. Update the `config/cable.yml` file to have `<%= ENV["REDISCLOUD_URL"] %>` in the `production url`

3. Set what URLs can use ActionCable in the `production.rb` file

```ruby
config.action_cable.allowed_request_origins = ["http://my-url.herokapp.com", "https://my-url.herokapp.com"]
config.action_cable.url = "wss://my-url.herokapp.com/cable"
```

---

# Encrypted Credentials

Rails 5.2+ changed the way it handled credentials

It creates a `credentials.yml.emc` which can be edited/decrypted when you run `rails credentials:edit`

`Rails.application.credentials.something_here` - will return the value of the "something_here" key when you're in the `rails console`

`rails secret` - will generate a secrey key

Add `RAILS_MASTER_KEY` with the value being the apps `master_key` to Heroku to get the app working, otherwise Heroku can't read your credentials
