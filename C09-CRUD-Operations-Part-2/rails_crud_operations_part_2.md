# Ruby on Rails - CRUD Operations part 2

## Topics covered
  - **<em>forms</em>** [resource](https://guides.rubyonrails.org/form_helpers.html)
    - scope - table name
    - url - path to submit to
    - instance variable errors - errors when creating the instance. We can display a message if we have any errors
  - **<em>permit parameters</em>** [resource](https://apidock.com/rails/ActionController/Parameters/permit)
    - strong parameters to limit/whitelist certain parameters
  - **<em>Authenticity Tokens</em>** - Rails includes hidden fields for post/update/delete forms that include an authenticity token to prevent cross site forgery attacks.
  - **<em>flash</em>** [resource](https://api.rubyonrails.org/classes/ActionDispatch/Flash.html)
    - The flash provides a way to pass temporary primitive-types (String, Array, Hash) between actions. Anything you place in the flash will be exposed to the very next action and then cleared out. This is a great way of doing notices and alerts, such as a create action that sets flash[:notice] = "Post successfully created" before redirecting to a display action that can then expose the flash to its template. Actually, that exposure is automatically done.
  - **<em>Strong Parameters</em>**
    - Prevents parameters requested to an object unless specifically permitted.

---
:wave: Saw a misspelled word? Want to improve the class notes? Create a **pull request** and **contribute**! 
