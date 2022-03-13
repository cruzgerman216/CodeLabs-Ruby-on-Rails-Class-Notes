# BookIt App Part 1

---

### Table of Contents

- <a href="#generating-a-new-rails-app">Generating a new Rails app</a>
- <a href="#adding-a-migration">Generating a Migration file</a>
- <a href="#adding-a-model">Creating a Model class</a>
- <a href="#rails-console">Rails Console and ORM methods</a>
- <a href="#adding-validations">Adding Validations</a>
- <a href="#adding-validations">ByeBug</a>
- <a href="#index-page">Index Page for books</a>

---

In a 7 part series, we will be creating a Full-Stack Rails application called the BookIt application.

![Book It Application](../assets/images/C8/BookIt-App.png)

<div id="generating-a-new-rails-app"></div>

--- 

## Generating a new Rails app

In a Code Editor (such as VS code), in the terminal, generate a new rails app called `book-it`

```
rails new book-it
```

Run `rails s` and go to `localhost:3000` to check if successfully created

<div id="adding-a-migration"></div>

--- 

## Adding a migration

Let's create a migration file so we can create/update the schema file(The schema file is in charge of what the data looks like when saving/deleting/creating/reading). Enter the following

```
rails g migration create_books
```

Navigate to `db/migrate` to locate the specified migration file you created. Add a `title` attribute to the table. Make it of type `string`. Leave the `timestamps` atribute there.

```ruby
class CreateBooks < ActiveRecord::Migration[6.1]
  def change
    create_table :books do |t|
      t.string :title
      t.timestamps
    end
  end
end
```

Enter `rails db:migrate` to create the books table. Check the details of the table in the schema file
. `rails db:migrate` will run any new migration files (running it again will not do anything unless we create a new migration file)

Generate a new migration file to add the description attribute to the books table. Enter the following:

```
rails g migration add_description_to_books
```

Call the `add_column` method within the change method and include the table name `books`, field `description` and the type `text` as arguments and symbols.

```ruby
class AddDescriptionToBooks < ActiveRecord::Migration[6.1]
  def change
            #  table  | field      |  type
    add_column :books, :description, :text
  end
end
```

Because we added a new migration file, enter `rails db:migrate`
Check to see if the schema updated from the latest migration

<div id="adding-a-model"></div>

--- 

## Creating a Model class

Create a file called `book.rb` under the models folder. Define a `Book` class and inherit from `ApplicationRecord`.

```ruby
class Book < ApplicationRecord

end
```

<div id="console"></div>

--- 

## Rails console and ORM methods

Enter `rails c` in the console, and follow the lines of code. Create a book and save it to the database

```ruby
    Book.all
    Book.create(title: "first book", description: "Description of first book")
    Book.all
```

Create a book and save it to the database with the `new` and `save` method

```ruby
    Book = Book.new
    Book.title = "second book"
    Book.description = "description of second book"
    Book.save
    Book.all
```

Finding specific instances from the books table using `first`, `all`, `find` methods.

```ruby
    Book.first # gives you first instance from the Books table
    Book.all[0] # gives you the first instance from the Books table
    Book.find(1) # takes in an id and returns the specific book instance that holds id of 1

```

You can also update the attributes. Be sure to use the `save` method to be able to update the changes.

```ruby
    book = Book.first
    book.title = "Updated title"
    book.save # saves updated attributes
```

Deleting the instances using the `destroy` method.

```ruby
    book.last.destroy # deletes last record from the Book Table
```

--- 

## Adding Validations

In the `book` model class, validate the `title` and `description` attributes

```ruby
class Book < ApplicationRecord
    validates :title, presence:true
    validates :description, presence:true
end
```

Validate necessary lengths for both title and description

```ruby
class Book < ApplicationRecord
    validates :title, presence:true, length: {minimum: 2, maximum: 100}
    validates :description, presence:true, length: {minimum: 6, maximum: 200}
end
```
--- 

## Pages controller

Generate a controller called `pages`

```
rails g controller Pages
```

Define instance methods `home` and `about` in the `pages` controller

