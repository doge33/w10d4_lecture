# Rails Back-End with React Front-End Setup

## Create Rails App

- create a project folder and cd into the folder
- type the following command:

`rails new backend --api --database=postgresql -T --no-rdoc --no-ri`

- configure database in database.yml

## Ports

- React front-end is running on port 3000
- Rails back-end is running on port 3001 (or any other)

  `rails s -p 3001 -b 0.0.0.0`

## Cors

**Cross-Origin Resource Sharing**

- A web application makes a cross-origin HTTP request when it requests a resource that has a different domain (i.e. different ports)
- Web application using APIs can only request HTTP resources from the same origin the application was loaded from, unless the response from the other origin includes the right CORS headers.

## Proxy API Calls on The Client

Add a proxy to package.json:

```json
{
  "name": "client",
  "version": "0.1.0",
  "private": true,
  "proxy": "http://localhost:3001",
  ...
```

Start the React Client with Yarn start

## Enable Cors on The Back-End

Rails 5 with --api mode will prepare the setup for you. You just need to uncomment the following:

```ruby
# Gemfile
gem 'rack-cors'

# config/initializers/cors.rb
# client running on port 3000

Rails.application.config.middleware.insert_before 0, Rack::Cors do
  allow do
    origins 'localhost:3000'
    resource '*',
      headers: :any,
      methods: %i(get post put patch delete options head)
  end
end
```

- Run `Bundle install`
- Run `rake db:reset` and `rake db:migrate`
- Start the back-end: `rails s -p 3001 -b 0.0.0.0`

** if you get this warning **
warning: already initialized constant FileUtils::VERSION

Disable `require 'bootsnap/setup'` in config/boot.rb

## Create The Tables

- generate models with rails g model

`rails g model user name`

- generate controllers with rails g controller

`rails g controller api/users`

- Setup the relationships
- run migrations
- run seed

```ruby
require('faker')

3.times do
  User.create(first_name: Faker::Name.first_name, last_name: Faker::Name.last_name, email: Faker::Internet.email, password: Faker::Internet.password)
end
```

## Setup The Routes

### Create API Namespace

- In routes.rb:

```ruby
namespace :api do
  resources :users
end
```

- Namespace the controllers

- Create a folder `/api/` under `app/controllers`
- Move the controller files into this folder
- Add namespacing to controllers:

```ruby
class Api::UsersController < ApplicationController

  def index
    users = User.all
    render json: users
  end

end
```

## References

[Cross-Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

[Rails 5 API and CORS](https://til.hashrocket.com/posts/4d7f12b213-rails-5-api-and-cors)

[Configuring the Proxy Manually](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#configuring-the-proxy-manually)

[ReactJS + Ruby on Rails API + Heroku App](https://medium.com/@bruno_boehm/reactjs-ruby-on-rails-api-heroku-app-2645c93f0814)
