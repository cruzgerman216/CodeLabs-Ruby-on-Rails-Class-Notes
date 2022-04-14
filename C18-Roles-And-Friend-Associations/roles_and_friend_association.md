  # Ruby on Rails - Roles and friend associations
  ## PetMe Social Media App Part 2 Rails 6

## Topics Covered
- Creating Roles with many to many associations
- How to make friends with self Referential Associations 

### Role and UserRole association
1. In order to make roles, we will allow a user to have one or more roles. In a use case scenario, a user me be a regular sign in user or a guest user. Or an owner of a specific service of the application. Either way, we can be dynamic and allow admin to create any roles that see fit. Let's generate two tables to create many to many associate between roles and users. A role can have many users. A user can have many roles. 

- `rails g model Role slug:string`
- `rails g model UserRole user_id:integer role_id:integer`
- `rails db:migrate`

2. Let's now add the association between User, Role and UserRole tables. Navigate to models/user.rb and create a has many association between Role and UserRole.

```ruby
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
  has_many :user_roles
  has_many :roles, through: :user_roles
end
```

3. Navigate to models/role.rb and create an association between User and UserRole 

```ruby 
class Role < ApplicationRecord
    has_many :user_roles 
    has_many :users, through: :user_roles
end
```

4. Navigate to models/user_role.rb and use belongs_to to attach each user_role instance to a user and role instnace.

```ruby
class UserRole < ApplicationRecord
    belongs_to :user 
    belongs_to :role
end
```

### Seed File
- Let's create a seed file to test out admin and regular users.

```ruby
Role.create(slug: "regular")
Role.create(slug: "admin")
user = User.create(email: "admin@admin.com", password: "password", password_confirmation:"password")
user.roles << Role.second
regular = User.create(email: "regular@regular.com", password: "password", password_confirmation:"password")
regular.roles << Role.first
regular2 = User.create(email: "regular2@regular2.com", password: "password", password_confirmation:"password")
regular.roles << Role.first
```
- `rails db:seed`

### Updating Navbar Admin view
1. Navigate to the _navigation.html.erb, and create a conditional that will include content "logged in as admin" if the current user is an admin. 

```html
          <li class="nav-item">
            <b class="nav-link">
            <%= "(Logged in as Admin)" if admin?%>
            </b>
          </li>
```

- In this case, we don't have a helper method called admin, we have to create one to see if the current user is admin. This takes a little bit of logic to solved this issue as now a user can have multiple roles. Navigate to application_helper.rb to create this functionality.

```ruby
module ApplicationHelper
    def admin? 
        current_user.roles.each do |role| 
            return true if role.slug == "admin"
        end
        return false
    end
end
```
- log in using the user we created from the seed file and test this out.

### User controller + actions/views show and index
- Devise gives us a great amount of controllers but doesn't give us a controller to handle a possible index page or show page. Let's generate a new user controller using `rails g controller User show index`

- this command will generate a user_controller.rb file, define actions show and index, views file

- Include user as a resource in routes 
```ruby
Rails.application.routes.draw do
  resources :user, only: [:show, :index]
  root to: "pages#home"
  devise_for :users
end
```

1. In the navbar, create navlink to users index page
```html 
          <% if user_signed_in? %>
          <li class="nav-item">
            <%= link_to "Users", user_index_path, class: "nav-link"%>
          </li>
          <li class="nav-item">
            <%= link_to "Edit Profile", edit_user_registration_path(current_user), class: "nav-link"%>
          </li>
```

2. In user_controller.rb, let's create an instance value that has all existing users in the user table.

```ruby
  def index
    @users = User.all
  end
```

3. Navigate to user/index.html.erb. Use the each method to iterate through @users to display user information. Included in the following 
- user email
- link to profile page
- static text regarding animal count the user has.
```html
<div class="container">
<h1 class="text-center">Users</h1>
    <div class="row">
    <% @users.each do |user| %>
    <div class="col-md-4">
        <div class="card" style="width: 18rem;">
            <img src="https://montevista.greatheartsamerica.org/wp-content/uploads/sites/2/2016/11/default-placeholder.png" class="card-img-top" alt="...">
            <div class="card-body">
              <h5 class="card-title"><%= user.email %></h5>
              <p class="card-text">Animals 0</p>
              <%= link_to "Profile", user_path(user), class: "btn btn-primary" %>
            </div>
          </div>
    </div>
    <% end %> 
    </div>
</div>
```

4. Navigate to user_controller, under show, use params of id to locate the specific user in the database with the corresponding id.

```ruby
  def show
    @user = User.find(params[:id])
  end
```

5. Navigate to views/user/show.html.erb, update the file to display user information. 

