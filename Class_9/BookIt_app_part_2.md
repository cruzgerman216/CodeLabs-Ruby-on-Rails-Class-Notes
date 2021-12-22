  # Ruby on Rails - CRUD Operations part 2
  ## BookIt App Part 2

## Topics covered
  - **<em>forms</em>** [resource](https://guides.rubyonrails.org/form_helpers.html)
    - scope - table name
    - url - path to submit to
    - instance variable errors - errors when creating the instance. We can display a message if we have any errors
  - **<em>permit parameters</em>** [resource](https://apidock.com/rails/ActionController/Parameters/permit)
    - strong parameters to limit/whitelist certain paramters
  - **<em>Authenticity Tokens</em>** - Rails includeds hidden fields for post/update/delete forms that include an authenticity token to prevent cross site forgery attacks.
  - **<em>flash</em>** [resource](https://api.rubyonrails.org/classes/ActionDispatch/Flash.html)
    - The flash provides a way to pass temporary primitive-types (String, Array, Hash) between actions. Anything you place in the flash will be exposed to the very next action and then cleared out. This is a great way of doing notices and alerts, such as a create action that sets flash[:notice] = "Post successfully created" before redirecting to a display action that can then expose the flash to its template. Actually, that exposure is automatically done.
  - **<em>Strong Paramters</em>**
    - Prevents paramters requested to an object unless specifically permitted.

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

#### Create and New action
4. In books_controller.rb, under the create method, declare a instance variable called book
```ruby
def create  
  @book
end
```
- Set book to `Book.new()`
```ruby
  @book = Book.new()
```
- Because strong parameters allow only certain params to be requested, use require and permit methods to whitelist certain keys
```ruby
  @book = Book.new(title:params[:title], description: params[:description])
```
- Save book into the database

```ruby
    def create 
        @book = Book.new(params.require(:book).permit(:title, :description))
        @book.save
    end
```

- Fill in the input fields and click submit(Make sure you meet your validations, book has to have a length of at least 2 characters and descriptions to be at least 6 characters when being entered. Check your book model to see validations). You should get an ERROR.
  - **<em>EXPLANATION</em>**: The reason why you get an error is the way we did the form in new.html.erb, it requires an instance of the Book model to help render the form.

5. In the new method, Declare an instance variable called book and set that to an unsaved Book Intance. 

```ruby 
    def new 
        @book = Book.new()
    end
```

- Try again and fill the input fields. And then click submit. You should get a status code 204 in the terminal `No content`. No content means we aren't returning anything back or rendering a view. Which makes sense because all we did was save our Book instance to the database. Run `rails c` and enter the following `Book.last`. You should see that your Book instance saved succcesfully.

- **<em>Explanation</em>** When the user requests a form to submit a book, our new action needs an instance variable to be render onto the form thus `@book = Book.new()`. Of course, there are other ways to provide the new form.
- When inspecting the form in the browser, you can see that the method is set to `post`. Method refers to what type of request is being sent. Action refers to the path, in this case `/books`. When submitting the form, you will have sent a post request to the url `/books` which will execute the create action. To make sure of this, in the terminal, enter `rails routes --expanded`. You should see all the list of available routes in an organized manner. 

#### Authenticity Token
- What is the Authenticity Token in the rails forms?
```When the user views a form to create, update, or destroy a resource, the Rails app creates a random authenticity_token, stores this token in the session, and places it in a hidden field in the form. When the user submits the form, Rails looks for the authenticity_token, compares it to the one stored in the session, and if they match the request is allowed to continue.``` [Resource](https://stackoverflow.com/questions/941594/understanding-the-rails-authenticity-token)

- Why?
```Rails prevents what is called a cross-site request forgery where a hacker may take advantage of stored data known as sessions.``` [For more info](https://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf)

#### redirecting 
6. In the create method, redirect to `books/:id` of what will contain the path to the instance we created.
```ruby 
    def create 
        @book = Book.new(params.require(:book).permit(:title, :description))
        @book.save
        redirect_to @book
    end
```

#### Error messages
7. In the create method, use an if/else statement to see if the book saved successfully. If true, redirect to `@book`. Else, ```render `new\```
  
```ruby
  if @book.save 
      redirect_to @book
  else 
    render 'new'
  end
```

8. In new.html.erb, use an if statement and embbeded tags to check any error messages the instance variable holds.
```html 
  <h1>Create a new Book</h1>

  <% if @book.errors.any? %>

  <% end %>
```

9. In the conditional, iterate through the messages and print them to the page.
```html
<% if @book.errors.any? %>
        <% @book.errors.full_messages.each do |message| %>
        <p><%= message %></p>
        <% end %>
<% end %>
```

#### Using Flash messages
10. In the create method, if the book saved successfully, let's create a flash message to output a message to the books/:id page `Book was saved successfully`

```ruby
        if @book.save
            flash[:notice] = "Book was created successfully"
            redirect_to @book
        else 
```

11. Include the flash message in application.html.erb. 

```html
  <body>
  
    <% flash.each do |name, message| %> 
      <%= message %>
    <% end %>

    <%= yield %>

  </body>
```

#### Edit and Update Methods to update book

12. Create an edit and update method for the books controller file.

```ruby 
def edit 

end 

def update 

```

13. Create edit.html.erb under views/books directory. Navigate to new.html.erb, and copy the entire file. Paste the contents of the copied file inside of edit.html.erb. Change the content of the h1 element to `Edit Book` and the content of the submit button to `Update Book`.

```html
<h1>Edit Book</h1>

<% if @book.errors.any? %>
        <% @book.errors.full_messages.each do |message| %>
        <p><%= message %></p>
        <% end %>
<% end %>

<%= form_with(model: @book, url: books_path) do |form| %>
        <%= form.label :title, "Title:" %>
        <%= form.text_field :title %>
        <%= form.label :title, "description:" %>
        <%= form.text_area :description %>
        <%= form.submit "Update Book" %>
<% end %>
```

14. In books_controller.rb, under the edit method, use the params hash to find the specific book indicated in the url. Store the instance in an instance variable.

```ruby
    def edit
        @book = Book.find(params[:id])
    end
```

15. Use the update method to update the instance variable. Pass in the corresponding attributes from the params hash.

```ruby
  @book = Book.find(params[:id])
  @book.update(params.require(:book).permit(:title, :descriptoin))
```

16. Use an if/else statement to check if @book updated successfully. If true, add a flash message set to `Update Successfully`, then redirect to the book path. Otherwise, render the edit page.

```ruby 
        if @book.update(params.require(:book).permit(:title, :description))
            flash[:message] = "Update successfully!"
            redirect_to @book
        else
            render 'edit'
        end
```

#### delete action to delete book
17. In books_controller.rb, define a destroy method. In the destroy method find the specific record that holds the id that exists in params.

```ruby 
    def destroy 
        @book = Book.find(params[:id])
    end
```

18. Delete the book, then redirect to the books page 

```ruby 
      @book = Book.find(params[:id])
      @book.destroy
      redirect_to books_path
```

19. Navigate to the index.html.erb file to update the placeholder actions. Create a link with content `Delete` that will send a delete request to the particular book you want to delete.

```html 
            <tr>
                <td><%= book.title%></td>
                <td><%= book.description%></td>
                <td><%= link_to 'Delete', book_path(book), method: :delete%></td>
            </tr>
```

20. Create a link with content with content `Show` that will send a get request to `books/:id` of the particular book. 

```html
                <td><%= link_to 'Show', book_path(@book)%>
                <td><%= link_to 'Delete', book_path(book), method: :delete%></td>
```
