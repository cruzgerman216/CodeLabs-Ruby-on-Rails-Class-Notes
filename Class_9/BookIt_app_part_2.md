  # Ruby on Rails - CRUD Operations part 2
  ## BookIt App Part 2

## Topics covered
###### View
  - **<em>forms</em>** [resource](https://guides.rubyonrails.org/form_helpers.html)
    - scope - table name
    - url - path to submit to
    - instance variable errors - errors when creating the instance. We can display a message if we have any errors
###### Controller
  - **<em>permit parameters</em>** [resource](https://apidock.com/rails/ActionController/Parameters/permit)
    - strong parameters to limit/whitelist certain paramters
  - **<em>flash</em>** [resource](https://api.rubyonrails.org/classes/ActionDispatch/Flash.html)
    - The flash provides a way to pass temporary primitive-types (String, Array, Hash) between actions. Anything you place in the flash will be exposed to the very next action and then cleared out. This is a great way of doing notices and alerts, such as a create action that sets flash[:notice] = "Post successfully created" before redirecting to a display action that can then expose the flash to its template. Actually, that exposure is automatically done.
    - 
###### Model

#### Create and Forms

- In the ```new``` method, a new Book instance is created but not saved in the database. We don't know yet whether or not the book is valid. We store this in an instance variable we can use in the new.html.erb file.
```ruby
  def new
    @book = Book.new
  end
```

<br>

- In the new.html.erb file, we use embedded tags to render a form and pass in the instance variable from the ```new``` method. ```<%= render 'form', book: @book %>```
- In the next embedded tags, we use ```link_to``` wich takes two 'arguments', text of the link which is ```'Back'``` and then the path. ```books_path``` is a short way of going to the index route.

```html
    <h1>New Book</h1>

    <%= render 'form', book: @book %>

    <%= link_to 'Back', books_path %>
```

<br>

- In the form, here we use syntax ```form_with``` to take in the specific instance variable
 - under field elements we use embedded tags to render the attribute name and include a field corresponding to each model attribute. ```form_with``` also is associated with the ```create``` method once submited.

    - **NOTICE** form.text_field and form_text_area are different. When we used scaffold to generate the views, each attribute has a different type and therefore was given different form input.

 ```html
 <%= form_with(model: book) do |form| %>
    <div class="field">
    <%= form.label :title %>
    <%= form.text_field :title %>
  </div>

  <div class="field">
    <%= form.label :description %>
    <%= form.text_area :description %>
  </div>

  <div class="actions">
    <%= form.submit %>
  </div>
<% end %>
```
- Once submitted, rails will send a post request and execute the ```create``` method that exists in the books_controller.rb

<br>

- In the ```create``` method, as we invoke/call the new method from the book instance, we pass in book params from the form. 

```ruby
    @book = Book.new(book_params)
```
- We then check to see if the book instance variable is valid. 
- The save method is ORM doing some work. Checking to see if the params to create the book instance/record is valid. Calling save will return true or false depending on storing the potential instance to the database. 
- If true, use ruby code to redirect to show the book instance. Include a notice to indicate to the page that it was succesffully created
- If false, render new and add a status of unprocessability entity (you can see more about specific status codes [here](https://gist.github.com/mlanett/a31c340b132ddefa9cca))

```ruby
    respond_to do |format|
      if @book.save
        format.html { redirect_to @book, notice: "Book was successfully created." }
      else
        format.html { render :new, status: :unprocessable_entity }
      end
    end
```

- ```respond_to``` can be an efficent way of formmating responses whether it is responding to a view or JSON data. [for more information](https://apidock.com/rails/ActionController/MimeResponds/InstanceMethods/respond_to)
#### Flash
- You can store temporay data in flash that allow you to expose that in the very next action(which executes the method that exists from the controller and renders the view)
```ruby
    format.html { redirect_to @book, notice: "Book was successfully created." }
```

```notice: "Book was successfully created."``` is a short way of ```flash[:notice] = "Book was successfully created```

- Inlcuding the following in the show.html.erb file will show the notice created in the created method
```html
<% if flash[:notice]%>
<p id="notice"><%= notice %></p>
<% end %>
```

#### Update
Explain the update process in the edit action, update action and also the correspondance file of edit.html.erb.

#### Delete
Explain the delete process in the destroy method.

#### References
