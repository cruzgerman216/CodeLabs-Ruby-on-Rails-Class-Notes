      ___  __  ____  ____  ____  __    ____   __   ____  ____     __   ____  __
     / __)/  \(    \(  __)(  __)(  )  (  _ \ / _\ / ___)(  __)   / _\ (  _ \(  )
    ( (__(  O )) D ( ) _)  ) _)  )(    ) _ (/    \\___ \ ) _)   /    \ ) __/ )(
     \___)\__/(____/(____)(__)  (__)  (____/\_/\_/(____/(____)  \_/\_/(__)  (__)

![Rails API](https://robertobartolome.com/wp-content/uploads/2016/11/rsz_railsapi-1.jpg)

# Rails As An API - Part 3

### Table of Contents

- <a href="#FvS">Fat Model Thin Controller vs Plain Old Ruby Objects</a>

<div id="FvS"></div>

## Fat Model Thin Controller vs PORO

### Fat Model Thin Controller Demonstration

"Fat Model Thin Controller" is a pattern for when actions aren't compact with too much logic and instead are extracted into the model. Here's an example. I went ahead and refactored the `login` action from `controllers/user_controler.rb` to demonstrate this concept.

```ruby
      def login
        result = nil
        user = User.find_by(email: params[:email]).try(:authenticate, params[:password])
        result = OpenStruct.new({ success?: false, payload: nil, errors: convert_to_custom_error('User not found') }) if user.nil?
         result = OpenStruct.new({ success?: false, payload: nil, errors: convert_to_custom_error('Incorrect password') }) unless user && result
        token = user.generate_token!(@ip)
        result = OpenStruct.new({ success?: true, payload: { user: user, token: token }, errors: convert_to_custom_error(nil) }) if result == nil
        render_error(errors: 'User not authenticated', status: 401) and return unless result.success?
        payload = {
          user: UserBlueprint.render_as_hash(result.payload[:user], view: :login),
          token: TokenBlueprint.render_as_hash(result.payload[:token])
        }
        render_success(payload: payload)
      end
```

Notice how difficult this is to read. It's hard to manage and figure out what exactly is going on and what is dealing with the `login` logic. This is referred to as a `fat` controller action. There will be use cases where a controller method can be as big as 20 lines of code and there's not a way for it to extract it and that is fine. This is just a commmonly used pattern for readability purposes. Let's go ahead and extract this logic into a model to confirm to the `fat model thin controller` pattern.

```ruby
      def login
        result = User.login(params[:email], params[:password], @ip)
        render_error(errors: 'User not authenticated', status: 401) and return unless result.success?

        payload = {
          user: UserBlueprint.render_as_hash(result.payload[:user], view: :login),
          token: TokenBlueprint.render_as_hash(result.payload[:token])
        }
        render_success(payload: payload)
      end
```

That's a lot better. This is much more easier to read. But something's not right.

```ruby
result = User.login(params[:email], params[:password], @ip)
```

We have the User model handling the logic to the login but models shouldn't deal with the requests, rather, models are only associated with business logic (interacting with the database). We can instead direct our attention to services.

<img src="https://yourdiamondteacher.com/wp-content/uploads/2017/03/BUT-WAIT-THERES-MORE.png" >

#### Plain Old Ruby Objects

This is where the concept "Plain Old Ruby Objects" in services come into play. Where we extract this logic into a service instead of a model. A service is just a module or class that encapsulates the logic and makes services lean and readable. Instead of including the login logic in a model, let's extract it in a service. The name of the service or module will be `BaseApi::Auth`.

```ruby
        result = BaseApi::Auth.login(params[:email], params[:password], @ip)
```

Why is it called "Plain Old Ruby Objects"?

"A service object in Rails is a plain old Ruby object created for a specific business action. It usually contains code that doesn't fit in the model or view layer, e.g., actions via a third-party API like posting a tweet." We don't see this happening but behind the scenes of `BaseApi::Auth.login(params[:email], params[:password], @ip)`, this is returning an object.

If the method `login` deems to be a successful result, then the object should include two attributes such as:

- An attribute called `success?` that is set to `true`. This let's us know whether or not the logic was deemed successful.
- An attribute called `payload` that is set to some data, e.g, user information.

All in all, you get something like this:

```ruby
{
  success?: true,
  payload: 'some-data'
}
```

If the method `login` deems to be an error response, then the object should include two attributes such as:

- An attribute called `success?` that is set to `false`.
- An attribute called `error`.

The makeup of the object will look like the following:

```ruby
{
  success?: false,
  error: 'some-error'
}
```

How are these objects being created? We first have to start off with the `ServiceContract` module.

## Services

### Service Contract

Navigate to `services/service_contract.rb`. This is the `ServiceContract` module.

```ruby
# frozen_string_literal: true

# Standardizes what a service method should always return
module ServiceContract
  # Create the contractual return object
  def self.sign(success:, payload:, errors:)
    OpenStruct.new({ success?: success, payload: payload, errors: convert_to_custom_error(errors) })
  end

  def self.success(payload)
    OpenStruct.new({ success?: true, payload: payload, errors: convert_to_custom_error(nil) })
  end

  def self.error(errors)
    OpenStruct.new({ success?: false, payload: nil, errors: convert_to_custom_error(errors) })
  end

  # Convert a string, an array of strings, or a model's errors to a CustomError instance
  def self.convert_to_custom_error(errors)
    case errors.class.name.to_sym
    when :String, :NilClass
      custom_error = CustomError.new(errors)
    when :Array
      custom_error = CustomError.new
      errors.each { |e| custom_error.add(e) }
    else
      custom_error = CustomError.new.convert(errors)
    end
    custom_error
  end
end
```

This module provides methods that "standardizes" or adheres to the PORO pattern. We will be using these methods throughout our services. Let's start with the `sign` method.

### Sign Method

```ruby
  def self.sign(success:, payload:, errors:)
    OpenStruct.new({ success?: success, payload: payload, errors: convert_to_custom_error(errors) })
  end
```

The `sign` method defines three keyword parameters `success`, `payload` and `errors`. Notice how they are being included in a hash.

```ruby
{ success?: success, payload: payload, errors: convert_to_custom_error(errors) }
```

It's definitely a customizable hash that includes not only the payload but also the errors.

There's two questions that pop up. What is `convert_to_custom_error`? And what is `OpenStruct`?

### OpenStruct

Let's talk about `OpenStruct`. `OpenStruct` is a class that turns a hash into an object. In an OpenStruct object, you are free to add attributes. Here's an example.

```ruby
  user = OpenStruct.new({username: "johndoe123"})
  # returns an instance of OpenStruct with the attribute username.
  user.username = "amywinehouse123"
  puts user.username # prints amywinehouse123
  user.age = 5
  puts user.age # 5
```

### Struct vs OpenStruct vs Hash

Structs define classes as though they are templates with pre-defined attributes.

```ruby
  User = Struct.new(:name, :age)
  User.new("Jimmy", 34)
```

OpenStructs are different because although they are pre-define attributes, you can add more attributes such as

```ruby
  user = OpenStruct.new({username: "johndoe123"})
  user.age = 5
```

OpenStruct is a lot more flexible than hashes and structs. There may be cases where we do want to include attributes to the object.

### Convert To Custom Error Method

So we now know what `OpenStruct` is, let's go ahead and go back to our two parter and go over `convert_to_custom_error`.

```ruby
  def self.sign(success:, payload:, errors:)
    OpenStruct.new({ success?: success, payload: payload, errors: convert_to_custom_error(errors) })
  end
```

```ruby
  # Convert a string, an array of strings, or a model's errors to a CustomError instance
  def self.convert_to_custom_error(errors)
    case errors.class.name.to_sym
    when :String, :NilClass
      custom_error = CustomError.new(errors)
    when :Array
      custom_error = CustomError.new
      errors.each { |e| custom_error.add(e) }
    else
      custom_error = CustomError.new.convert(errors)
    end
    custom_error
  end
```

`convert_to_custom_error` uses a `case/when` block to structure an error depending what the class of the error is. Is it a string? A nil class class? Or an array of errors? Here, `CustomError` is being used. In `services/custom_error.rb`, is where this class exists and how it converts error messages. 

```ruby
# frozen_string_literal: true

# Helper class for handling error messages
#
# Methods: add(message), size, none?, all, as_sentence, convert
class CustomError
  def initialize(message = nil)
    @errors = []
    @errors << message unless message.blank?
  end

  def add(message)
    raise_if_no_message_provided(message)
    @errors << message
    self
  end

  def size
    @errors.size
  end

  def none?
    size.zero?
  end

  def all
    @errors
  end

  def as_sentence
    @errors.to_sentence.capitalize
  end

  def convert(errors)
    class_type = errors.class.to_s
    messages = []
    # Convert to a messages array based on the type passed in
    # If it's an active model's errors object
    messages = errors.full_messages if class_type == 'ActiveModel::Errors'
    # If it's an array
    messages = errors if class_type == 'Array'
    # If it's any active model object
    messages = errors&.errors&.full_messages || [] unless class_type.in?(%w[Array ActiveModel::Errors])

    # Loop over the messages, adding them to the @errors array
    messages.each { |error| @errors << error }
    self
  end

  private

  def raise_if_no_message_provided(message)
    raise 'Must provide usable error message!' if message.blank?
  end
end
```

### Success Method
Remember, to adhere to the PORO pattern, we must create a representation of a success object. That is what the `success` method provides.

```ruby
  def self.success(payload)
    OpenStruct.new({ success?: true, payload: payload, errors: convert_to_custom_error(nil) })
  end
```

### Error method
Same with an error representation. 
```ruby
  def self.error(errors)
    OpenStruct.new({ success?: false, payload: nil, errors: convert_to_custom_error(errors) })
  end
```

## Users Service 
<img src = "https://media.istockphoto.com/vectors/vector-comic-cartoon-of-tired-or-overworked-businessman-or-man-or-vector-id1205473265" width = "200px">

Okay that was a lot of jumping around. From "fat models thin controllers" to custom error messages and service contracts to setup PORO. Let's go ahead and go over each service file, starting with `services/base_api/users.rb` 

```ruby
# frozen_string_literal: true

module BaseApi
  module Users
    def self.new_user(params)
      user = User.new(
        first_name: params[:first_name],
        last_name: params[:last_name],
        email: params[:email],
        phone: params[:phone],
        password: params[:password],
        password_confirmation: params[:password_confirmation]
      )
      user.save!
      return ServiceContract.error('Error saving user.') unless user.valid?

      ServiceContract.success(user)
    end

    def self.destroy_user(user_id)
      user = User.find(user_id)
      return ServiceContract.error('Error deleting user') unless user.destroy

      ServiceContract.success(payload: user)
    end
  end
end
```

As you can see, the file consist of a module called `BaseApi` follow by a nested module called `Users`. Within the `Users` module, there are two methods that are extracting the logic from controller actions: `new_user` and `destroy_user`. 

```ruby
    def self.new_user(params)
      user = User.new(
        first_name: params[:first_name],
        last_name: params[:last_name],
        email: params[:email],
        phone: params[:phone],
        password: params[:password],
        password_confirmation: params[:password_confirmation]
      )
      user.save!
      return ServiceContract.error('Error saving user.') unless user.valid?

      ServiceContract.success(user)
    end
```

The `new_user` method will try to create a User record from params. Depending on whether it be successful or not, an object will be returned to represent the outcome (PORO). Same with `destroy_user`.

```ruby
    def self.destroy_user(user_id)
      user = User.find(user_id)
      return ServiceContract.error('Error deleting user') unless user.destroy

      ServiceContract.success(payload: user)
    end
```


## Roles Service 
Let's navigate to `services/base_api/roles.rb`. 

```ruby
# frozen_string_literal: true

module BaseApi
  # Container for methods related to handling roles on users
  module Roles
    # Handles determining if a user has a given role
    def role?(role)
      roles.any? { |r| r.slug.underscore.to_sym == role }
    end

    # Handles adding a role to a user
    def add_role(role)
      return ServiceContract.error('Role must be a symbol') if role.class.name.to_sym != :Symbol

      return ServiceContract.error("Role type '#{role}' is not available.") unless Role.valid_role?(role)

      target_role = Role.find_by_slug(role)
      roles << target_role unless roles.include?(target_role)
      ServiceContract.success(roles)
    end

    # Handles removing a role from a user
    def remove_role(role)
      return ServiceContract.error('Role must be a symbol') if role.class.name.to_sym != :Symbol

      return ServiceContract.error("Role type '#{role}' is not available.") unless Role.valid_role?(role)

      role = Role.find_by_slug(role)
      if user_roles.where(role: role).destroy_all
        ServiceContract.success(nil)
      else
        ServiceContract.error("Could not destroy #{role}")
      end
    end
  end
end
```

The Roles service isn't being used at the moment. But that doesn't mean we can't use it's methods to our benefit. In order to implement this service, we must first include it in our User model. 

```ruby
class User < ApplicationRecord
  include BaseApi::Roles
```

Now each user has access to these defined methods. Let's go through them. 

```ruby
    def role?(role)
      roles.any? { |r| r.slug.underscore.to_sym == role }
    end
```

The `role` returns true or false depending on whether the user already has that specific role. Where is `roles` being defined? Because this method is included in the user model, it has access to the models methods like `roles`, `tokens`, ect.

```ruby
    # Handles adding a role to a user
    def add_role(role)
      return ServiceContract.error('Role must be a symbol') if role.class.name.to_sym != :Symbol

      return ServiceContract.error("Role type '#{role}' is not available.") unless Role.valid_role?(role)

      target_role = Role.find_by_slug(role)
      roles << target_role unless roles.include?(target_role)
      ServiceContract.success(roles)
    end
```

Notice how `ServiceContract` is being called here, this allows us to follow the PORO pattern and we are expecting either an error or successful outcome from the `add_role` method.

So the first condition checks to see if `role` is a symbol and if not, return an object as a failed outcome. 

The next condition will go ahead and check to see if the user already has that role using the `valid_role?` method from the `Role` model class. Here is that code snippet below to remind you what `valid_role?` does. It downcases the parameter and then checks to see if there is a role with its same exact value.

*models/role.rb*
```ruby
  def self.valid_role?(role)
    stringified_role = role.to_s.downcase.underscore
    stringified_role.in?(available_roles)
  end
```

Back to `add_role` in `services/base_api/roles.rb` . We make our way to add the Role to the user once we check to see if the role actually exists. If so we can go ahead and add that role onto the user.

```ruby
      target_role = Role.find_by_slug(role)
      roles << target_role unless roles.include?(target_role)
      ServiceContract.success(roles)
```

### Wrap up 
There are several files we haven't fully explored but I urge you to go through the files yourselves and break it down sort of like how we did it. Use the descriptive methods and variables to your advantage of what is what. 

---

---

## *Additional Resource*s

### Documentation

- [Digest::SHA1](https://ruby-doc.org/stdlib-2.4.0/libdoc/digest/rdoc/Digest/SHA1.html)

### Videos

### Articles

- [Rails Service Objects: A Comprehensive Guide](https://www.toptal.com/ruby-on-rails/rails-service-objects-tutorial)
- [Refactoring Your Rails App With Service Objects](https://www.honeybadger.io/blog/refactor-ruby-rails-service-object/)
- [Using Service Objects in Ruby on Rails](https://blog.appsignal.com/2020/06/17/using-service-objects-in-ruby-on-rails.html)

### Other Resources

### Helpful Commands

---

:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!

