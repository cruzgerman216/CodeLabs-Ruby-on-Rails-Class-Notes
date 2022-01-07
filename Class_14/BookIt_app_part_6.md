  # Ruby on Rails - Associations and Authentication Systems Part 2
  ## BookIt App Part 6

## Topics covered

### Cleanup Layout
1. Navigate to views/users/show.html.erb, add a edit profile button that navigates to the user's edit path.

```html
<h1 class="text-center"><%= @user.username %> profile</h1>
<div class="text-center">
    <%= link_to "Edit Profile", edit_user_path(@user), class: "btn btn primary mt-4 mb-4",
style:"border: 1px solid blue; color: blue;"%>
</div>
```
2. When updating the profile, instead of navigating to the books index page, redirect the user to their show page. Navigate to users_controller.rb, and update the update method.

```ruby
        if @user.update(user_params)
            flash[:notice] = "Your account information was succesfully updated"
            redirect_to @user
        else
```
3. In the books index page, make each username text into a link to redirect to the corresponding user's profile page. Navigate to users/index.html.erb.
```html
        <div class="card">
          <div class="card-header">
            By <%= link_to book.user.username, user_path(book.user)%>
          </div>
```

### Add Login Form
1. Navigate to routes.rb, create a get request to the route 'login'
```ruby
  resources :users, except: [:new]
  get 'login', to: 'sessions#new'
```
- create a post request to be sent when submitting login form
```ruby
  get 'login', to: 'sessions#new'
  post 'login', to:'sessions#create'
```
- create a delete request to be sent when logging out.
```ruby
  get 'login', to: 'sessions#new'
  post 'login', to:'sessions#create'
  delete 'logout', to: 'sessions#destroy'
end
```
2. Under controllers, create sessions_controller.rb and define te corresponding class.
3. Define methods new, create and destroy
```ruby
    def new 

    end

    def create 

    end

    def destroy
        
    end
```
4. Create a sessions folder under views. Create a file called new.html.erb.
5. Create form corresponding to the login path

```html
<h1 class="text-center">Login</h1>
<div style="margin: 0 25%; width: 50%">
    <%= form_with(scope: :sessions, url: login_path) do |form| %>
  </div>
```
- add fields email and password as well as a login submit button
````html
<h1 class="text-center">Login</h1>
<div style="margin: 0 25%; width: 50%">
    <%= form_with(scope: :sessions, url: login_path) do |form| %>
    <div class="mb-3">
      <%= form.label :email, "Email:", class:"form-label" %> <%=
      form.text_field :email, class:"form-control" %>
    </div>
    <div class="mb-3">
      <%= form.label :password, "Password:", class:"form-label" %> <%=
      form.password_field :password, class:"form-control" %>
    </div>
    <%= form.submit("Login", class:"btn btn-primary") %>
     <% end %>
  </div>
````

### Create and Destroy users
1. In the create method, use the params hash that contains the email from the request to search for the specific user.
```ruby
    def create 
        user = User.find_by(email: params[:session][:email].downcase)
    end
```
2. Use a condition that checks if the user has been found and that has been authenticated with the password. If true, redirect to user otherwise render the login page.
```ruby
    def create 
        user = User.find_by(email: params[:sessions][:email].downcase)
        if user && user.authenticate(params[:sessions][:password])
            flash[:notice] = "Logged in successfully"
            redirect_to user
        else
            flash[:notice] = "Unsuccessfull login"
            render 'new'
        end
    end
```
3. Use session to store the user id
```ruby
            session[:user_id] = user.id
            flash[:notice] = "Logged in successfully"
```

4. In the navigation bar, include a nav link to login.
```ruby
        <li class="nav-item">
          <%= link_to "Log in", login_path, class:"nav-link" %>
        </li>
```

5. In sessions_controller.rb, under destroy, set sessions of user_id to nil. Redirect to the root path.

```ruby
    def destroy
        session[:user_id] = nil
        flash[:notice] = "Logged out"
        redirect_to root_path
    end
```

6. In the navigation bar, include a nav link to logout. Specify the request to be a delete request.
```ruby
        <li class="nav-item">
          <%= link_to "Log out", logout_path, class:"nav-link", method: :delete %>
        </li>
```

### Authentication helper methods
1. Navigate to application_helper.rb, define a module called ApplicationModule. Define methods current_user and logged_in?
```ruby
module ApplicationHelper
    def current_user

    end

    def logged_in?
        
    end
end
```
2. In current_user, return user based on if sessions[:user_id] exists.
```ruby
        @current_user ||= User.find(session[:user_id]) if session[:user_id]
```
3. In logged_in?, return a boolean if current_user exists.
```ruby
    def logged_in?
        !!current_user
    end
```
4. Use logged_in? as a conditional for the navigation bar. If true, show log out, else show log in.

