# BookIt App Part 4 - Associations and Authentication Systems Part 1

### Table of Contents

- <a href="#users">Adding Users</a>
- <a href="#association">Associations Between Users and Books</a>
- <a href="#user-info">Show User Info In Book Cards</a>
- <a href="#bcrypt">Hashing passwords with Bcrpyt</a>
- <a href="#sign-up">Allow Users to Sign Up</a>
- <a href="#edit-users">Edit Users</a>
- <a href="#profile-page">Profile Page</a>
- <a href="#user-index">User Index</a>

---

## Terminology

<details>
<summary><strong>Associations</strong></summary>
<em>Association</em> is a type of relationship between two models (Ex: books and authors)
  <details style="margin-left:10px">
  <summary><strong>Has many </strong></summary>
  <em>Has many </em> indicates that one instance of a model has either one or more instances of another model (Ex: An author has many books)
  </details>
  <details style="margin-left:10px">
  <summary><strong>Has one</strong></summary>
  <em>Has one</em> indicates an instance of a model in relation to another model instance and no more (Ex: A user has one profile).
  </details>
  <details style="margin-left:10px">
  <summary><strong>Belongs to</strong></summary>
  <em>Belongs to</em> indicates an instance of a model in relation to another model instance in which it belongs to that model. This table requires a foreign key. (EX: A book belongs to a user.)
  </details>
  <details style="margin-left:10px">
  <summary><strong>Many to Many</strong></summary>
  <em>Many to Many</em> indicates two model instances that have more than one relation with each other (EX: An author has many books. A book has many authors). A many to many association requires a third table to that holds two foreign keys of both models.
  </details>
</details>
<details>
<summary><strong>ERD or Entity Relationship Diagram</strong></summary>
<em>ERD or Entity Relationship Diagram</em> is a way to view the association between models.
</details>

<br>

---

<div id="users"></div>

## Adding Users

In the terminal, generate a migration file that will create a users table `rails generate migration create_users`. In the migration file, include attributes `username` and `email` as well as timestamps.

```ruby
    create_table :users do |t|
      t.string :username
      t.string :email
      t.timestamps
    end
```

Run `rails db:migrate`.

Navigate to the models folder, create a `user.rb` file. Define a class called `User` that inherits ApplicationRecord:

```ruby
class User < ApplicationRecord
end
```

### Add user validations to User class

In `models/users.rb`, let's create a validation that will allow each username to be unique.

```ruby
class User < ApplicationRecord
    validates :username, uniqueness: true
    validates :email, uniqueness: true
end
```

Make both username and email present in each creation.

```ruby
class User < ApplicationRecord
    validates :username, uniqueness: true, presence: true,
    validates :email, uniqueness: true, presence: true,
end
```

Make the length of the username minimum 3 and maximum 25. Validate email to be a maximum of 25 characters.

```ruby
class User < ApplicationRecord
    validates :username, uniqueness: true, presence: true, length: {minimum: 3, maximum: 25}
    validates :email, presence: true, length: {minimum: 5, maximum: 25}, uniqueness: true
end
```

To validate the email with the correct format we can user the built in Ruby URI to do this.

```ruby
  validates :email, uniqueness: true, presence: true, length: { maximum: 25 }, format: { with: URI::MailTo::EMAIL_REGEXP }
```

<div id="association"></div>

## Associations Between Users and Books

### Has many association

We need to add a `user_id` to the book's table so we can create a one to many association. Let's generate a migration file with the following `rails generate migration add_user_id_to_books`. Navigate to the migration file under migrate and add a new column to the books table called `user_id` that is of type `int`.

```ruby
class AddUserIdToBooks < ActiveRecord::Migration[6.1]
  def change
    add_column :books, :user_id, :int
  end
end
```

Run `rails db:migrate` to reflect these migrations towards the DB/schema.

Let's now create a one to many association between Users and Books.

```ruby
class User < ApplicationRecord
  has_many :books
  validates :username, uniqueness: true, presence: true, length: { minimum: 3, maximum: 25 }
  validates :email, uniqueness: true, presence: true, length: { maximum: 25 }, format: { with: URI::MailTo::EMAIL_REGEXP }
end
```

### Belongs to association

```ruby
class Book < ApplicationRecord
    belongs_to :user
    validates :title, presence:true, length: {minimum: 2, maximum: 100}
    validates :description, presence:true, length: {minimum: 6, maximum: 200}
end
```

In order to create a Book, each book record now needs a `user_id` due to the `belongs_to` association. This creates a connection between the book record and user record. Every book now belongs to a particular user.

Create a user record in `rails c`

```
User.create(username: test123, email: test@test.com)
```

Navigate to controllers/books_controller.rb, under the create method. Assign each book that gets created to the first User in the db, for now.

