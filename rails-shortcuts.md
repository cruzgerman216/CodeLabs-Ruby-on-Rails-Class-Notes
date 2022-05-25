# Shortcut commands

Long commands

```
$ rails server	       
$ rails console	        
$ rails generate	    
$ rails test	        
$ bundle install	    
```

Short commands

```
 $ rails s
 $ rails c
 $ rails g
 $ rails t
 $ bundle
```

#### Generate controller with the following actions and templates

```
rails generate controller StaticPages home help
```

You can also do the same with the following but in snake case

```
 rails generate controller static_pages home help
```

To undo the previous command run the following

```
rails destroy  controller StaticPages home help
```

#### Generate a model

```
rails generate model User name:string email:string
```

You can include just name and email like so

```
rails generate model User name email
```

And they will be defaulted to type strings

To undo this, enter the following

```
rails destroy model User
```

#### Go back on a previous migration

```
rails db:rollback
```

To go back to a specific version we can do the following. Versions are located in the file names, usually they are long numbers.

```
rails db:migrate VERSION=0
```