```html
        <% if logged_in? %>
        <li class="nav-item">
          <%= link_to "Log out", logout_path, class:"nav-link", method: :delete %>
        </li>
        <% else %>
        <li class="nav-item">
          <%= link_to "Log in", login_path, class:"nav-link" %>
        </li>
        <% end %>
```
5. In the else, add a sign up link.

```html
        <li class="nav-item">
          <%= link_to "Log in", login_path, class:"nav-link" %>
        </li>
        <li class="nav-item">
          <%= link_to "Sign Up", signup_path, class:"nav-link" %>
        </li>
```
6. Using current_user, create a link that will link to the current user's profile
```html
  <% if logged_in? %>
        <%= link_to @current_user.username, user_path(@current_user), class:"nav-link" %>
```
7. Navigate to users_controller.rb, upon creation of the user, store the user id to a session hash.
```ruby
        if @user.save 
            session[:user_id] = @user.id
```
8. Remove the redundancy of `@user = User.find(params[:id])` from methods edit, update, show.
9. Create a private method called set_user. Using the params hash to gather the id, find the user and store that in an instance variable.
```ruby
    def set_user 
        @user = User.find(params[:id])
    end
```
10. Use the before_action feature to execute set_user before actions show, edit and update.
```ruby
    before_action :set_user, only: [:show, :edit, :update]
```

### Adding controller methods as helper methods
1. In books_controller.rb, under the create method, every time we create a book, we associate the book with the first user of the db. Let's change this. We want to get the current_user but we know that current_user is also associated with the view. In rails 6, we can use `helpers` to access our helper methods from the controller.
```ruby
    @book.user = helpers.current_user
    if @book.save
```

2. When a logged in user goes to the home page, let's redirect him to the books index page. In pages_controller.rb, under home, use `helpers` to redirect the user if logged in.
```ruby
    def home
        redirect_to books_path if helpers.logged_in?
    end
```

### Restrict actions from UI
1. In views/books/_book.html.erb, let's restrict the edit and delete buttons to only the user that is logged in that associated with each book. Use the logged_in helper method and a conditional to do this.
```html
          <% if logged_in? && book.user == current_user %>
          <button class="btn btn-primary">
            <%= link_to 'Edit', edit_book_path(book), class:"link" %>
          </button>
          <button class="btn btn-danger">
            <%= link_to 'Delete', book_path(book), method: :delete, class:"link"
            %>
          </button>
          <% end %>
```
2. Repeat the same process for views/books/show.html.erb
```html
      <% if logged_in? && @book.user == current_user %>
      <button class="btn btn-primary">
        <%= link_to 'Edit', edit_book_path(@book), class:"link" %>
      </button>
      <button class="btn btn-danger">
        <%= link_to 'Delete', book_path(@book), method: :delete, class:"link" %>
      </button>
      <% end %>
```
3. Navigate to users/index.html.erb. We don't want a user to be able to edit anyone's profile other than their own. We will use logged_in? and a conditional to check to see if it is corresponding user in order to edit the profile.

```html
            <% if logged_in? && user == current_user %>
            <button class="btn btn-primary">
              <%= link_to 'Edit Profile', edit_user_path(user), class:"link" %>
            </button>
            <% end %>
```
4. Repeat the same process for show.html.erb under users.
```html
<% if logged_in? && @user == current_user %>
<div class="text-center">
  <%= link_to "Edit Profile", edit_user_path(@user), class: "btn btn primary
  mt-4 mb-4", style:"border: 1px solid blue; color: blue;"%>
</div>
```

### Modify Navigation based on helpers
1. Navigate to _navigation.html.erb, check to see if the user is logged in, if so allow them to create a book.
```html
            <% if logged_in? %>
            <li>
              <%= link_to "Create Book", new_book_path, class: "dropdown-item"%>
            </li>
            <%end%>
```

2. To allow easy access to edit/view profile, turn the navlink that holds the username into a dropdown.
```html

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
```
- Include dropdown items view profile and edit profile.
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
               <%= link_to "View Profile", user_path(@current_user), class:"dropdown-item" %>
            </li>
            <li>
              <%= link_to "Edit Profile", edit_user_path(@current_user), class: "dropdown-item"%>
            </li>
          </ul>
</li>
```

### Restrict actions at the controller level
1. Users whom aren't logged in can still access certain pages such as the create new book page, let's restrict this. Navigate to appliation_controller.rb, define a method require_user that redirects the user if the user is not logged in.
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

2. In books_controller.rb, use before_action to implement require_user to edit, new, create, update and destroy.
```ruby
    before_action :require_user, except: [:show, :index]
```
3. Users can still edit other users books. Let's prevent this. Define a private method called require_same_user that checks to see if the book association with a user is the same as current user.
```ruby
  def require_same_user
    if helpers.current_user != @book.user 
      flash[:notice] = "You can edit or delete your own books"
      redirect_to @book
    end
  end
