  # Ruby on Rails - More many to many associations and Uploading
  ## PetMe Social Media App Part 3 Rails 6

### Many to Many Association
1. `rails g scaffold Animal name:string`
2. `rails g model UserAnimal user_id:integer animal_id:integer`
3. `rails db:migrate`
4. Create association in all models. Navigate to models/user.rb.

```ruby
  has_many :user_animals
  has_many :animals, through: :user_animals
end
```
5. Navigate to models/animal.rb
```ruby
class Animal < ApplicationRecord
    has_many :user_animals
    has_many :owners, through: :user_animals
end
```
5. Navigate to models/user_animal.rb
```ruby
class UserAnimal < ApplicationRecord
    belongs_to :user
    belongs_to :animal
end
```

### Adjust controller and navigation bar
1. Update the animals_controller create method to be included add into under the current user's animals.

```ruby
    @animal = Animal.new(animal_params)

    respond_to do |format|
      if @animal.save
        current_user.animals << @animal
        format.html { redirect_to animal_url(@animal), notice: "Animal was successfully created." }
        format.json { render :show, status: :created, location: @animal }
      else
```
2. Update navigation bar in adding a navlink to add a new animal
```html
          <li class="nav-item">
            <%= link_to "Add Animal", new_animal_path, class: "nav-link"%>
          </li>
```

### Views 
1. Update the index.html.erb file to reflect how many animals each user has 

```html
              <p class="card-text"><%= user.animals.count %> animals</p>
```

### Image uploading
1. Installed active_storage with the following `rails active_storage:install` `rails db:migrate`
2. Navigate to User.rb and include the method `has_one_attached` follow by a name that references the attachment.
```ruby
  has_one_attached :avatar
end
```
3. Let's update the registration_controller as we need include the image attached in the record. Navigate to users/registrations_controller.rb
```ruby

  def update
    user = User.find(image_params[:user_id])
    user.avatar.attach(image_params[:avatar])
    super
  end

  private 
  def image_params 
    params.require(:user).permit(:avatar, :email, :password, :password_confirmation, :current_password, :user_id)
  end
```

4. In order to use the specify action, we need to update our routes.
```ruby
  root to: "pages#home"
  devise_for :users, controllers: { registrations: 'users/registrations' }
end
```

5. Navigate to registrations/edit.html.erb, create a conditional to check to see if the user contains an image, if so, display the image. Then create a form to upload an image.
```html
<% if resource.avatar.attached? %>  
<%= image_tag resource.avatar, class: "width:25%"%>
<% end %>
  <%= f.label "Avatar" %>
  <%= f.file_field :avatar, class: "form-control"%>
```


### Updating Navbar to show profile image
1. Navigate to _navigation.html.erb, include image tags if the attachedment exists.

```html
          </ul>
            <% if user_signed_in? && current_user.avatar.attached? %>
              <%= link_to user_path(current_user) do %>
                <%= image_tag current_user.avatar, style:
                "width:20%; border-radius: 50%", class: "float-right"%>
              <% end %>
            <% end %>
          <% end %>
```

### Image for all animals
1. Navigate to models/animal.rb, and include `has_one_attached` to each animal record.
```ruby
class Animal < ApplicationRecord
    has_many :user_animals
    has_many :owners, through: :user_animals

    has_one_attached :image
end
```
2. Navigate to animals_controller.rb. Update the animal params to include the image
```ruby
  def create
    @animal = Animal.new(name: animal_params[:name])
    @animal.image.attach(animal_params[:image])
```
```ruby
    def animal_params
      params.require(:animal).permit(:name, :image)
    end
```

3. Update the animals/_form_.html.erb file to include a file upload option

```html
 <%= form_with(model: animal) do |form| %>

    <%= form.label "Image" %>
    <%= form.file_field :image, class: "form-control"%>
```

4. Navigate to animals/show.html.erb to show and include a condition to show the image of the animal after successfully creating it.

```html
<% if @animal.image.attached? %> 
<%= image_tag @animal.image, class: "width:25%"%>
<% end %>
```

### Adjusting views to Animal images.
1. Navigate to user/show.html.erb, let's iterate through the animals and display their information. 

- NOTE: Keep in mind we are sending a lot of requests to the backend, this may slow down page performance and consume alot of resources.

```html

    <div > 
        <% if current_user != @user %>
        <% if current_user.friends.include?(@user)%>
        <%= form_with url: "/removefriend", method: :delete do |form| %> 
          <%= form.hidden_field :friend_id, value: @user.id %> 
          <%= form.submit "Unfiend" %>
          <% end %>
    <% else %>
            <%= form_with url: "/addfriend", method: :post do |form| %>
            <%= form.hidden_field :friend_id, value: @user.id %>
            <%= form.submit "Add" %>
          <% end %>
       <% end %>
    <% end %>
    <div class="row">

    <% @user.animals.each do |animal| %>
      <div class="col-md-4">
        <div class="card" >
          <% if animal.image.attached? %> 
          <%= image_tag animal.image, class:"card-img-top"%>
          <% else %> 
          <img width= "100%" src= "https://www.regionalsan.com/sites/main/files/imagecache/lightbox/main-images/vacant_placeholder.gif" />
          <% end %>
          <div class="card-body">
            <h5 class="card-title"><%= animal.name %></h5>
          </div>
        </div>
      </div>
    <% end %>
  </div>

```

websocket

rails g channel chatroom

routes mount

create messsage broadcast channel