```ruby
    def home

    end

    def about

    end
```

In `routes.rb`, create a `root` path that corresponds to the `home` method and the `about` method that exists in the `pages` controller

```ruby
Rails.application.routes.draw do
  root 'pages#home'
  get 'about', to: 'pages#about'
end

```

Create views for corresponding methods in the pages controller. Under the `views/pages`, create a `home.html.erb` file and create an `h1` element with content `Home`

Create another file called `about.html.erb` and create an `h1` element with content `About`

Run `rails s` in the terminal

**<em>NOTE</em> We don't have to render anything in the home and about methods, rails is smart enough to match the method names with the corresponding view file names in the file structure**

--- 

## Adding a resource

Call the `resources` method and pass in `:books`. This will create a resource and generate routes for `index`, `show`, `edit`, `create`, `update` and `destroy` methods in your `books` controller file.

```ruby
Rails.application.routes.draw do
  root 'pages#home'
  get 'about', to:'pages#about'
  resources :books
end
```

Create a file called `books_controller`.rb under controllers
Define a `BooksController` class that inherits ApplicationController

```ruby
class BooksController < ApplicationController
end
```

Define the show action under the books controller

```ruby
class BooksController < ApplicationController
    def show
    end
end
```

Create a folder called `books` under the views directory. Create a file under `books` called `show.html.erb.` There, create an `h1` element with content `Showing book details`

```html
<h1>Showing book details</h1>
```

Run rails s and go to the route `localhost/3000/books/1`

- **NOTE** Be sure the instance that has this id exists. iI not, use another id in the route.
- **EXPLANATION**

```ruby
  resources :books
```

Resources define routes to certain actions from the given controller. For Example, the` /books/:id` route only refers to the show method. Run `rails routes` in the console. Scroll up to see each given route and their actions provided by this application.

The `resources` method is a quick way to implement common CRUD operations for routes. Otherwise, we would have to define these routes ourselves.

Under the show method in the `books_controller.rb `file, get the specific book using the params

```ruby
    def show
        book = Book.find(params[:id])
    end
```

Declare book as an instance variable so our view can access it

```ruby
        @book = Book.find(params[:id])
```

In `show.html.erb` under the books directory, include the following

```html
<h1>Showing book details</h1>

<p><strong>Title: </strong></p>
<p><strong>Description: </strong></p>
```

Use embedded tags to access info from the book instance

```html
<h1>Showing book details</h1>

<p><strong>Title: </strong><%= @book.title %></p>
<p><strong>Description: </strong><%= @book.description %></p>
```

<div id ="byebug" ></div>

--- 

## ByeBug Demonstration

In the `books` controller, use `byebug` to test the route/access variables. The `byebug` gem should be included in your gemfile, if not be sure to add it under development and test

```ruby
group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end
```

Under the `show` method, include `byebug`

```ruby
    def show
        byebug
        @book = Book.find(params[:id])
    end
```

Reload the page to the `/books/:id` route and your terminal should pause. Enter params and you should see key pair values such as id. Type `continue` to continue the action. Comment `byebug` as we don't need it for now.

<div id="index-page"></div>

--- 

## Index page for Books

In the `books` controller class, define an `index` instance method

```ruby
    def show
        @book = Book.find(params[:id])
    end

    def index

    end
```

Create a erb view for index called index.html.erb under views/books directory. Include the following

```html
<h1>Books listing page</h1>
```

Run `rails s `and enter the `localhost:3000/books` in the url bar to test the route

Under the `index` method, store all Books from the database in an instance variable called books

```ruby
    def index
        @books = Book.all
    end
```

In `index.html.erb`, iterate through `@books` and print out each content

```html
<h1>Books listing page</h1>

<table>
  <thead>
    <tr>
      <th>Title</th>
      <th>Description</th>
      <th>Actions</th>
    </tr>
  </thead>

  <tbody>
    <% @books.each do |book| %>
    <tr>
      <td><%= book.title%></td>
      <td><%= book.description%></td>
      <td>placeholder</td>
    </tr>
    <% end %>
  </tbody>
</table>
```

---
Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 