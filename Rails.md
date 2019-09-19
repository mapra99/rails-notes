# Ruby... on Rails!!!

## Initialize a Project

From a parent directory where the project folder is about to be created, on the CLI:

```bash
rails new <project_name>
```

To specify the rails version to use:

```bash
rails _5.2.3_ new <project_name>
```

With these commands Rails create a bunch of directories and files.

## Bundler and Gemfile

The _Bundler_ installs and includes gems in the app. The `./GemFile` is a document that contains all the gems that should be imported.

### The Gemfile

Among the default gems that the `Gemfile` contains, `sqlite3`, `puma`, `sasss-rails`, etc, are selected.

```ruby
source 'https://rubygems.org'
git_source(:github) { |repo| "https://github.com/#{repo}.git" }

ruby '2.5.5'

# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
gem 'rails', '~> 5.2.3'
# Use sqlite3 as the database for Active Record
gem 'sqlite3'
# Use Puma as the app server
gem 'puma', '~> 3.11'
# Use SCSS for stylesheets
gem 'sass-rails', '~> 5.0'
# Use Uglifier as compressor for JavaScript assets
gem 'uglifier', '>= 1.3.0'
# See https://github.com/rails/execjs#readme for more supported runtimes
gem 'duktape'
# Use CoffeeScript for .coffee assets and views
gem 'coffee-rails', '~> 4.2'
# Turbolinks makes navigating your web application faster. Read more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
# Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
gem 'jbuilder', '~> 2.5'
# Use Redis adapter to run Action Cable in production
# gem 'redis', '~> 4.0'
# Use ActiveModel has_secure_password
# gem 'bcrypt', '~> 3.1.7'

# Use ActiveStorage variant
# gem 'mini_magick', '~> 4.8'

# Use Capistrano for deployment
# gem 'capistrano-rails', group: :development

# Reduces boot times through caching; required in config/boot.rb
gem 'bootsnap', '>= 1.1.0', require: false

group :development, :test do
  # Call 'byebug' anywhere in the code to stop execution and get a debugger console
  gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
end

group :development do
  # Access an interactive console on exception pages or by calling 'console' anywhere in the code.
  gem 'web-console', '>= 3.3.0'
end

group :test do
  # Adds support for Capybara system testing and selenium driver
  gem 'capybara', '>= 2.15'
  gem 'selenium-webdriver'
  # Easy installation and use of chromedriver to run system tests with Chrome
  gem 'chromedriver-helper'
end

# Windows does not include zoneinfo files, so bundle the tzinfo-data gem
gem 'tzinfo-data', platforms: [:mingw, :mswin, :x64_mingw, :jruby]
```

### Ways of using the gem command

- The most basic one:

  `gem <packageName>` 

- Using a version range:

  - `gem '<packageName>', '>= <version>'` 

    It will call any version higher or equal than `<version>`. If `<version>` is `4.0.0` and current version is 7.2.0, version `7.2.0` will be called.

  - `gem '<packageName>', '~> <version>'`  

    It will call any **child version** higher than `<version>`. It means that, if `<version>` is `4.0.0`, it will call the latest  `4.0.x` version.

- To see a list of all installed gems and their current version:

  `gem list`

- To see the version of a determined installed gem and the current version:

  `gem list <packageName>`

### Setting up different gems according to environment

The `group`structure from Ruby can be used to group statements that will apply when the specified environment applies:

```ruby
group :development, :test do
	gem 'sqlite3'
end
```

This will call the `sqlite3` package only for **development** and **test** environments.

### Bundle

Every time the `GemFile` is updated, the Bundler must be called to execute the changes and include the specified gems in the project. From the CLI, the following command must be executed:

```shell
#To install all gems
bundle install

#To install all gems except the ones from production to avoid troubles with Deployment
bundle install --without production
```

When the specified version in the GemFile is not the same locally available version, this command must be called:

```shell
bundle update
```

