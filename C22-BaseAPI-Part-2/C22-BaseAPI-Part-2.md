      ___  __  ____  ____  ____  __    ____   __   ____  ____     __   ____  __
     / __)/  \(    \(  __)(  __)(  )  (  _ \ / _\ / ___)(  __)   / _\ (  _ \(  )
    ( (__(  O )) D ( ) _)  ) _)  )(    ) _ (/    \\___ \ ) _)   /    \ ) __/ )(
     \___)\__/(____/(____)(__)  (__)  (____/\_/\_/(____/(____)  \_/\_/(__)  (__)

![Rails API](https://miro.medium.com/max/700/1*Piijn-cC4d0_K3meyzBfJg.jpeg)

# Rails as an API - Part 2

### Table of Contents

- <a href="#routes">Routes</a>
- <a href="#controllers">Controllers</a>

<div id="routes"></div>

## Routes

You're familiar with routes right? The requests the user makes and in return, the controllers handle these requests. I figured you knew. Let's navigate to `config/routes.rb`.

What the? What is that?

<img src="https://theactivevoice.com/wp-content/uploads/2016/08/ThinkstockPhotos-178062396-1024x683.jpg" width="200px">

```ruby
  scope :monitoring do
    # Sidekiq Basic Auth from routes on production environment
    if Rails.env.production?
      Sidekiq::Web.use Rack::Auth::Basic do |username, password|
        ActiveSupport::SecurityUtils.secure_compare(::Digest::SHA256.hexdigest(username),
                                                    ::Digest::SHA256.hexdigest(Rails.application.credentials.sidekiq[:auth_username])) &
          ActiveSupport::SecurityUtils.secure_compare(::Digest::SHA256.hexdigest(password),
                                                      ::Digest::SHA256.hexdigest(Rails.application.credentials.sidekiq[:auth_password]))
      end
    end
    mount Sidekiq::Web, at: '/sidekiq'
  end
```

This is a Sidekiq configuration block of code. `scope :monitoring` is prefixing the path such as `/monitoring`. This code block allows for basic authentication in the production environment, which will enable the web UI of Sidekiq. Sidekiq comes with a [web application](https://github.com/mperham/sidekiq/wiki/Monitoring#web-ui) that can display the current state of a Sidekiq installation. This a way to protect access to this information.

```ruby
  namespace :api, defaults: { format: :json } do
    namespace :v1 do
      namespace :users do
        post :login
        delete :logout
        get :me
        post :create
      end
    end
  end
```

Namespacing is just another way of prexing the defined paths such as

- logging in a user by sending a post request to `/api/v1/users/login`
- logging out a user by sending a delete request to `/api/v1/users/logout`
- returning the user by sending a get request to `/api/v1/users/me`
- signing up a user by sending a post request to `/api/v1/users/create`

The default response all comes in the format of json (JavaScript Object Notation), a fast and lightweight way to send data.

Notice the paths all container the prefixes `/api/v1`. This signifies versioning which allows the API to continue to expand into other versions and development but still maintain the same resource of paths. Here's an example of what a defined version 2 set of routes may look like

```ruby
Rails.application.routes.draw do
  concern :api_base do
    resources :posts do
      resources :comments
    end
  end

  namespace :v2 do
    concerns :api_base
  end

  namespace :v1 do
    concerns :api_base
  end
end
```

<div id ="controllers"></div>

## Controllers

### Application Controller

Now, let's get to the meat of the API and move onto controllers. So far we've talk about the layout of the database, the models and associations as well as the defined routes users can request. Now how do we deal with these requests? Let's navigate to `controllers/application_controller.rb`. This class isn't inherited by any other controller class in the application but is a great baseline. Let's disect this before moving onto version one controllers.

```ruby
class ApplicationController < ActionController::API
  include ActionController::HttpAuthentication::Token::ControllerMethods

  before_action :authenticate

  def authenticate
    authenticate_token || render_unauthorized
  end

  def authenticate_token
    authenticate_with_http_token do |token, options|
      @current_user = User.find_by(token: token)
      @current_user
    end
  end

  def render_unauthorized
    logger.debug "*** UNAUTHORIZED REQUEST: '#{request.env['HTTP_AUTHORIZATION']}' ***"
    self.headers['WWW-Authenticate'] = 'Token realm="Application"'
    render json: {error:"Bad credentials"}, status: 401
  end
end
```

Notice how `ApplicationController` is inheriting from `ActionController::API`, this is a lightweight version of `ActionController::Base`, the module we inherit when developing a FS Rails app as mentioned in the docs.

```
API Controller is a lightweight version of ActionController::Base, created for applications that don't require all functionalities that a complete Rails controller provides, allowing you to create controllers with just the features that you need for API only applications.

An API Controller is different from a normal controller in the sense that by default it doesn't include a number of features that are usually required by browser access only: layouts and templates rendering, flash, assets, and so on. This makes the entire controller stack thinner, suitable for API applications. It doesn't mean you won't have such features if you need them: they're all available for you to include in your application, they're just not part of the default API controller stack.
```

```ruby
  include ActionController::HttpAuthentication::Token::ControllerMethods
```

These methods provided by the line above is usually included by inheriting `ActionController::Base` but because we are inheriting from a downsize version, we must include this line for authorization.

```ruby
before_action :authenticate

def authenticate
    authenticate_token || render_unauthorized
end

```

Here we have defined two instances of whether a token will be authenticated or render `unauthorized`. Let's take a look at the first method defined in the very same class.

```ruby
  def authenticate_token
    authenticate_with_http_token do |token, options|
      @current_user = User.find_by(token: token)
      @current_user
    end
  end
```

Rails handles tokens from the Authorization header in any of the following formats with the `authenticate_with_http_token` method. It automatically checks the authorization header for a token and passes it in as an argument. It will then return any user that has this token.

```ruby
  def render_unauthorized
    logger.debug "*** UNAUTHORIZED REQUEST: '#{request.env['HTTP_AUTHORIZATION']}' ***"
    self.headers['WWW-Authenticate'] = 'Token realm="Application"'
    render json: {error:"Bad credentials"}, status: 401
  end
```

Using Rails logger allows us to display some useful information in the console. You can also use `puts` to log information but logger offers a few features that can be helpful.

In the next line of code `self` which refers to the instance of the controller that is handling the said request. In this case you can attach a response-type header such as `WWW-Authenticate` and set it to what is referred to as a realm. As said [here](https://www.geeksforgeeks.org/http-headers-www-authenticate/), "It serves as a support for various authentication mechanisms which are important to control access to pages and other resources as well". In this case, we are protecting the application from those who don't have valid credentials.

### Application Controller Version 1

<img src="https://cdn.vox-cdn.com/thumbor/mFiywP9BUHDC8AIRBDYJvXdfQiA=/1400x1050/filters:format(jpeg)/cdn.vox-cdn.com/uploads/chorus_asset/file/23265504/Spider_Man_meme.jpg" width="400px">

Don't get confused by file names, `controllers/application_controller.rb` acts as a layout and is just "there". While in version 1 of our application, `controllers/api/v1/application_controller.rb` includes the class our other controllers from v1 will inherit.

It looks very similiar to the original `application_controller` but has a couple more methods and also updated methods.

For example, the `authenticate_token` method.

```ruby
  def authenticate_token
    @ip = request.remote_ip || "unknown"
    authenticate_with_http_token do |token, _options|
      @token = Token.find_by(value: token)
      if @token.nil?
        render_unauthorized
      else
        if @token.expiry.after?(DateTime.now) && @token.revocation_date.blank?
          @current_user = @token.user
          @current_user
        else
          render_unauthorized
        end
      end
    end
  end
```

This method will check to see if the token exists and also checks to see if the token has been expired. If the token is valid and the token hasn't expired, return that specific user. Otherwise, call `render_unauthorized`.

---

### ensure_required_params

```ruby
  def ensure_required_params(required_params, passed_in_params)
    required_params = required_params.map(&:to_sym)
    passed_in_params = passed_in_params.keys.map(&:to_sym)

    remainder = required_params - passed_in_params

    count = remainder.size
    errors = remainder

    render json: { message: "Missing required parameters: #{errors.to_sentence}" }, status: :bad_request and return unless count.zero?
  end
```

`ensure_required_params` is a way to respond if the params are missing any require parameters. If so, then the response will include a message `"Missing required paramseters"` follow by the specific missing params.

---

### render_error

`render_error` structures an error response by including keys `success`, `errors`, and `status`.

```ruby
  def render_error(errors:, status: :internal_server_error)
    render json: {
      success: false,
      errors: errors,
    }, status: status
  end
```

`success` is automatically set to false. This is because when our Front End takes this response, we can include a condition that checks whether or not their request had failed.

`internval server error` is the status of the response, specifically a 500 error which means that something went wrong with the server.

### render_success

```ruby
  def render_success(payload:, status: :ok)
    render json: {
      success: true,
      payload: payload,
    }, status: status
  end
```

`render_success` structures a succcessful response.

## User Controller

Navigate to `controllers/api/v1/users_controller.rb`.

```ruby
# frozen_string_literal: true

module Api
  module V1
    # Handles endpoints related to users
    class UsersController < Api::V1::ApplicationController
      skip_before_action :authenticate, only: %i[login create]

      def login
        result = BaseApi::Auth.login(params[:email], params[:password], @ip)
        render_error(errors: 'User not authenticated', status: 401) and return unless result.success?

        payload = {
          user: UserBlueprint.render_as_hash(result.payload[:user], view: :login),
          token: TokenBlueprint.render_as_hash(result.payload[:token])
        }
        render_success(payload: payload)
      end

      def logout
        result = BaseApi::Auth.logout(@current_user, @token)
        render_error(errors: 'There was a problem logging out', status: :unprocessable_entity) and return unless result.success?

        render_success(payload: 'You have been logged out')
      end

      def create
        result = BaseApi::Users.new_user(params)
        render_error(errors: 'There was a problem creating a new user', status: 400) and return unless result.success?
        payload = {
          user: UserBlueprint.render_as_hash(result.payload, view: :normal)
        }
        #  TODO: Invite user to accept invitation via registered email
        render_success(payload: payload)
      end

      def me
        render_success(payload: UserBlueprint.render_as_hash(@current_user))
      end

      def validate_invitation
        user = User.invite_token_is(params[:invitation_token]).invite_not_expired.first

        render_success(payload: { validated: false }) and return if user.nil?
        render_success(payload: { validated: true })
      end
    end
  end
end
```

This controller involves a pattern that extracts logic out of the actions into services. It also extracts logic by using commonly used methods such as `render_success` and `render_error` are from `Api::V1::ApplicationController`. The reason for that is for readability purposes. It's a `size that fits all` type of situation for using `render_success` and `render_error` but also again, extracts logic so it's easier to read.

```ruby
module Api
  module V1
    # Handles endpoints related to users
    class UsersController < Api::V1::ApplicationController
    end
  end
end
```

```ruby
      skip_before_action :authenticate, only: %i[login create]
```

Here we are going to invoke `authenticate` for each action except `login` and `create`. In order for the user to fetch their information and logout, they must first be authenticated (login).

### login action

Let's dive deep into the logic action and talk about the api's pattern in controllers. So when the user sends a post to `domain-name/api/v1/users/login`, the following method will execute.

```ruby
      def login
        result = BaseApi::Auth.login(params[:email], params[:password], @ip)
        render_error(errors: 'User not authenticated', status: 401) and return unless result.success?

        payload = {
          user: UserBlueprint.render_as_hash(result.payload[:user], view: :login),
          token: TokenBlueprint.render_as_hash(result.payload[:token])
        }
        render_success(payload: payload)
      end
```

We're use to authenticating the user within this action here but we're extracted that into a module(service) called `BaseApi::Auth`. We aren't yet concerned with what login does at the moment but we know that it will handle authenticating the user. We've been doing that the entire time during our programming journey, using methods that we didn't create. This one is no difference. It takes in three parameters, email and password as well as `@ip`. `@ip` should be regarded as `reuest.remote_ip` which provides the ip address of the user.

`@ip` is used to create the token when logging in.

```ruby
        render_error(errors: 'User not authenticated', status: 401) and return unless result.success?
```

The next line of code will render errors and return if the result was unsucessful. Otherwise, `render_success` will be called.

```ruby
        payload = {
          user: UserBlueprint.render_as_hash(result.payload[:user], view: :login),
          token: TokenBlueprint.render_as_hash(result.payload[:token])
        }
```

A payload is information that is to be included with the response. When everything checks out, we can return a successful response.

```ruby
        payload = {
          user: UserBlueprint.render_as_hash(result.payload[:user], view: :login),
          token: TokenBlueprint.render_as_hash(result.payload[:token])
        }
        render_success(payload: payload)
```

We see something very interesting which is `UserBlueprint` and `TokenBlueprint`. These are classes from files in `blueprints` and inherits from `Blueprinter::Base` which is provided by the `Blueprinter` gem. Blueprinter allows us to serialize our data.

#### Serializing data with Blueprinter

Let's have a quick lesson on Blueprinter.

Please navigate to `blueprints/user_blueprint.rb`. Blueprinter takes hashes and serializes them into JSON objects. Blueprinter relies on the idea of predefining different ways of structuring the info being sent back in the response. Let's say in some responses, you don't want to include timestamps and other responses you do, so you are "predefining output for data in different contexts". A class that inherits from `BluePrinter::Base` looks like this. By inheriting from `BluePrinter::Base`, we get methods such as `identifier`, `fields` and more.

```ruby
# frozen_string_literal: true

# Defines the JSON blueprint for the User model
class UserBlueprint < Blueprinter::Base
  identifier :id
  fields :first_name, :last_name, :name, :email

  view :login do
    association :token, blueprint: TokenBlueprint do |user, _options|
      user.tokens.last
    end
  end

  view :normal do
    fields :first_name, :last_name, :name, :email, :phone
  end
end
```

`identifier` is, like its name, the identifier of the JSON object, in this case, is the id of the user record.

`fields` on the other hand are default attributes to be included in the JSON object. So every JSON object should include the records `first_name`, `last_name`, `name` and `email`. Notice how `phone` is left out. That's our choice to include it as default info. ALSO, notice `name`, that isn't an attribute from the Users table?

That is actually a method defined by the user's model class. This means we can include methods defined in our model class that will transform the data in some sort of way.

_models/user.rb_

```ruby
  def name
    "#{first_name} #{last_name}"
  end
```

Let's get back to blueprinter and see an example of how this works. We need to invoke a class method called `render_as_hash` from `UserBlueprint`. This will take a hash, for example, `result.payload[:user]` which will be a hash of the user info such as

```ruby
  {
    id: 1,
    first_name: "John",
    .
    .
    .
  }
```

This will serialize our data depending what the default view when we execute the line below

```ruby
UserBlueprint.render_as_hash(result.payload[:user])
```

Because this is the default 'view', this will return:

```json
{
            "id": 1,
            "email": "test@test.com",
            "first_name": "John",
            "last_name": "Doe",
            "name": "John Doe"
},
```

Remember, the default view includes attributes `first_name`, `last_name`, `name` and `email`. Don't forget about the identifier which is the `id` of the record.

Let's say that we want to include the token of the user in the JSON object. We can do the following

```ruby
  view :login do
    association :token, blueprint: TokenBlueprint do |user, _options|
      user.tokens.last
    end
  end
```

We would then need to include an additional argument to serialize the data such as

```ruby
UserBlueprint.render_as_hash(result.payload[:user], view: :login)
```

This will return the following:

```JSON
{
            "id": 1,
            "email": "test@test.com",
            "first_name": "John",
            "last_name": "Doe",
            "name": "John Doe",
            "token": {
                "id": 8,
                "created_at": "2022-04-13T23:40:55.866Z",
                "expiry": "2022-04-20T23:40:55.859Z",
                "ip": null,
                "revocation_date": null,
                "updated_at": "2022-04-13T23:40:55.866Z",
                "user_id": 1,
                "value": "40910ff7846064fcd91db36f3da6ed23a13e0585"
            }
  },
```

### Logout Action

<img src="https://media.istockphoto.com/photos/logout-button-picture-id177335293" width="200px">

```ruby
      def logout
        result = BaseApi::Auth.logout(@current_user, @token)
        render_error(errors: 'There was a problem logging out', status: :unprocessable_entity) and return unless result.success?

        render_success(payload: 'You have been logged out')
      end
```

```ruby
result = BaseApi::Auth.logout(@current_user, @token)
```

The logic to logout the user comes from the define method `logout` from the `BaseApi::Auth` module. This module comes from `services/base_api/auth.rb`. This method takes in two arguments: `@current_user` and `@token`. Where are these variables being defined? Don't forget, the authenticate method executes before the logout action and this is where `@current_user` and `@token` are being defined.

_controllers/api/v1/application_controller_

```ruby
  def authenticate
    authenticate_token || render_unauthorized
  end

  def authenticate_token
    @ip = request.remote_ip || "unknown"
    authenticate_with_http_token do |token, _options|

      @token = Token.find_by(value: token)
      if @token.nil?
        render_unauthorized
      else
        if @token.expiry.after?(DateTime.now) && @token.revocation_date.blank?
          @current_user = @token.user
          @current_user
        else
          render_unauthorized
        end
      end
    end
  end
```

Let's get back to the `logout` method.

```ruby
      def logout
        result = BaseApi::Auth.logout(@current_user, @token)
        render_error(errors: 'There was a problem logging out', status: :unprocessable_entity) and return unless result.success?

        render_success(payload: 'You have been logged out')
      end
```

If the `result` is unsuccessful, we can call `render_error` and return. Otherwise, we give a successful response.

### Create Action

The `create` action, as you guess it, is dealing with the logic that creates a user record if valid.

```ruby
      def create
        result = BaseApi::Users.new_user(params)
        render_error(errors: 'There was a problem creating a new user', status: 400) and return unless result.success?
        payload = {
          user: UserBlueprint.render_as_hash(result.payload, view: :normal)
        }
        #  TODO: Invite user to accept invitation via registered email
        render_success(payload: payload)
      end
```

```ruby
BaseApi::Users.new_user(params)
```

The `new_user` method is define in the `BaseApi::Users` module from `services/base_api/users.rb`. The rest of logic follows the same pattern as the previous actions we've gone through. "If we run into an error, call `render_error` and return otherwise call `render_success`.

### Me Action

```ruby
      def me
        render_success(payload: UserBlueprint.render_as_hash(@current_user, view: :normal))
      end
```

The `me` method returns `current_user` info. Remember, the user has to be authenticated first before being able to request this information.

```ruby
skip_before_action :authenticate, only: %i[login create]
```

### Validate Invitation Action

We won't worry about `validate_invitation` but it's nice to know what it's purpose is and how it works.

```ruby
      def validate_invitation
        user = User.invite_token_is(params[:invitation_token]).invite_not_expired.first

        render_success(payload: { validated: false }) and return if user.nil?
        render_success(payload: { validated: true })
      end
```

When a user is sent an invitation email, the user will be able to send a request back with the `invitation_token` that is handled by the `validate_invitation` action.

```ruby
User.invite_token_is(params[:invitation_token])
```

`invite_token_is` is a scope method from the `User` model, that performs a custom query in finding the user who holds this `invitation_token`

_models/user.rb_

```ruby
scope :invite_token_is, ->(invitation_token) { where(invitation_token: invitation_token) }
```

```ruby
User.invite_token_is(params[:invitation_token]).invite_not_expired
```

When `invite_token_is` returns this specific user, `invite_not_expired` is called upon that user. `invite_not_expired` will check to see if that token is expired. If not, this method will return an array with this user, hence why `first` is called at the end.

```ruby
        user = User.invite_token_is(params[:invitation_token]).invite_not_expired.first
```

---

---

## *Additional Resource*s

### Documentation

- [Sidekiq web ui](https://github.com/mperham/sidekiq/wiki/Monitoring#web-ui)
- [Introducing json](https://www.json.org/json-en.html)
- [ActionController:HTTPAuthentication::Token::ControllersMethod](https://api.rubyonrails.org/v6.1.0/classes/ActionController/HttpAuthentication/Token/ControllerMethods.html)
- [WWW-Authenticate](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/WWW-Authenticate)
- [ActionController::HttpAuthentication::Token](https://api.rubyonrails.org/classes/ActionController/HttpAuthentication/Token.html)
- [Blueprinter github](https://github.com/procore/blueprinter)

### Videos

- [Basic Authentication in Five Minutes](https://www.youtube.com/watch?v=rhi1eIjSbvk&ab_channel=OktaDev)

### Articles

- [How to add Basic HTTP Authentication to Sidekiq UI mounted on a Rails application](https://www.aloucaslabs.com/miniposts/how-to-add-basic-http-authentication-to-sidekiq-ui-mounted-on-a-rails-application)
- [Scope vs namespaces in Rails 5](https://devblast.com/b/rails-5-routes-scope-vs-namespace)
- [Versioning a Rails API](https://chriskottom.com/blog/2017/04/versioning-a-rails-api/)
- [Token Based Authentication in Rails](https://www.pluralsight.com/blog/tutorials/token-based-authentication-rails)
- [Authentication vs Authorization](https://medium.datadriveninvestor.com/authentication-vs-authorization-716fea914d55)
- [Rails Logger and Rails Logging Best Practices](https://stackify.com/rails-logger-and-rails-logging-best-practices/)
- [HTTP headers | WWW-Authenticate](https://www.geeksforgeeks.org/http-headers-www-authenticate/)
- [500 Internal Server Error](https://www.lifewire.com/500-internal-server-error-explained-2622938#:~:text=The%20500%20Internal%20Server%20Error,what%20the%20exact%20problem%20is.)
- [Serializing Data in Rails](https://medium.com/@stvik2/serializing-data-in-rails-a46cb16e0c2d#:~:text=What%20is%20serialization%3F,front%20end%20can%20read%20it.)

### Other Resources

- [What is the realm in basic authentication](https://stackoverflow.com/questions/12701085/what-is-the-realm-in-basic-authentication)

### Helpful Commands

---

:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!

```

```

```

```
