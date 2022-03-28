# BookIt App Part 6 - Associations and Authentication Systems Part 2

### Table of Contents

- <a href="#cleanup-layout">Cleanup Layout</a>
- <a href="#login-logout">Login and Logout users</a>
- <a href="#helper-methods">Authentication Helper Methods</a>
- <a href="#helper-methods-controllers">Using Helper Methods in Controllers</a>
- <a href="#restrict-actions-from-ui">Restrict Actions from UI</a>
- <a href="#navigation-helpers">Modify Navigation Based on Helpers</a>
- <a href="#restrict-actions">Restrict Actions at the Controller Level</a>
- <a href="#delete-user">Delete User</a>
- <a href="#admin-functionality">Admin-functionality</a>
- <a href="#admin-access">Admin Access</a>

---

<div id="cleanup-layout"></div>

## Cleanup Layout

Navigate to `views/users/show.html.erb`, add a `edit profile` button that navigates to the user's edit path.

```html
<h1 class="text-center"><%= @user.username %> profile</h1>
<div class="text-center">
  <%= link_to "Edit Profile", edit_user_path(@user), class: "btn btn primary
  mt-4 mb-4", style:"border: 1px solid blue; color: blue;"%>
</div>
```

When updating the profile, instead of navigating to the books index page, redirect the user to their show page. Navigate to `users_controller.rb`, to update the `update` method.

```ruby
        if @user.update(user_params)
            flash[:notice] = "Your account information was succesfully updated"
            redirect_to @user
        else
```

Navigate to `users/index.html.erb`. In the books index page, make each username text into a link to redirect to the corresponding user's profile page. 

```html
<div class="card">
  <div class="card-header">
    By <%= link_to book.user.username, user_path(book.user)%>
  </div>
</div>
```

## Add Login Form

Navigate to `routes.rb`, create a `get` request to the route 'login'

```ruby
  resources :users, except: [:new]
  get 'login', to: 'sessions#new'
```

Create a `post` request to be sent when submitting login form

```ruby
  get 'login', to: 'sessions#new'
  post 'login', to:'sessions#create'
```

Create a `delete` request to be sent when logging out.

```ruby
  get 'login', to: 'sessions#new'
  post 'login', to:'sessions#create'
  delete 'logout', to: 'sessions#destroy'
end
```

Under controllers, create `sessions_controller.rb`.
Define methods new, create and destroy

```ruby
  class SessionsController < ApplicationController 
    def new

    end

    def create

    end

    def destroy

    end
  end
```

Create a `sessions` folder under views. Create a file called `new.html.erb`.
Create form corresponding to the login path.

```html
<h1 class="text-center">Login</h1>
<div style="margin: 0 25%; width: 50%">
  <%= form_with(scope: :sessions, url: login_path) do |form| %>
</div>
```

Add fields email and password as well as a login submit button

```html
<h1 class="text-center">Login</h1>
<div style="margin: 0 25%; width: 50%">
  <%= form_with(scope: :sessions, url: login_path) do |form| %>
  <div class="mb-3">
    <%= form.label :email, "Email:", class:"form-label" %> <%= form.text_field
    :email, class:"form-control" %>
  </div>
  <div class="mb-3">
    <%= form.label :password, "Password:", class:"form-label" %> <%=
    form.password_field :password, class:"form-control" %>
  </div>
  <%= form.submit("Login", class:"btn btn-primary") %> <% end %>
</div>
```

<div id="login-logout"></div>

## Login and Logout users

Navigate to `sessions_controller.rb`, in the `create` method, use the params hash that contains the email from the request to search for the specific user.

```ruby
    def create
        user = User.find_by(email: params[:session][:email])
    end
```

Use a condition that checks if the user has been found and that has been authenticated. If true, redirect to user's show page otherwise render the login page.

```ruby
    def create
        user = User.find_by(email: params[:sessions][:email])
        if user && user.authenticate(params[:sessions][:password])
            flash[:notice] = "Logged in successfully"
            redirect_to user
        else
            flash[:notice] = "Unsuccessfull login"
            render 'new'
        end
    end
```

