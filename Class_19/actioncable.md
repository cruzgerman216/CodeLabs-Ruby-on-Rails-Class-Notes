  # Ruby on Rails - More many to many associations and Uploading
  ## PetMe Social Media App Part 4 Rails 6

### Setup channel and actioncable
1. Use the rails generator to generate files that configure the channel implementation of your application by entering `rails g channel chatroom`
2. Navigate to routes.rb and include the following in your routes 

```ruby 
  mount ActionCable.server, at: '/cable'
```

### Setting up chatroom and chatroomuser models
1. Let's create an association between users and chatrooms. A chatroom can have many users and a user can have many chatrooms. We need to generate a model for both chatroom and chatroomusers to create the association. I will also create the view and routes for just chatroom because chatroom will include methods to get the current users chatrooms and show a particular chatroom
2.  ```rails g resource Chatroom``` We don't need any attributes from chatroom. Just a table to create the association between users and chatroomusers. 
3.  ```rails g model ChatroomUser chatroom_id:integer user_id:integer```
4.  ```rails db:migrate```

5. Navigate to models/user.rb

```ruby
  has_many :chatroom_users
  has_many :chatrooms, through: :chatroom_users
  has_one_attached :avatar
end
```
6. Navigate to models/chatroom 

```ruby
class Chatroom < ApplicationRecord
    has_many :chatroom_users
    has_many :users, through: :chatroom_users
end
```
7. Navigate to chatroom_user.rb
```ruby
class ChatroomUser < ApplicationRecord
    belongs_to :user 
    belongs_to :chatroom
end
```

### Setting up Messages 
1. Messages can be derived in multiple ways. We know that each message belongs to a particular user and a channel. It also contains a body of information that the user enters. I will generate Message as a resource because perhaps in the future, I like to do things with the messages in the views.
2. ```rails g resource Message body:string user_id:integer chatroom_id:integer```
3. ```rails db:Migrate```
4. Navigate to models/message.rb

```ruby
class Message < ApplicationRecord
    belongs_to :user 
    belongs_to :chatroom
end
```
5. Navigate to models/user.rb

```ruby

  has_many :messages
end
```
6. Navigate to models/chatroom.rb

```ruby
    has_many :messages
end
```

### Setting up chatroom controller and routes
1. In chatrooms_controller.rb, let's define three methods show, index and create. 

- The show action will display all messages from the current chatroom
- The index action will display all known chatrooms that belong to current user. 
- The create action will allow a user to create a chatroom between two users. 

```ruby 
class ChatroomsController < ApplicationController
    # display chatroom
    def show 

    end
    # get all chatrooms from user
    def index 

    end

    # create chatroom
    def create 
        
    end
end
```

- create views for show and index under views/chatrooms

2. Setup routes for chatroom

```ruby 
  resources :chatrooms, only: [:create, :show]
  resources :user, only: [:show, :index] do 
    resources :chatrooms, only: [:index]
  end
```

- Show and create should only be the viable actions in ChatroomsController 
```ruby 
resources :chatrooms, only: [:create, :show]
```
- Because getting all of the current user's chatrooms, we can include nested routes for only the index action from ChatroomsController. 

```ruby
  resources :user, only: [:show, :index] do 
    # users/:id/chatrooms
    resources :chatrooms, only: [:index]
  end
```

### Creating a chatroom + Adding "Send message" button
1. Navigate to user/show.html.erb. We will includea form that will send a post request to `chatrooms_path`. This will execute the create method in the ChatroomsController. I will make it so that you have to be friends with the user to send him a message.

```html
        <% if current_user.friends.include?(@user)%>
          <%= form_with url: "/removefriend", method: :delete do |form| %> 
          <%= form.hidden_field :friend_id, value: @user.id %> 
          <%= form.submit "Unfiend", class:"btn btn-secondary" %>
          <% end %>

          <%= form_with url: chatrooms_path, method: "post" do |form| %>
          <%= form.hidden_field :friend_id, value: @user.id %>
          <%= form.submit "Send a message" %>
          <% end %>
      <% else %>
```

2. Navigate to chatrooms_controller.rb. In the create action, let's create a chatroom and add it under both the friend and the current users list of chatrooms. Then redirect to that specific chatroom path.

```ruby 
    def create 
        chatroom = Chatroom.create()
        friend = User.find(params[:friend_id])
        current_user.chatrooms << chatroom
        friend.chatrooms << chatroom
        redirect_to chatroom_path(chatroom)
    end
```

### Updating navbar to link to inbox of chatrooms
1. Navigate to _navigation.html.erb. Include a link to current user chatrooms.

```html
          <li class="nav-item">
            <%= link_to "Inbox", user_chatrooms_path(current_user), class: "nav-link"%>
          </li>
```

- `user_chatrooms_path` is a special helper method created by the nested routes in routes.rb. Rails automatically creates that for us because of this nested route. It redirects to `user/:id/chatrooms` and executes the index method in ChatroomsController
```ruby 
  resources :user, only: [:show, :index] do 
    resources :chatrooms, only: [:index]
  end
```
2. Navigate to chatrooms_controller.rb. In index, store all current user's chatrooms in an instance variable. We will use this instance variable to display all chatrooms in the view.