```html
<div class="container">
    <h1 class="text-center"><%= @user.email %>'s profile</h1>
</div>
```
### Adding friends with self-referential association
1. Self-referential association is a technique used to join two of the same models to itself and compares in some way. We can do this with a friendship association.

- generate a table called friendship `rails g model Friendship user_id:integer friend_id:integer`
- `rails db:migrate`

2. Navigate to models/user.rb. Let's create a has many association through friendships

```ruby
  has_many :friendships 
  has_many :friends, through: :friendships
end
```
- Here `has_many :friends` allows us to give us back the users in which we compare during our association. `through: :friendships` compares `has_many :friendships`, seeking every table that has the user_id of the current user instance's id. This will give us all friend_ids however, rails still need friendships to return some type of instance to do this.

3. navigate to models/frienship.rb. Let's create the logic to create a self-referential association.

```ruby
class Friendship < ApplicationRecord
    belongs_to :user
    belongs_to :friend, class_name: 'User'
end
```
- Here we give the attach of this friendship model to user and friend. Friend refers to an id that belongs to the User class. Now that we know we aren't returning just anything from `has_many :friends` from the user model, it will return all friends associated with the friend_id that is the user class.

- test this out in the console

### Creating a friendship controller for adding friends
1. Generate a friendship controller 
```rails g controller Friendship```
2. Define a method called add_friend
```ruby
class FriendshipController < ApplicationController
    def add_friend
        
    end
end
```
3. In routes.rb, create a path that allows a post request to the add_friend action.

```ruby
    post 'addfriend' => 'friendship#add_friend'
```

4. Navigate to index.html.erb and let's create a form that allows us to send this post request. Be sure to include a hidden tag to include the user id.
```html
              <%= form_with url: "/addfriend", method: :post do |form| %>
              <%= form.hidden_field :friend_id, value: user.id %>
              <%= form.submit "Add" %>
              <% end %>
```
- create a validation that the button does not show for the current user.
```html
              <% if current_user != user %>
                <%= form_with url: "/addfriend", method: :post do |form| %>
                <%= form.hidden_field :friend_id, value: user.id %>
                <%= form.submit "Add" %>
                <% end %>
               <% end %>
```
- Let's use a condition to display different buttons for when the user is friends with the current user 

```html
              <% if current_user != user %>
                <% if current_user.friends.include?(user)%>
                <button class="btn btn-secondary">Unfriend</button>
                <% else %>
                        <%= form_with url: "/addfriend", method: :post do |form| %>
                        <%= form.hidden_field :friend_id, value: user.id %>
                        <%= form.submit "Add" %>
                        <% end %>
                <% end %>
               <% end %>


```

5. Navigate to friendship_controller.rb, use params to locate the specified user. Use `<<` to add the specified friend to current user's friend's list.

```ruby
    def add_friend
        friend = User.find(params[:friend_id])
        current_user.friends << friend
        friend.friends << current_user
        flash[:notice] = "Added #{friend.email} as a friend"
        redirect_to user_path(friend)
    end
```

### Unfriending a user
1. Navigate to user/show.html.erb, create a view the will display a button with content unfriend otherwise, add. 

```html
        <% if current_user.friends.include?(@user) %>
        <button class="btn btn-secondary">Unfriend</button>
        <% else %>
                <%= form_with url: "/addfriend", method: :post do |form| %>
                <%= form.hidden_field :friend_id, value: @user.id %>
                <%= form.submit "Add" %>
               <% end %>
        <% end %>
```

1. Update the routes to include a delete request.
```ruby
  delete 'removefriend' => 'friendship#remove_friend'
```

3. Define a remove_friend method in the friendship_controller.rb file.

4. In the remove_friend action, remove friendships between current user and friend.

```ruby
    def remove_friend 
        Friendship.find_by(user_id: current_user.id, friend_id: params[:friend_id]).destroy
        Friendship.find_by(user_id: params[:friend_id], friend_id: current_user.id).destroy
        redirect_to user_index_path
    end
```

5. Let's update the view in index.html.erb

```html 
              <% if current_user != user %>
                  <% if current_user.friends.include?(user)%>
                  <%= form_with url: "/removefriend", method: :delete do |form| %> 
                    <%= form.hidden_field :friend_id, value: user.id %> 
                    <%= form.submit "Unfiend" %>
                    <% end %>
              <% else %>
                      <%= form_with url: "/addfriend", method: :post do |form| %>
                      <%= form.hidden_field :friend_id, value: user.id %>
                      <%= form.submit "Add" %>
                    <% end %>
                 <% end %>
              <% end %>
```

- repeat the same process with show.hmtl.erb

- Include the amount of friends in each show and index 

```html
              <p class="card-text"><%= user.friends.count %> friends</p>
```
