  # Ruby on Rails - Associations and Authentication Systems Part 1
  ## BookIt App Part 5

## Topics covered
  - ***<em>Associations</em>** - a type of relationship between two models (Ex: books and authors)
    - <em>one to many</em> indicates that one instance of a model has zero or more instances of another model (Ex: An author has many books)
    - <em>has one</em> indicates an instance model in relation to another model instance and no more (Ex: A book has one author. NOTE: In this case, we will make each book have exactly one author)
    - <em>many to many</em> indicates two model instances that have more than one relation with each other (EX: An author has many books. A book has many authors)
- ***<em>ERD or Entity Relationship Diagram</em>*** is a way to view the association between models.


### Creating a feature branch
- Use GitHub desktop or git to create a feature branch called create-users-table-model

### Configuring the Users table
1. In the terminal, create a migration file that will create a users table `rails generate migration create_users`
- In the migration file, include attributes username and email as well as timestamps

```ruby
    create_table :users do |t|
      t.string :username
      t.string :email
      t.timestamps
    end
```
2. `rails db:migrate`. Check your schema file to see your users table as been successfully created.

3. Navigate to the models folder, crate a users.rb file. 
   - Create a User class that inherits ApplicationRecord
```ruby 
class User < ApplicationRecord
end
```
- enter `rails c` to test out ORMs on the User table. 

4. Track/Commit your files to your feature branch and then merge to the main branch using Git or GitHub desktop.

### Add user validations to User class
1. In models/users.rb, add validation that will limit username creation by making each username unique

```ruby 
class User < ApplicationRecord
    validates :username, uniqueness: true
    validates :email, uniqueness: true
end
```
2. Make both username and email present in each creation.

```ruby
class User < ApplicationRecord
    validates :username, uniqueness: true, presence: true,
    validates :email, presence: true, uniqueness: true
end
```
3. Make the length of the username minimum 3 and maximum 25. Validate email to be a maximum of 25 characters.
```ruby
class User < ApplicationRecord
    validates :username, uniqueness: true, presence: true, length: {minimum: 3, maximum: 25}
    validates :email, presence: true, length: {maximum: 25}, uniqueness: true
end
```
4. To validate the email with the correct format we can user the built in ruby URI to do this.

```ruby
  validates :email, uniqueness: true, presence: true, length: { maximum: 25 }, format: { with: URI::MailTo::EMAIL_REGEXP }
```
### One to many association with User and Books
1. We need to add a user_id to the book's table so we can create a one to many associations. Let's generate migration file with the following `rails generate migration add_user_id_to_books`. Navigate to the migration file under migrate and add a new column to the books table called user_id that is of type int.
```ruby
class AddUserIdToBooks < ActiveRecord::Migration[6.1]
  def change
    add_column :books, :user_id, :int
  end
end
```

2. Then `rails db:migrate` to reflect these migrations towards the DB/schema.

3. Create a one to many association between Users and Books.

```ruby
class User < ApplicationRecord
  has_many :books
  validates :username, uniqueness: true, presence: true, length: { minimum: 3, maximum: 25 }
  validates :email, uniqueness: true, presence: true, length: { maximum: 25 }, format: { with: URI::MailTo::EMAIL_REGEXP }
end
```
```ruby
class Book < ApplicationRecord
    belongs_to :user
    validates :title, presence:true, length: {minimum: 2, maximum: 100}
    validates :description, presence:true, length: {minimum: 6, maximum: 200}
end
```
- In order to create a Book, book now needs a user_id due to `belongs_to` that creates this connection between the book record and user record.

- Navigate to controllers/books_controller.rb, under the create method. Assign each article that gets created to the first User in the db.

```ruby
        @book.image_path = "https://upload.wikimedia.org/wikipedia/commons/b/b9/No_Cover.jpg"
    end

    @book.user = User.first

    if @book.save
      flash[:notice] = "Book was created successfully"
      redirect_to @book
    else
```

- In the console, give each Book record that exists a user_id (right now, each user_id is nil for each Book record) with the following command `Book.update_all(user_id: User.first.id)`

