# Ruby on Rails - CRUD Operations Part 1

## Topics covered

- **<em>Resource naming conventions</em>**
  - Resource example
    - Model name: article
    - Article model file name: article.rb
    - Article model class name: Article
    - Table name: articles
  - **<em>rails db:migrate</em>** - will update the schema file for any new migration files
  - **<em>[rails db:rollback](https://blog.saeloun.com/2020/04/21/rails-adds-support-for-db-rollback-name-for-multiple-database-applications.html#:~:text=To%20revert%20such%20mistakes%20we,the%20latest%20migration%20was%20run.&text=We%20can%20also%20pass%20STEP,number%20of%20migrations%20to%20revert)</em>** - to revert such mistakes we use db:rollback , which restores the database to a state, before the latest migration was run.
  - **<em>rails c</em>**
    - Once in the terminal, you can exit the console by entering `exit`
    - rails console allows you to test your tables or models such as `Article.all`
    - when updating the model, you can enter `reload!` to reflect those changes
  - **<em>Expand routes in terminal</em>**
    - `Rails routes --expanded `
  - **<em>params</em>** is a hash with key pair values of parameters being passed in from a request.
  - **<em>instance variables defined in controller methods</em>** are able to be read from the view
  - **<em>embedded ruby tabs</em>** `<% %>` allows instance variabled to be evaluated(used) in the view
    - displayed ruby code `<%= %>`
  - **<em>byebug</em>**
    - Ruby gem used for testing
    - <em>The debugger permits the ability to understand what is going on inside a Ruby program while it executes </em>
:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**!