Use session to store the user id

```ruby
            session[:user_id] = user.id
            flash[:notice] = "Logged in successfully"
```

In the navigation bar, include a nav link to login.

```ruby
        <li class="nav-item">
          <%= link_to "Log in", login_path, class:"nav-link" %>
        </li>
```

In `sessions_controller.rb`, under `destroy`, set sessions of user_id to nil. Redirect to the root path.

```ruby
    def destroy
        session[:user_id] = nil
        flash[:notice] = "Logged out"
        redirect_to root_path
    end
```

In the navigation bar, include a nav link to logout. Specify the request to be a delete request.

```ruby
        <li class="nav-item">
          <%= link_to "Log out", logout_path, class:"nav-link", method: :delete %>
        </li>
```

<divi id="helper-methods"></div>

## Authentication Helper Methods

Navigate to `application_helper.rb`, define a module called `ApplicationModule`. Define methods `current_user` and `logged_in?`

```ruby
module ApplicationHelper
    def current_user

    end

    def logged_in?

    end
end
```

In `current_user`, return user based on if sessions[:user_id] exists.

```ruby
        @current_user ||= User.find(session[:user_id]) if session[:user_id]
```

In logged_in?, return a boolean to check if current_user exists.

```ruby
    def logged_in?
        !!current_user
    end
```

Use `logged_in?` as a conditional for the navigation bar. If true, show log out, else show log in.

```html
<% if logged_in? %>
<li class="nav-item">
  <%= link_to "Log out", logout_path, class:"nav-link", method: :delete %>
</li>
<% else %>
<li class="nav-item"><%= link_to "Log in", login_path, class:"nav-link" %></li>
<% end %>
```

In the else, add a sign up link.

```html
<li class="nav-item"><%= link_to "Log in", login_path, class:"nav-link" %></li>
<li class="nav-item">
  <%= link_to "Sign Up", signup_path, class:"nav-link" %>
</li>
```

Using `current_user`, create a link that will link to the current user's profile

```html
<% if logged_in? %> <%= link_to @current_user.username,
user_path(@current_user), class:"nav-link" %>
```

Navigate to `users_controller.rb`, upon creation of the user, store the user id to a session hash.

```ruby
        if @user.save
            session[:user_id] = @user.id
```

Remove the redundancy of `@user = User.find(params[:id])` from methods edit, update, show.
Create a private method called set_user. Using the params hash to gather the id, find the user and store that in an instance variable.

```ruby
    def set_user
        @user = User.find(params[:id])
    end
```

Use the `before_action` feature to execute set_user before actions show, edit and update.

```ruby
    before_action :set_user, only: [:show, :edit, :update]
```

<div id="helper-methods-controllers"></div>

## Using Helper Methods in Controllers

In `books_controller.rb`, under the `create` method, every time we create a book, we associate the book with the first user of the db. Let's change this. We want to get the `current_user` but we know that `current_user` is also associated with the view. In rails 6, we can use `helpers` to access our helper methods from the controller.

```ruby
    @book.user = helpers.current_user
    if @book.save
```

When a logged in user goes to the home page, let's redirect him to the books index page. In `pages_controller.rb`, under home, use `helpers` to redirect the user if logged in.

```ruby
    def home
        redirect_to books_path if helpers.logged_in?
    end
```

<div id="restrict-actions-from-ui"></div>

## Restrict Actions from UI

In `views/books/_book.html.erb`, let's restrict the edit and delete buttons to only the user that is logged in that associated with each book. Use the `logged_in` helper method and a conditional to do this.

```html
<% if logged_in? && book.user == current_user %>
<button class="btn btn-primary">
  <%= link_to 'Edit', edit_book_path(book), class:"link" %>
</button>
<button class="btn btn-danger">
  <%= link_to 'Delete', book_path(book), method: :delete, class:"link" %>
</button>
<% end %>
```

Repeat the same process for `views/books/show.html.erb`