### Show user info in books
1. Navigate to views/books/index.html.erb, let's creates a card header that show's the info of the user that belongs to that books.

```html
    <div class="col-md-3">
      <div class="card">
        <div class="card-header">
          By <%= book.user.username %>
        </div>
```

2. Repeat the same process in show.html.erb.
```html
<div class="container" >
  <div class="card" style="margin: 10% 25%">
    <div class="card-header">
      By <%= @book.user.username %>
    </div>
```

### Alter user object state before_save
1. Navigate to models/user.rb, use before_save to downcase the email characters before creation.
```ruby
class User < ApplicationRecord
  before_save { self.email = email.downcase }
  has_many :books
```

### Using Bcrpyt, add secure_password
1. Navigate to your gemfile and uncomment Bcrypt.

```ruby
# Use Active Model has_secure_password
gem 'bcrypt', '~> 3.1.7'
```
- run `bundle install`
2. include has_secure_password in your user model
3. Create a migration file to add password_digest to the users table `rails generate migration add_password_digest_to_users`
4. In the newly created migration file, create a password_digest column under the users table that is of type string.
```ruby
class AddPasswordDigestToUsers < ActiveRecord::Migration[6.1]
  def change
    add_column :users, :password_digest, :string
  end
end
```
5. Run `rails db:migrate` to reflect these newly added changes onto the db and schema file.

### New user signup form
1. Build a route that navigates the user to the sign up page. 
```ruby
  resources :books
  get 'signup', to: 'users#new'
```

2. Create a new file under controllers called users_controller.rb. Create a UsersController that inherits ApplicationController.

```ruby
class UsersController < ApplicationController
end
```
1. Create a folder under views called users. Create files _form.html.erb and new.html.erb under the users folder. In new.html.erb, render the partial form and also create an h1 element with content `Sign up to Book It`

```html 
<h1 class="text-center"> Sign up to Book It</h1>
<%= render 'form' %>
```
2. Navigate to _form.html.erb. Copy the form from books/_form.html.erb and paste the following. Edit the following labels and fields to corrrespond to user attributes.

```html
<div style="margin: 0 25%; width: 50%">
    <%= render 'shared/errors' %> <%= form_with(model: @user) do |form| %>
    <div class="mb-3">
      <%= form.label :username, "Username:", class:"form-label" %> <%= form.text_field :username, class:"form-control" %>
    </div>
    <div class="mb-3">
      <%= form.label :email, "Email:", class:"form-label" %> <%=
      form.text_field :email, class:"form-control" %>
    </div>
    <div class="mb-3">
      <%= form.label :password, "Password:", class:"form-label" %> <%=
      form.password_field :password, class:"form-control" %>
    </div>
    <%= form.submit "Sign up", class:"btn btn-primary" %> <% end %>
  </div>
```

3. An error will occur in shared/errors as we render new.html.erb due to being instance specific to book. Let's change this to make it be flexible to any instance. Navigate to _errors.hmtl.erb

```html
<% if obj.errors.any? %>
        <ul class="alert alert-warning alert-dismissible fade show" >
                <h3>The following errors prevented the book to be saved</h3>
                <% obj.errors.full_messages.each do |message| %>
                <li style="color:red; margin: 1%"><%= message %></li>
                <% end %>
                  <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
        </ul>
<% end %>
```
- In the h3 element, we specify the specific instance to be of type Book but that is not the case for User instances. Instead, let's replace this to specify the instance of the errors.
```html
                <h3>The following errors prevented the <%= obj.class %> to be saved</h3>
```
1. Navigate to books/_form.hmtl.erb and set obj to the @book instance when rendering errors.
```html
<div style="margin: 0 25%; width: 50%">
  <%= render 'shared/errors', obj: @book %> <%= form_with(model: @book) do |form| %>
  <div class="mb-3">
```
5. Repeat same process for users/_form.html.erb
```html
<div style="margin: 0 25%; width: 50%">
    <%= render 'shared/errors', obj: @user %> <%= form_with(model: @user) do |form| %>
    <div class="mb-3">
```
6. Create a resource route for users
```ruby
  get 'signup', to: 'users#new'
  resources :users, except: [:new]
end
```
### Create new users (back end)
1. In the books controller, create a private method named user_params. Use params to require the specific set of info: username, email, password.
```ruby
    def user_params 
        params.require(:user).permit(:username, :email, :password)
    end
```
2. In the create method, create an instance with user_params.
```ruby 
@user = User.new(user_params)
```
- User the save method as a conditional for when true, redirect to the books path and create a flash message = "Welcome [username] to BookIt, Usyou have successfully sign up". Otherwise, render new.