```ruby
    def index 
        @chatrooms = current_user.chatrooms
    end
```

3. Navigate to chatrooms/index.html.erb. Iterate through chatrooms and then print out the users contained in each chatroom. For now, we only have logic for two users joining a chatroom but maybe in the future, we like to add more than two users in a chatroom. Let's be sure to not display the current user while printing out the friend in each chatroom.

```html
<div class="container">
    <div class="text-center">
        <h1>Inbox</h1>
    </div>

    <% @chatrooms.each do |chatroom| %>
        <%= link_to chatroom_path(chatroom) do %>
        <div style="border: 1px solid black; width: 100%;">
            <% chatroom.users.each do |user| %>
            <%= user.email if user != current_user %>
            <% end %>
        </div>
        <% end %>
    <% end %>
</div>
```

### Chatroom View and Messages
1. Navigate to chatrooms/show.html.erb. Let's add html and css to display a chatbox style.

```html
<h1 class="text-center">Chatroom</h1>

<div class="w-50" style="margin: 0 25%">
  <div
    style="
      height: 50vh;
      border: 1px solid black;
      overflow-y: scroll;
      padding: 1%;
    "
  >
    <p>Email: Message</p>
  </div>
  <div>
    <textarea style="width: 100%"></textarea>
    <button class="btn btn-primary" style="margin: 0 40%">Send message</button>
  </div>
</div>
```

2. Under the chatbox, create a form that sends a post request to messages controller. We want to also attach a input from the user.
      <%= form_for(@message) do |form| %> 
        <%= form.text_field :body, class: "form-control" %>
        <%= hidden_field_tag 'chatroom_id', @chatroom.id  %>
        <%= form.submit "Send Message", class: "btn btn-success" %>
      <% end %>

- In order for the messages controller to be aware of the chatroom, we must send a hidden fild tage to do this. 
- In order for the form to use @message, we need to declare it in our show method

```ruby
class ChatroomsController < ApplicationController
    def show 
        @chatroom = Chatroom.find(params[:id])
        @message = Message.new
    end
  ```

3. Navigate to messages_controller.rb. Define the creatte and messages_params methods. Use the build method to create a nested relationship between the user and message record.

```ruby
    def create 
        @message = current_user.messages.build(body: message_params[:body], chatroom_id: params[:chatroom_id])
        if @message.save
            @chatroom = Chatroom.find(params[:chatroom_id])
            redirect_to chatroom_path(@chatroom)
        end
    end

    private 

    def message_params 
        params.require(:message).permit(:body)
    end
```

4. Navigate to chatrooms/show.html.erb. Iterate through messages and display the email of the user belonging to the message as well as the body of the message.

```html
    <% @chatroom.messages.each do |message| %>
    <p><b><%=message.user.email%></b>: <%= message.body %> </p>
    <% end %>
```

### Implementing channel feature
1. Navigate to channels/chatroom_channel.rb. Uncomment the stream_from comment, change the string to "chatroom_1". The string can be anything but be aware this name will be used in other parts of our application.

```ruby
class ChatroomChannel < ApplicationCable::Channel
  def subscribed
    stream_from "chatroom_channel"
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end
end
```
2. Navigate to javascript/channels/chatroom_channel.js. Let's console log in the connected method `Connected to chatroom channel`

```js
consumer.subscriptions.create("ChatroomChannel", {
  connected() {
    console.log("Connected to chatroom channel")
  },
```

3. In the recieved method, we are going to console log the data parameter being passed in

```js
  received(data) {
    console.log(data)
  }
```

4. Navigate to messages_controller.rb. We need to use ActionCable to broadcast this message to the stream instead of having to relod the page. Anyone else who is on the stream will also get the messages instantaneously.

```ruby
        if @message.save
            ActionCable.server.broadcast("chatroom_channel", { body: @message.body, user: @message.user.email})
        end
```

- Test this out in the web application. You should see whatever you entered to be log to the dev tools console and also your email. Open a new browser, log in as the other user in the chatroom. You can see, he also gets the message. 

### Adding messages to the DOM
1. Navigate to chatroom/show.html.erb. Now that we have this instant message working. Let's now do some DOM manipulation to add this message to the box container. We must first give the box an id to access it.

```html
  <div
    style="
      height: 50vh;
      border: 1px solid black;
      overflow-y: scroll;
      padding: 1%;
    "
    id="message-container"
  >
```

2. Navigate to javascript/channels/chatroom_channel.rb. Use javascript to add elements to message-container.

```js
  received(data) {
    document.getElementById("message-container").innerHTML += `<p><b>${data.user.email}</b>: ${data.body}</p>`
  }
```

3. Let's also enable the button. Right now, it get's disabled after submitting. Navigate to show.html.erb, and give the submit button an id of chatroom-button.
```html
    <%= form.submit "Send Message", class: "btn btn-success", id: "chatroom-button" %>
  <% end %>
```


4. In chatroom_chanell.rb, get the specific button from the DOM using getElementById. Enable the button by setting disable to true.

```js
  received(data) {
    document.getElementById('chatroom-button').disabled = false;
    document.getElementById("message-container").innerHTML += `<p><b>${data.user}</b>: ${data.body}</p>`;
  }
```