```ruby
    @book = Book.new(book_params)
    @book.user = User.first
    if @book.save
      flash[:notice] = "Book was created successfully"
      redirect_to @book
    else
```

<div id="user-info"></div>

## Show User Info In Book Cards

Navigate to `views/books/index.html.erb`, let's creates a card header that show's the info of the user that belongs to that books. If a book belongs to the user, then print the username.

```html
<div class="col-md-3">
  <div class="card">
    <div class="card-header">
      <%= "By " + book.user.username if book.user %>
    </div>
  </div>
</div>
```

Repeat the same process in `show.html.erb`.

```html
<div class="container">
  <div class="card" style="margin: 10% 25%">
    <div class="card-header">
      <%= "By " + book.user.username if book.user %>
    </div>
  </div>
</div>
```

<div id="bcrypt"></div>

## Hashing passwords with Bcrpyt

Navigate to your `gemfile` and uncomment `Bcrypt`.

```ruby
# Use Active Model has_secure_password
gem 'bcrypt', '~> 3.1.7'
```

Run `bundle install`
Include `has_secure_password` in your user model

```ruby
class User < ApplicationRecord
    has_secure_password
    before_save { self.email = email.downcase }
    has_many :books
```

Create a migration file to add `password_digest` to the users table `rails generate migration add_password_digest_to_users`.
In the newly created migration file, create a `password_digest` column under the users table that is of type `string`.

```ruby
class AddPasswordDigestToUsers < ActiveRecord::Migration[6.1]
  def change
    add_column :users, :password_digest, :string
  end
end
```

Run `rails db:migrate` to reflect these newly added changes onto the db and schema file.

<div id="sign-up"></div>

## Allow Users to Sign Up

Build a route that navigates the user to the sign up page.

```ruby
  resources :books
  get 'signup', to: 'users#new'
```

Create a new file under controllers called `users_controller.rb`. Create a `UsersController` that inherits `ApplicationController`. Define a method called `new`

```ruby
class UsersController < ApplicationController
  def new
    @user = User.new
  end
end
```

Create a folder under views called users. Create files `_form.html.erb` and `new.html.erb` under the users folder. In `new.html.erb`, render the partial form and also create an `h1` element with content `Sign up to Book It`

```html
<h1 class="text-center">Sign up to Book It</h1>
<%= render 'form' %>
```

Navigate to` _form.html.erb`. Copy the form from `books/_form.html.erb` and paste the following. Edit the following labels and fields to correspond to user attributes.

```html
<div style="margin: 0 25%; width: 50%">
  <%= render 'shared/errors' %> <%= form_with(model: @user) do |form| %>
  <div class="mb-3">
    <%= form.label :username, "Username:", class:"form-label" %> <%=
    form.text_field :username, class:"form-control" %>
  </div>
  <div class="mb-3">
    <%= form.label :email, "Email:", class:"form-label" %> <%= form.text_field
    :email, class:"form-control" %>
  </div>
  <div class="mb-3">
    <%= form.label :password, "Password:", class:"form-label" %> <%=
    form.password_field :password, class:"form-control" %>
  </div>
  <%= form.submit "Sign up", class:"btn btn-primary" %> <% end %>
</div>
```

### Refactoring errors partial

An error will occur in `shared/errors` as we render `new.html.erb` due to being instance specific to book. Let's change this to make it be flexible to any instance. Navigate to `_errors.hmtl.erb`.

```html
<% if obj.errors.any? %>
<ul class="alert alert-warning alert-dismissible fade show">
  <h3>The following errors prevented the book to be saved</h3>
  <% obj.errors.full_messages.each do |message| %>
  <li style="color: red; margin: 1%"><%= message %></li>
  <% end %>
  <button
    type="button"
    class="btn-close"
    data-bs-dismiss="alert"
    aria-label="Close"
  ></button>
</ul>
<% end %>
```

In the `h3` element, we specify the instance to be of type `Book` but that is not the case for User instances. Instead, let's replace this to specify the instance of the errors.

```html
<h3>The following errors prevented the <%= obj.class %> to be saved</h3>
```

Navigate to `books/_form.html.erb` and set `obj` to the `@book` instance when rendering errors.

```html
<div style="margin: 0 25%; width: 50%">
  <%= render 'shared/errors', obj: @book %> <%= form_with(model: @book) do
  |form| %>
  <div class="mb-3"></div>
</div>
```

Repeat same process for `users/_form.html.erb`

```html
<div style="margin: 0 25%; width: 50%">
  <%= render 'shared/errors', obj: @user %> <%= form_with(model: @user) do
  |form| %>
  <div class="mb-3"></div>
</div>
```

Create a resource route for users

```ruby
  get 'signup', to: 'users#new'
  resources :users, except: [:new]
end
```

### Create new users

In the users controller, create a private method named `user_params`. Use params to require the specific set of info: `username`, `email`, `password`.