After calling `bundle update`, `bundle install` must be called.

`bundle update` also updates the gems to the latest version if the `GemFile` doesn't specify a version, or if the command is called this way:

```shell
bundle update *gems[--all]
```

## Rails Server

On the CLI, from the Project's Folder:

```shell
rails server
```

It will run the application on a localhost. The app can be opened in http://localhost:3000 . This is the default content of the app:

![](etc\Captura.PNG)

## Model-View-Controller (MVC)

MVC is an architecture that enforces a separation between the data in the application and the code used to display it. It's a common way of structuring a GUI.

The architecture works this way: **Browser** sends a request, it is received by a server and passed to a **Controller**. The controller decides one of two choices: it can render a **View**, or it can interact with a **Model**. The Model is a Ruby object that represents an object in the site, and communicates with the database.

![](etc\mvc_schematic.png)

MVC architecture works in many contexts, not only web development. It is one of the most frequently used patterns. These are some of the WebDev frameworks use concepts of MVC:

- Ruby on Rails
- Sinatra (Ruby)
- Codeigniter (PHP)
- Django (Python)
- Angular (JS)

### The Model

Basically the 'Brain' of the App. It contains the data related logic, it manages the interactions with database , it communicates with controller and sometimes it updates the view (not so MVC, but sometimes it happens).

#### DB Associations

To establish the relation rules between tables in the DB, Rails has a set of statements that can be applied in the `./app/model/` files, inside the class definitions for each object.

- `has_many`
- `belongs_to`

##### Examples:

```ruby
class User < ApplicationRecord
    has_many :microposts
end
```

```ruby
class Micropost < ApplicationRecord
    belongs_to :user
end
```

#### Input Validation

Inside the model files the app can perform input validation with `validates`

##### Examples:

 ```ruby
class Micropost < ApplicationRecord
  belongs_to :user
  validates :content, length: { maximum: 140 },
                      presence: true
end
 ```

```ruby
class User < ApplicationRecord
  #belongs_to :user
  has_many :microposts
  validates :name,  format: { with: /\A[a-zA-Z|\s]+\z/, message: "must be only letters and spaces"},
                    presence: true
  validates :email, format: { with: URI::MailTo::EMAIL_REGEXP },
                    presence: true
end
```

### The View

It takes care of the UI. It usually consists of HTML/CSS. It communicates with the controller and uses template engines to output dynamic values. In ruby it is the `erb` (Embedded Ruby) files.

### The Controller

The Controller takes in User Input, processes requests, gets data from model and passes data to the view (to the template engine).

### The Router

The router is the component that really receives the requests and calls specific controllers. It's the doorman of the application.

The router receives the HTTP request: an operation (GET, UPDATE, POST, ETC.) over a determined resource and it calls for a determined controller to attend the request. It also creates a hash called `params` that collects all the parameters sent in the request, for later use in the controller.

On the CLI the following statement can be typed to get all the routes of the project:

```shell
rails routes
```

#### Root

The simplest route in the `routes.rb` file is the root. Usually the root is used to give the route of the homepage. So, in the `routes.rb ` file, the root can be set this way:

```ruby
Rails.application.routes.draw do
    root "<resource>#<method_name>"
end
```

So the established route will send the request to the method `<method_name>` from the resource `<resource>` , which is defined in the controller.

#### REST Routes