```ruby
        @user = User.new(user_params)
        if @user.save 
            flash[:notice] = "Welcome #{@user.username} to BookIt, you have successfully sign up"
            redirect_to books_path
        else 
            render 'new'
        end
```
3. Navigate to pages/home.html.erb and change the signup page to direct the use to the signup page

```html
    personal bookshelf. Join today by signing up.
  </p>

  <%= link_to "Sign up!", signup_path, class: "btn btn-success btn-lg home-button" %>
  
  <button class="home-button explore">
```

### Edit users 
1. Navigate to the users controller, define a class method called edit. Define another class method called update. 
```ruby
    def edit 

    end

    def update
        
    end
```

2. In the edit method, use the params to locate the specific user from the db.

```ruby
        @user = User.find(params[:id])
```
3. Under views/users create a file called edit.html.erb that renders the form partial.
```html
<h1 class="text-center">Edit your profile</h1>
<%= render 'form' %>
```
4. Navigate to users/_form.html.erb, let's change the submit button. Use a ternary operator to edit the content of the button to check if the user is a new user.

```html
    <%= form.submit(@user.new_record? ? "Sign up" : "Edit profile" , class:"btn btn-primary") %>

```

5. Navigate to views/books/_form.html.erb, and repeat the same process for the creation/edit of books for the submit button

```html
  <%= form.submit(@book.new_record? ? "Create book" : "Update book", class:"btn btn-primary") %> 
```
6. Navigate to controllers/users_controller.rb, in the update method, update the specified user. If update was successful, create a flash message and redirect to the books index page otherwise render the edit page. 

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

### Show user page and profile image
1. In users_controller.rb, define a show method. Using the id from params, get the user instance and store that in a variable instance called user.

```ruby
    def show 
        @user = User.find(params[:id])
    end
```

2. Create a show.html.erb file under users.

```html
<h1 class="text-center"><%= @user.username %> profile</h1>
<h2 class="text-center"><%=>Books</h2> 
```
3. Under views/books, create another partial called _book.html.erb.

- copy the container element in index.html.erb and paste that into the book partial file. In index.html.erb, render the book partial file.

```html
<h1 style="font-size: 5rem; text-align: center" id="library">Library</h1>
  <%= render 'book' %>
</div>
```
4. Navigate to views/books/users_controller.rb. Store the users books in an instance variable called books
5. In uses/show.html.erb, render the contents of the books created by the users.

```html
<h1 class="text-center"><%= @user.username %> profile</h1>
<h2>Books</h2> 
<%= render 'books/book' %>
```

### Add users index
1. In users_controller.rb, define an index method. Store all user instances in a variable instance.

```ruby
    def index 
        @users = User.all
    end
```
2. Create an index.html.erb file in views/users.
```html
<h1 class="text-center">Users</h1>
```
3. Navigate to layouts/_navigation.html.erb, create a link to users path
```html
        <li class="nav-item">
          <a class="nav-link" href="#">Link</a>
        </li>
        <li class="nav-item">
          <%= link_to "Users", users_path, class: "nav-link" %>
        </li>
```
4. Navigate to books/index.html.erb, copy the container element. Paste that into users/index.html.erb, configure the style and output for user instances.

```html
<div class="container text-center" >
    <div class="row" style="margin: 0 0 5% 0">
      <% @users.each do |user| %>
      <div class="col-md-12" style="margin: 1% 0 ">
        <div class="card">
          <div class="card-header">
            <%= user.username %>
          </div>
          <div class="card-body">
            <h5 class="card-title"><%= user.books.count %> books </h5>
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