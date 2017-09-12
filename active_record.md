## Active Record

Since Rails bundles by default with Active Record let's embrace it; however
since we want for our application to be easy to maintain, understand, test
and scale here we are going to establish certain guidelines to reduce
the coupling between the business logic of the application which is the thing
that changes the most and the framework.

There's one notion to take into account Active Record is a pattern and
Rail's ActiveRecord is an implementation of it. The pattern definition goes as follow:

```
An object carries both data and behavior. Much of this data is persistent and needs
to be stored in a database. Active Record uses the most obvious approach,
putting data access logic in a domain object. This way all people know how to
read and write their data to and from the database.
```

Which since reasonable however if you take a closer look you will see that it
specify the mixing of persisted data in a database with domain logic; this is
not appropiate since what we know change the most in an application is the
domain logic. For this reason we want to reduce the amount of business logic in
an active record model and instead we are going to use the models as dump data
structures to access and modify data in our database.

This is commonly known as [Anemic Domain Model] however do not let the "anemic"
trick you into think that this is something bad; quite the contrary since we
don't have control over ActiveRecord changes in a Rails application and we
don't want to load all it's dependencies to test our business logic this is
a good approach to do separation of concerns.

## What to put in an active record model

A model should only contain the absolute minimum to exist:

* A minimum set of validations to enforce data integrity
* Definitions for associations
* Universally useful convenience methods to find or manipulate records (scopes)

## Suggested directory structure for components dependant on persistence details.

```
# Directory structure
apps
  |-models
   |-<model_name>s # Pluralize
    |-<query object>
   |-<model_name>
    |-<form object>
```

## What not to put in an active record model

* Validations that only happen on a particular form - use form objects instead
* Private methods (Use value objects, service objects to extract the logic)
* Callbacks (Do the operation per context to reduce coupling)
* Avoid calls to objects outside of the persistence layer (workers, views,
controllers, services objects, message queue wrappers, serializers) prefer to
make those calls in context dependent to a top level component. 
```ruby
class User < ActiveRecord::Base
   # Avoid
   def publish_user_info(key) 
      ::Hutch::Publisher.publish(key, UserSerializer.new(self).as_json)
   end
end
```
* Avoid complex scopes (Use query objects instead)
* Do not add wrapper methods for display purposes; use value objects if it's
  needed in a lot of places or view models for doing a transformation for
  a particular model)

```ruby
class User < ActiveRecord::Base 
  # Avoid
  def full_info 
     {
        profile: ProfileSerializer.new(profile), 
        user: UserSerializer.new(self)
     }
  end
end

# Possible refactor: 
class Users::WithFullInfo
   def initialize(user:, profile_serializer: ProfileSeralizer, user_serializer: UserSerializer) 
     @user = user
     @profile_serializer = profile_serializer
     @user_serializer = user_serializer 
   end 
   
   def to_h
     {
       profile: profile_serializer.new(user.profile), 
       user: user_serializer.new(user)
     }
   end
   
   private 
   
   attr_reader :user, ;profile_serializer, :user_serializer
end
   
```
* Avoid concerns. Concerns or module mixins are another way of doing
  inheritance. The main purpose of that concept is for code specialization not
  for sharing code across components. A decorated model would be better to
  isolate the logic and use it in specific context.

### Form objects

Use a form object when a set or one validation happens dependent of a context.
A hint to find this out is when we use a conditional validation.

```ruby
# TODO: Add example
```

#### Suggested refactoring:

```ruby
# TODO: Add example
```

### Avoid private methods

Private methods normally hide a concept from the business domain. Also we want
to make all the active record models definitions as dump as possible in order
for them to be flexible.

```ruby
# TODO: Add Example
```

#### Suggested refactoring:

```ruby
# TODO: Example
```

### Avoid callbacks

Callbacks can create coupling between the record state life cycle instead let's
be explicit about what needs to happen before/after any particular context.

```ruby
# TODO: Example
```

#### Suggested refactoring:

```ruby
# TODO: Example
```

### Use query objects when defining complex scopes

Query objects are still persistence logic so a good place to put them would be
in the `app/model` directory. The idea behind this is that we are explicitly going to
be separating which are the Rails framework details from what is our
business logic. Also use query objects when you need to use `SQL`.


```ruby
# TODO: Example
```

#### Suggested refactoring:

```ruby
# TODO: Example
```

The advantages of defining query objects in this way is that Rails will
understand the `.call` method and call it in the background. Also now we are
going to be able to be specific about the spec for this particular query object
in a new spec file, reducing the amount of clutter in both the `app/models`
directory and the `Shipment` model spec.

### Value objects

```ruby
# TODO: Example
```

#### Suggested refactoring:

```ruby
# TODO: Example
```

The use of [dry-types](http://dry-rb.org/gems/dry-types/) will give us the advantage of type coercion and also documentation
to know what to pass to this new value object.

### View models

View models normally would be transformation returned by service objects that
will provide all the view logic needed to display data in an UI.

```ruby
# TODO: Example
```

Often than not you will prefer value objects to carry most display logic. Use
view models when the underlying transformation is very complex at first and
then when the need for removing duplication appears try to extract smaller
values.

## Testing

After following some of this guidelines or all of them; tests for active record
models become very straight forward. However there are some guidelines to also
follow while testing.

* Do not test validations (This will be tested as a side effect from the top
  level layers of the application)
* Do not make assertions about associations (This will be tested as a side
  effect from a top level acceptance test)
* Do not test side effects of wrapper methods that make a call to the internal
  API of ActiveRecord (Rails already has an extensive test suite testing all
  those methods)

```ruby
# TODO: Example
```

Since we are going to be pushing most complex logic to the higher level
application layer the need for having a one on one unit tests for active record
models should be minimal.

### Load just active record when testing a model

```ruby
require 'active_record'

connection_info = YAML.load_file("config/database.yml")["test"]
ActiveRecord::Base.establish_connection(connection_info)

RSpec.configure do |config|
  config.around do |example|
      ActiveRecord::Base.transaction do
        example.run
        raise ActiveRecord::Rollback
      end
  end
end
```

## Resources

[Delegating to query objects through active record scopes](http://craftingruby.com/posts/2015/06/29/query-objects-through-scopes.html)

[7 patterns to refactor fat active record models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)

[Growing Rails applications in practice](https://pragprog.com/book/d-kegrap/growing-rails-applications-in-practice)

[Active record spec helper](https://gist.github.com/coreyhaines/2068977)