```
- use before action to implement this methd to actions such as edit, destroy and update.
```ruby
  before_action :require_same_user, only: [ :update, :destroy, :edit]
```

4. Navigate to users_controller, use before_action and require_user in actions edit and update.

```ruby
    before_action :require_user, only: [:edit, :update]
```
5. Define a method called require_same_user. Redirect the current user if it is not their profile.
```ruby
    def require_same_user
        if helpers.current_user != @user 
          flash[:notice] = "You can only edit your own profile"
          redirect_to helpers.current_user
        end
      end
```
- implement before_action to edit and update
```ruby
    before_action :require_user, :require_same_user, only: [:edit, :update]
```

### Delete user
1. Navigate to _navigation.html.erb, under the profile dropdown, let's add an additional navlink that will delete the user profile.
```html
            <li>
              <%= link_to "Edit Profile", edit_user_path(@current_user), class: "dropdown-item"%>
            </li>
            <li >
              <%= link_to "Delete Profile", user_path(@current_user), class: "dropdown-item text-danger", method: :delete ,  data:{confirm: "are you sure"} %>
            </li>
```
2. Navigate to users_controller.rb, define a method called destroy. Delete the user using the destroy method and set the sessions of user_id to nil. Redirect to root path.
```ruby
    def destroy
        @user.destroy
        session[:user_id] = nil
        flash[:notice] = "Account and all associated books successfully deleted"
        redirect_to root_path
    end
```
- Implement require_user and require_same_user before the destroy action.

```ruby
    before_action :require_user, :require_same_user, only: [:edit, :update, :destroy]
```

3. Before deleting the user, let's destroy all of their books. Navigate to models/user.rb, use the dependent control to delete every book that's associated with the user once the user no longer exists.

```ruby
  before_save { self.email = email.downcase }
  has_many :books, dependent: :destroy
```

### Add admin functionality
1. In the terminal, enter the following `rails g migration add_admin_to_users`.
- In the migration file, add a admin column to the users table.
```ruby
  def change
    add_column :users, :admin, :boolean, default: false
  end
```
- `rails db:migrate`
- In the console, you can use the toggle method (or manually do it) to set admin to true for a paricular user.

### add admin access to views and controllers
1. In the navlink that contains the username, include text "(Admin)" if current user is an admin
```html
          data-bs-toggle="dropdown"
            aria-expanded="false"
          >
            <%= "(Admin)" if @current_user.admin? == true %> <%= @current_user.username %>
          </a>
```
2. Navigate to _books.html.erb, let's allow admins to also edit or delete books.
```html
          <% if logged_in? && book.user == current_user || current_user.admin? %>
          <button class="btn btn-primary">
            <%= link_to 'Edit', edit_book_path(book), class:"link" %>
          </button>
```
3. Navigate to books_controller, in require_same_user, we don't want to redirect the admin to the book page. 
```ruby
    if helpers.current_user != @book.user && !helpers.current_user.admin?
      flash[:notice] = "You can edit or delete your own books"
      redirect_to @book
    end
```

4. Navigate to books/show.html.erb. Allow user to edit or delete the book.
```html
      <% if logged_in? && (@book.user == current_user || current_user.admin?) %>
```

5. Navigate to users/users.index.html.erb. Allow admin to edit any profile.
```html
            <% if logged_in? && (user == current_user || current_user.admin?) %>
            <button class="btn btn-primary">
              <%= link_to 'Edit Profile', edit_user_path(user), class:"link" %>
            </button>
            <% end %>
```
6. Create a button element to delete a profile. Let's allow only admins to do this.

```html
            <% if logged_in? && current_user.admin? %>
            <button class="btn btn-danger">
              <%= link_to 'Delete Profile', user_path(user),style:"color:white; text-decoration: none", method: :delete, data: {confirm: "Are you sure you want to delete " + user.username + "?"} %>
            </button>
            <% end %>
```

7. We still have to allow the admin to delete the profile in the users controller. Navigate to users_controller.rb and under require_same_user, don't redirect the admin.

```ruby
        if helpers.current_user != @user && !helpers.current_user.admin?
          flash[:notice] = "You can only edit your own profile"
          redirect_to helpers.current_user
        end
```

- We have one bug, if we delete a profile that isn't ours, we log out. Use a conditional to prevent this.

```ruby
    def destroy
        @user.destroy
        session[:user_id] = nil if @user == current_user
```

### Production Deploy

1. Push your code to github and then to heroku
- `git push heroku master`
- `Heroku run rails db:migrate`
- You can associate each book with a user in the console or seed file or you can delete all books. I will delete all books to make it easy.
  - `heroku run rails c` -> `Book.destroy_all`
- `heroku restart`
- You can also give a user admin privileges in the heroku command line `User.find(1).toggle!(:admin)`