Recalling the [REST architecture](#rest-architecture), there is always a series of operations of interest to do on a resource. The corresponding routes would be (taking a resource called `posts` as example):

```ruby
  get "/posts", to: "posts#index"
  get "/posts/:id", to: "posts#show"
  get "/posts/new", to: "posts#new"
  post "/posts", to: "posts#create"  # usually a submitted form
  get "/posts/:id/edit", to: "posts#edit"
  put "/posts/:id", to: "posts#update" # usually a submitted form
  delete "/posts/:id", to: "posts#destroy"
```

In order to speed up this repetitive process of creating a series of routes for every REST operation, This command can be used instead:

```ruby
 resources :posts
```

#### Route Helpers

In cases where in the same app it is required to call another resource, Rails has the route helpers to avoid the hardcoding of the URL.

```ruby
link_to "<text_to_be_shown>", <rest-op>_<resource>_path(<required_params)
```

All the helpers have the `_path` or the `_url.path` ending. The first one creates a relative path, and the other one creates the full URL.



## Deployments on Heroku

There are some main changes that are needed to be made. 

1. Heroku uses PostgreSQL as database manager. It is not compatible with SQLite3, so the `GemFile` must distinguish between the production environment, and the development and testing environment. So, in the `GemFile`, instead of calling the `sqlite3` gem:

   ```
   gem 'sqlite3'
   ```

   This must be replaced with this:

   ```ruby
   group :production do
   	gem 'pg'
   end
   group :development, :test do
       gem 'sqlite3'
       gem 'byebug', platforms: [:mri, :mingw, :x64_mingw]
   end
   ```

   As the `GemFile` was updated, the `bundle install` command must be executed in the CLI, but in this case the `--without` flag must be used to avoid installing `pg` or any other production gem:

   ```bash
   bundle install --without production
   ```

### Initialize Heroku

To initialize Heroku, the following command must be used on CLI:

```shell
heroku create
```

This command creates a remote Git repository on Heroku. To check the connection, this can be typed:

```shell
git remote
```

and `heroku` should appear on the output list.

The files must be pushed to the Heroku's remote repo. To do that:

```shell
git push -u heroku master
```

When finished the upload, the app is now deployed.

### Migrate DB to Heroku

To migrate the database to Heroku, this command must be run on the CLI:

```shell
heroku run rails db:migrate
```

## Resources

A Resource is the constitution of a data model and a web interface. It will let think of the objects as instances that can be created, read, updated and deleted through the web.

### Create a Resource

The `scaffold` command leads to the creation of resources. On the CLI:

```shell
rails generate scaffold <name> <attr1_name>:<attr1_type> <attr2_name>:<attr2_type> <attr3_name>:<attr3_type> ...
```

Once a Resource is created, its DB must be migrated:

```shell
rails db:migrate
```

### Associate a Resource in the Router

On the router file (`./config/routes.rb`), to associate a resource in the router, this must be written inside the Rails block:

```ruby
Rails.application.routes.draw do
    resources :<name>
end
```

## HTTP

Hypertext Transfer Protocol is a **stateless**, **application-layer** protocol for communicating between distributed systems. In the context of Web Development, it's the protocol that most of web pages use to make client - server requests.

- Stateless: It doesn't keep state of message exchanges. It is necessary to allow the communication of a variety of hosts an clients.
- Application-layer: It is executed at the level of app-code, which means that you can take control of the requests and responses directly from the code.

### Requests

These are some of the requests that the browser sends to a server to open the page [tuitsplus](https://code.tuitsplus.com).

![](.\etc\http1.PNG)

A common HTTP request is composed of a URL, a Verb and the HTTP version. All these three elements are wrapped in a message header.

![](.\etc\http2.PNG)

#### URL

A Uniform Resource Locator (URL) is a structured string that contains the following elements to request something over a determined resource:

```web-idl
http://video.google.co.uk:80/videoplay?docid=-7246927612831078230&hl=en#00h02m30s
```

- The request protocol (`http://`)
- The host (`video.google.co.uk`)
  - The subdomain (`video`)
  - The domain (`google.co.uk`)
  - The top-level domain (`uk`)
  - The second-level domain (`co.uk`)
- A port (`:80` If not typed, the default port is `:80`)
- A path to the resource (`/videoplay`. If not set, the default path is the `root`)
- Parameters, if necessary (`?docid=-7246927612831078230&hl`).
- Fragment/Named Anchor (`#00h02m30s`)

URLs can be static or dynamic. They are static when the Web Server is not required to perform any computation (i.e. just HTML, CSS and JS). They are Dynamic when the Web Server is required to perform computations (Back-end Algorithms, Operations over Database, etc).

#### Verbs

HTTP allows multiple request types. The most frequent ones are:

- GET fetches an existing resource
- POST  creates a new resource
- PUT updates an existing resource
- DELETE delete an existing resource
- HEAD fetches only the response header
- TRACE retrieves the hops that a request takes to get to the server
- OPTIONS retrieves the server capabilities

#### Version

There multiple versions of HTTP/HTTPS, most of them come with updates of the request and response structure, with small variations on the Verbs and the URL format.

Current HTTP version is HTTP/2.0 (16/09/2019).

### Responses

The content of a HTTP response depends on the type of information and the type of action (verb) that is being requested. Generally all elements of an HTTP response contain a message header and a message body. In the message header, there is always a **Status Code** that says if a the request was successful or if there was an error.

- Status Codes 2xx
  - Code 200 OK: Everything is ok
  - The other 2xx status codes are related to errors made by the client in the request, or lack of content in the resource. All 2xx are generally messages that warn about lack of content or lack of precision in the request, although they all are successful.
- Status Codes 3xx indicate that the resource has been moved to a different URL.
- Status Codes 4xx indicate a Client Error.
  - Code 404 Not Found. The resource hasn't been found.
- Status Codes 5xx indicate a Server Error.

## REST Architecture

REST (Representational State Transfer) basically says that there are really only 7  different types of things that you usually want to do to an individual  resource via the web and you can do them by mixing and matching the HTTP  verbs we just covered. 

1. GET all elements from a resource (aka **“index”** the resource)
2. GET just one specific element from the resource (aka **“show”** a specified element)
3. GET the page that lets you create a new element from the resource (aka view the **“new”** element page)
4. POST the data you just filled out for a new element back to the server so it can create that element (aka **“create”** the element)
5. GET the page that lets you edit an existing element (aka view the **“edit”** element page)
6. PUT (or PATCH) the data you just filled out for editing the element back to the server so it can actually perform the update (aka **“update”** the element)
7. DELETE one specific element by sending a delete request to the server (aka **“delete”** the element)

The highlighted words correspond to standard Rails controller actions.

This architecture is important because it gives a structured way of modeling the resources' requests. This must be the only way that those requests are done.

## APIs



## Practice Projects

### The Hello World

#### Sources

- [GitHub](https://github.com/mapra99/hello_world_rails)

- [Source](https://www.learnenough.com/ruby-on-rails-4th-edition-tutorial/beginning#sec-hello_world)

So, on the `./controllers/application_controller.rb` file, the following method must be created:

```ruby
class ApplicationController < ActionController::Base
  def hello
    render html: "hello, world!"
  end
end
```

With the `hello` method defined in the controller, it is necessary to tell the app to use this method instead of loading the default **View** document.

The **Router** is the component of the controller that defines what to do when the browser request is received. The router is located in the `./config/routes.rb` file.

The following statement must be typed in this file:

```ruby
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  root 'application#hello'
end
```

With these things ready, it is now possible to run again the app with `rails server` from the CLI, and going to http://localhost:3000.

![](etc\Captura2.PNG)

### Microposts Page

#### Sources

- [GitHub](https://github.com/mapra99/toyApp)
- [Live Version](https://pacific-atoll-88301.herokuapp.com/)
- [Source](https://www.learnenough.com/ruby-on-rails-4th-edition-tutorial/toy_app#sec-inheritance_hierarchies)

The microposts page will be a page where users can do small posts (< 140 characters).

##### Database

There will be two objects on the app: *users* and *microposts*. Each one will have a table.

- Users

  | attr_name | type   |
  | :-------: | ------ |
  |   name    | string |
  |   email   | string |
  
- Microposts

  