# Class 1 - Introduction to the Back End 
---
### Table of Contents 
- Introduction
- Back End Roadmap 
- Setup
--- 
## Introduction 
The Back End portion of Code Labs will be similiar to the Front End in regards to structure and style. Students are expected to learn outside of class and apply that knowledge inside of class ([Flipped Class Room Model](https://omerad.msu.edu/teaching/teaching-skills-strategies/27-teaching/162-what-why-and-how-to-implement-a-flipped-classroom-model)). You will be asked to read documentation, watch assigned videos, complete exercises and build projects as well as commit your code to GitHub. Building projects are our main focus here at Code Labs as it challenges you to think.  

Class notes will be posted for students to use as a resource. Keep in mind these class notes are maintained. If something comes ambiguous or needs an update, please contact your code coach for adjustment or clarification. 

## The Back End Roadmap

In the Back End, we will go over Ruby and Ruby on Rails (Rails for short). Rails is a web framework running on the Ruby programming language. Rails allows us to build Full Stack web applications and RESTful APIs fast and easily!  

By the end of this course, you will know how to create a full stack Ruby on Rails application and be able to connect a Rails API to Front End Applications. We will do things we weren't able to do before in the Front End such as: 
- store data in an SQL database 
- create our very own API 
- use Bcrypt to create an authentication system 
- create a model view controller application or MVC for short 
- cookies
- explore features such as emailing users, real time communication, role management systems and more. 

### Course Structure 
The span of this course will be 20 weeks. We will go over the following: 
- The Ruby Programming Language
- Introduction to Ruby on Rails and Setup
- CRUD Operations in Rails
- Styling in Rails
- Associations & Authentication Systems
- Many-to-many Associations & Automated Testing
- Course Project
- Rails Base API
- Employer Project 

#### The Ruby Programming Language
Classes 2 - 5 will be spent learning the Ruby programming language. It's important to understand the basic syntax and fundamentals of Ruby in order to move forward onto Rails. The reason for that is the ability to debug faster and recognize common uses of Ruby into Rails. Without the basics of Ruby, it'll be difficult to differentiate between what is Rails and what is Ruby. 

Live coding sessions will be spent creating a Ruby gem and building out a command line interface. In class will involve exercises. We will cover the following Ruby fundamentals:
- syntax 
- data types
- printing to the console
- Irb
- conditionals
- loops and iterations
- classes and Object Oriented Programming
- methods and more

#### Introduction to Ruby on Rails and Setup
Students will have installed Rails and will be introduced to the basics of Rails. Rails 6 and Ruby 2.7 will be used during the live coding sessions. Keep in mind the video course pertains to Rails 5 and Rails 6.

#### CRUD Operations in Rails 
Create, Read, Update, Delete or CRUD operations involve a specific process in which a user interacts with the application. For example, the user can, let's say, `create` a post in an application. The user can see or `read` the post they created in perhaps their homepage. The user can `update` the post. The user can also `delete` the post. CRUD operations is a standard convention commonly used in web development. We will also start a project in Rails similiar to what is built in the Front End. That's right, the [BookIt](https://bookit-codefi.herokuapp.com/) application but in Rails.

#### Styling in Rails
In the course videos provided, it's important to note that Bootstrap 4 will be used. Since the release of [version 5](https://getbootstrap.com/docs/5.0/getting-started/introduction/) in May 2021, these notes will be based on Bootstrap 5.

#### Associations & Authentication Systems
An association involves a relationship between two models. A model is a reference to a data entity in the database. Rails incorporates different types of associations that describe this connection. What's the point of an association? An association provides the coder commonly used model operations without directly using Structured Query Language or SQL. 

Furthermore, we will explore authentication. Previously in the Front End, we used the Firebase API as our main authentication system. In Rails, we will use the Bcrypt gem to develop our very own authentication system. We also explore different alternatives such as Devise. Devise incorporates an already coded authentication system in our application so us developers don't have to. Why not start with devise? It's a matter of use case of whether you need a custom authentication system or perhaps a quick and commonly used set up that is devise. In Rails, you will find out that by just a few command lines, you can scaffold an entire base line of an application in minutes. However, this does not teach you how to create these applications and more importantly understand them. 

#### Many-to-many Associations & Automated Testing
Many to Many associations involve the connection between multiple records of two specific models. The connection also involves a third model in which connects these two models. We also explore automated testing in Rails. Automated testing is great for when an application has many features and updates.

#### Course Project
During the Course Project section, students will create an MVP that follow specific requirements. This time spent will allow you to soley focus on a portfolio project.

#### Rails Base API
The Base API, created by developers at Codefi, will be given to each student. This gives you a chance to add any additional features pertaining to your Front End Applications and learn more of an orginized workflow of that of the base API. 

#### Employer Project
Towards the end of the Back End, students connect with local employers to simulate a real world experience.

## Setup
Before diving deep into the program, we need the necessary tooling. There are various of IDEs that you can use such as VS Code and WebStorm. In this specific curriculum, we will continue to use VS Code because of the live share extension. This helps for pair programming in a remote setting.

```To install Ruby, refer to section 13 for Mac and section 15 for Windows in *The Complete Ruby on Rails Developer Course*.```

For now we will use [Replit](https://replit.com/~). Replit is an online IDE for various programming languages. It's easy to sign up with and you can connect your GitHub to Replit to create repos. In the future, we will use a local IDE such as VS Code.

### Next Section - The Ruby Programming Language
- Before further exploring Rails, we have to first establish a good foundation of the Ruby programming language. In these next couple of weeks, we will focus on doing just that.