```ruby
    def user_params
        params.require(:user).permit(:username, :email, :password)
    end
```

Define a method called `create`. Use the `new` method to create an instance with `user_params`.

```ruby
    def create
        @user = User.new(user_params)
    end
```

If the user saves correctly, redirect to the books path and create a flash message = "Welcome [username] to BookIt, Usyou have successfully sign up". Otherwise, render new.

```ruby
        @user = User.new(user_params)
        if @user.save
            flash[:notice] = "Welcome #{@user.username} to BookIt, you have successfully sign up"
            redirect_to books_path
        else
            render 'new'
        end
```

Navigate to `pages/home.html.erb` and change the signup link to direct the usee to the signup page.

```html
    personal bookshelf. Join today by signing up.
  </p>
  <%= link_to "Sign up!", signup_path, class: "btn btn-success btn-lg home-button" %>
  <button class="home-button explore">
```

<div id="edit-users"></div>

## Edit Users

Navigate to the users controller, define a method called `edit`. Define another method called `update`.

```ruby
    def edit

    end

    def update

    end
```

In the `edit` method, use params to locate the specific user from the db.

```ruby
        @user = User.find(params[:id])
```

Under `views/users` create a file called `edit.html.erb` that renders the form partial.

```html
<h1 class="text-center">Edit your profile</h1>
<%= render 'form' %>
```

Navigate to` users/_form.html.erb`, let's change the submit button. Use a ternary operator to edit the content of the button to check if the user is a new user.

```html
<%= form.submit(@user.new_record? ? "Sign up" : "Edit profile" , class:"btn
btn-primary") %>
```

Navigate to `views/books/_form.html.erb` and repeat the same process for the creation/edit of books for the submit button

```html
<%= form.submit(@book.new_record? ? "Create book" : "Update book", class:"btn
btn-primary") %>
```

Navigate to `controllers/users_controller.rb`, in the update method, update the specified user. If update was successful, create a flash message and redirect to the books index page otherwise render the edit page.

```ruby
    def update
        @user = User.find(params[:id])

        if @user.update(user_params)
            flash[:notice] = "Your account information was succesfully updated"
            redirect_to books_path
        else
            render 'edit'
        end
    end
```

<div id="profile-page"></div>

## Profile page

In `users_controller.rb`, define a method called `show`. Using the `id` from params, get the user instance and store that in a instance varaible called `user`.

```ruby
    def show
        @user = User.find(params[:id])
    end
```

Create a `show.html.erb` file under users.

```html
<h1 class="text-center"><%= @user.username %> profile</h1>
<h2 class="text-center">Books</h2>
```

Under `views/books`, create another partial called` _book.html.erb`.

Copy the container element in `index.html.erb` and paste that into the book partial file. Iterate through 'books', this will be an alias.

```html
<div class="container">
  <div class="row"><% books.each do |book| %></div>
</div>
```

In `index.html.erb`, render the book partial file.

```html
<h1 style="font-size: 5rem; text-align: center" id="library">Library</h1>
<%= render 'book', books: @books %>
</div>
```

Navigate to `views/books/users_controller.rb`. Store the user books in an instance variable called `books`. In `uses/show.html.erb`, render the contents of the books created by the users.

```html
<h1 class="text-center"><%= @user.username %> profile</h1>
<%= render 'books/book', books: @user.books %>
```

<div id="user-index"></div>

## User Index

In `users_controller.rb`, define a method called `index`. Store all user instances in a variable instance.

```ruby
    def index
        @users = User.all
    end
```

Create an `index.html.erb` file in `views/users`.

```html
<h1 class="text-center">Users</h1>
```

Navigate to `layouts/_navigation.html.erb`, create a link to `users_path`

```html
<li class="nav-item">
  <a class="nav-link" href="#">Link</a>
</li>
<li class="nav-item"><%= link_to "Users", users_path, class: "nav-link" %></li>
```

Navigate to `books/index.html.erb`, copy the container element. Paste that into `users/index.html.erb`, configure the style and output for user instances.

```html
<div class="container text-center">
  <div class="row" style="margin: 0 0 5% 0">
    <% @users.each do |user| %>
    <div class="col-md-12" style="margin: 1% 0 ">
      <div class="card">
        <div class="card-header"><%= user.username %></div>
        <div class="card-body">
          <h5 class="card-title"><%= user.books.count %> books</h5>
          <p class="card-text"><%= user.books.count %></p>
          <button class="btn btn-primary">
            <%= link_to 'Edit Profile', edit_user_path(user), class:"link" %>
          </button>
          <button class="btn btn-secondary">
            <%= link_to 'Show Profile', user_path(user), class:"link" %>
          </button>
        </div>
      </div>
    </div>
    <% end %>
  </div>
</div>
```

:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!
