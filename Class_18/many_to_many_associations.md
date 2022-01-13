  # Ruby on Rails - More many to many associations
  ## PetMe Social Media App Part 3 Rails 6

### Create many to many association with users and animals
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


3. Update the index.html.erb file to reflect how many animals each user has 

```html
              <p class="card-text"><%= user.animals.count %> animals</p>
```


```ruby 
rails active_storage:install
```
rails g devise:controllers users 
rails g devise User 

devise_for :users, controllers: { registrations: 'users/registrations' }

- model/user.rb
```ruby
  has_one_attached :avatar
```

- users/registrations_controller.rb
```ruby

  def update
    user = User.second.avatar.attach(image_params[:avatar])

    super
  end

  private 
  def image_params 
    params.require(:user).permit(:avatar, :email, :password, :password_confirmation, :current_password)
  end
```

- registrations/edit.html.erb
```html
<% if resource.avatar.attached? %>  
<%= image_tag resource.avatar, class: "width:25%"%>
<% end %>
  <%= f.label "Avatar" %>
  <%= f.file_field :avatar, class: "form-control"%>
```

rails g model Friendship user:references

class CreateFriendships < ActiveRecord::Migration[6.1]
  def change
    create_table :friendships do |t|
      t.references :user, null: false, foreign_key: true
      t.references :friend, references: :users, foreign_key: { to_table: :users}
      t.timestamps
    end
  end
end

class Friendship < ApplicationRecord
  belongs_to :user
  belongs_to :friend, class_name: 'User'
end


user model
  has_many :friendships
  has_many :friends, through: :friendships

  has_one_attached :avatar
end





websocket

rails g channel chatroom

routes mount

create messsage broadcast channel