Navigate to `users/index.html.erb`. We don't want a user to be able to edit anyone's profile other than their own. We will use logged_in? and a conditional to check to see if it is corresponding user in order to edit the profile.

```html
<% if logged_in? && user == current_user %>
<button class="btn btn-primary">
  <%= link_to 'Edit Profile', edit_user_path(user), class:"link" %>
</button>
<% end %>
```

Repeat the same process for `show.html.erb` under users.

```html
<% if logged_in? && @user == current_user %>
<div class="text-center">
  <%= link_to "Edit Profile", edit_user_path(@user), class: "btn btn primary
  mt-4 mb-4", style:"border: 1px solid blue; color: blue;"%>
</div>
```

<div id="navigation-helpers"></div>

## Modify Navigation Based on Helpers

Navigate to `_navigation.html.erb`, check to see if the user is logged in, if so allow them to create a book.

```html
<% if logged_in? %>
<li><%= link_to "Create Book", new_book_path, class: "dropdown-item"%></li>
<%end%>
```

To allow easy access to edit/view profile, turn the navlink that holds the username into a dropdown.

````html
```html
<li class="nav-item dropdown">
  <a
    class="nav-link dropdown-toggle"
    href="#"
    id="navbarDropdown"
    role="button"
    data-bs-toggle="dropdown"
    aria-expanded="false"
  >
    <%= @current_user.username %>
  </a>
</li>
````

Include dropdown items view profile and edit profile.

```html
<li class="nav-item dropdown">
  <a
    class="nav-link dropdown-toggle"
    href="#"
    id="navbarDropdown"
    role="button"
    data-bs-toggle="dropdown"
    aria-expanded="false"
  >
    <%= @current_user.username %>
  </a>
  <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
    <li>
      <%= link_to "View Profile", user_path(@current_user),
      class:"dropdown-item" %>
    </li>
    <li>
      <%= link_to "Edit Profile", edit_user_path(@current_user), class:
      "dropdown-item"%>
    </li>
  </ul>
</li>
```

<div id="restrict-actions"></div>

## Restrict actions at the controller level

Users whom aren't logged in can still access certain pages such as the create new book page, let's restrict this. Navigate to appliation_controller.rb, define a method require_user that redirects the user if the user is not logged in.

```ruby
class ApplicationController < ActionController::Base
    def require_user
        if !helpers.logged_in? #Again, this is rails 6 implementation
            flash[:notice] = "You must be logged in to perform this action"
            redirect_to login_path
        end
    end
end
```

In `books_controller.rb`, use before_action to implement require_user to edit, new, create, update and destroy.

```ruby
    before_action :require_user, except: [:show, :index]
```

Users can still edit other users books. Let's prevent this. Define a private method called require_same_user that checks to see if the book association with a user is the same as current user.

```ruby
  def require_same_user
    if helpers.current_user != @book.user
      flash[:notice] = "You can edit or delete your own books"
      redirect_to @book
    end
  end
```

Use before action to implement this methd to actions such as edit, destroy and update.

```ruby
  before_action :require_same_user, only: [ :update, :destroy, :edit]
```

 Navigate to `users_controller`, use `before_action` and `require_user` in actions edit and update.

```ruby
    before_action :require_user, only: [:edit, :update]
```

Define a method called `require_same_user`. Redirect the current user if it is not their profile.

```ruby
    def require_same_user
        if helpers.current_user != @user
          flash[:notice] = "You can only edit your own profile"
          redirect_to helpers.current_user
        end
      end
```

Implement `before_action` to edit and update

```ruby
    before_action :require_user, :require_same_user, only: [:edit, :update]
```

<div id="delete-user"></div>

## Delete User

Navigate to _navigation.html.erb, under the profile dropdown, let's add an additional navlink that will delete the user profile.

```html
<li>
  <%= link_to "Edit Profile", edit_user_path(@current_user), class:
  "dropdown-item"%>
</li>
<li>
  <%= link_to "Delete Profile", user_path(@current_user), class: "dropdown-item
  text-danger", method: :delete , data:{confirm: "are you sure"} %>
