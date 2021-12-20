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

#### Creating a form to save a book 

1. Define methods new and create under the books_controller.rb file

```ruby 
    def show
        @book = Book.find(params[:id])
    end

    def index 
        @books = Book.all
    end

    def new 

    end

    def create 
        
    end
```
2. Define view new.html.erb under views/books directory. Include the following html

```html
  <h1>Create a new Book</h1>
```

3. Use embedded tags and the form_with helper to build a form in new.html.erb
```html
<%= form_with do |form| %>

<% end %>
```

- Include arguments model and url to form_with corresponding to @book and books_path
```html
<%= form_with(model: @book, url: books_path) do |form| %>

<% end %>
```

- use the reference `form` and embedded ruby tags to create labels for title and description

```html
<%= form_with(model: @book, url: books_path) do |form| %>
        <%= form.label :title, "Title:" %>
        <%= form.label :title, "description:" %>
<% end %>
```

- Create fields for title and description

```html
        <%= form.label :title, "Title:" %>
        <%= form.text_field :title %>
        <%= form.label :title, "description:" %>
        <%= form.text_area :description %>
```
- Add a submit button

```html
        <%= form.label :title, "Title:" %>
        <%= form.text_field :title %>
        <%= form.label :title, "description:" %>
        <%= form.text_area :description %>
        <%= form.submit "Save Book" %>
```
- Go to the route `localhost/3000/books/new`, you should see your newly created form. Try click the button and you should see status 204 in the terminal. That is because we didn't do anything to the create method in the books controller file.

#### Create action
4. In books_controller.rb, under the create method, access properties title and description from params. Use those properties values as arguement to invoke the new method from the Book class.
```ruby 
  Book.new(title: params[:title], description: params[:description])
```
#### References