</li>
```

Navigate to `users_controller.rb`, define a method called destroy. Delete the user using the destroy method and set the sessions of user_id to nil. Redirect to root path.

```ruby
    def destroy
        @user.destroy
        session[:user_id] = nil
        flash[:notice] = "Account and all associated books successfully deleted"
        redirect_to root_path
    end
```

Implement `require_user` and `require_same_user` before the destroy action.

```ruby
    before_action :require_user, :require_same_user, only: [:edit, :update, :destroy]
```

Before deleting the user, let's destroy all of their books. Navigate to models/user.rb, use the dependent control to delete every book that's associated with the user once the user no longer exists.

```ruby
  has_many :books, dependent: :destroy
```

<div id="admin-functionality"></div>

## Admin Functionality

In the terminal, enter the following `rails g migration add_admin_to_users`.

- In the migration file, add a admin column to the users table.

```ruby
  def change
    add_column :users, :admin, :boolean, default: false
  end
```

Run `rails db:migrate`
In the console, you can use the toggle method (or manually do it) to set admin to true for a paricular user.

<div id="admin-access"></div>

## Add Admin Access to Views and Controllers

In the navlink that contains the username, include text "(Admin)" if current user is an admin

```html
          data-bs-toggle="dropdown"
            aria-expanded="false"
          >
            <%= "(Admin)" if @current_user.admin? == true %> <%= @current_user.username %>
          </a>
```

Navigate to _books.html.erb, let's allow admins to also edit or delete books.

```html
<% if logged_in? && book.user == current_user || current_user.admin? %>
<button class="btn btn-primary">
  <%= link_to 'Edit', edit_book_path(book), class:"link" %>
</button>
```

 Navigate to `books_controller`, in `require_same_user`, we don't want to redirect the admin to the book page.

```ruby
    if helpers.current_user != @book.user && !helpers.current_user.admin?
      flash[:notice] = "You can edit or delete your own books"
      redirect_to @book
    end
```

Navigate to `books/show.html.erb`. Allow user to edit or delete the book.

```html
<% if logged_in? && (@book.user == current_user || current_user.admin?) %>
```

Navigate to `users/users.index.html.erb`. Allow admin to edit any profile.

```html
<% if logged_in? && (user == current_user || current_user.admin?) %>
<button class="btn btn-primary">
  <%= link_to 'Edit Profile', edit_user_path(user), class:"link" %>
</button>
<% end %>
```

Create a button element to delete a profile. Let's allow only admins to do this.

```html
<% if logged_in? && current_user.admin? %>
<button class="btn btn-danger">
  <%= link_to 'Delete Profile', user_path(user),style:"color:white;
  text-decoration: none", method: :delete, data: {confirm: "Are you sure you
  want to delete " + user.username + "?"} %>
</button>
<% end %>
```

We still have to allow the admin to delete the profile in the users controller. Navigate to `users_controller.rb` and under require_same_user, don't redirect the admin.

```ruby
        if helpers.current_user != @user && !helpers.current_user.admin?
          flash[:notice] = "You can only edit your own profile"
          redirect_to helpers.current_user
        end
```

We have one bug, if we delete a profile that isn't ours, we log out. Use a conditional to prevent this.

```ruby
    def destroy
        @user.destroy
        session[:user_id] = nil if @user == current_user
```

## Production Deploy

Push your code to github and then to heroku

1. `git push heroku master`
2. `Heroku run rails db:migrate`
- You can also give a user admin privileges in the heroku command line `User.find(1).toggle!(:admin)`


## *Additional Resource*s

### Videos

- [How to Use Rails Helper Methods ](https://www.youtube.com/watch?v=jASswaHkCbk&ab_channel=JesusCastello)

### Articles

- [How to Use Rails Helpers](https://www.rubyguides.com/2020/01/rails-helpers/)
  <br>

### Other Resources
- [Log in, log out](https://3rd-edition.railstutorial.org/book/log_in_log_out)
- [Updating, showing, and deleting users](https://3rd-edition.railstutorial.org/book/updating_and_deleting_users)

---

:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!
