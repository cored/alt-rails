# Active Record

Rails comes with Active Record by default, and we should make the most of it. Yet, to keep our application easy to maintain, there are some guidelines to follow.

## Understanding Active Record

Here's an expanded version of the content on Active Record:

Active Record is both a pattern and an implementation in Rails. The pattern can be defined as follows:

```plaintext

An object that holds both data and behavior. Most of this data is persistent and needs to be stored in a database. Active Record follows a straightforward approach by placing data access logic in a domain object. This ensures everyone knows how to read and write their data to and from the database.

```

Active Record, implemented in Rails, provides an abstraction layer over the database, allowing developers to interact with data using Ruby objects instead of writing raw SQL queries. It follows the Object-Relational Mapping (ORM) principle, which maps database tables to Ruby classes and database records to instances of those classes.

For example, if you have a `users` table in your database with columns like `id`, `name`, `email`, and `created_at`, you can define a corresponding `User` class that inherits from `ActiveRecord::Base`. This class will automatically have methods for querying, creating, updating, and deleting user records.

```ruby

class User < ApplicationRecord

  validates :email, presence: true, uniqueness: true

end

```

While the Active Record pattern and its implementation in Rails provide a convenient way to work with databases, it can lead to a mix of persisted data and domain logic within the same object. This can make the code harder to maintain and test, as changes in one area can affect other application parts.

Since domain logic changes most in an application, we aim to remove business logic from Active Record models and treat them as simple data structures that facilitate data access and modification. This approach is referred to as the Anemic Domain Model.

The term "anemic" might suggest this approach is problematic, but it can be a beneficial strategy for separating concerns. By keeping Active Record models focused on data access and manipulation, we can move the business logic to other components, such as service objects, value objects, or domain objects. This makes the code more modular, testable, and maintainable.

Moreover, since we cannot control changes in Active Record within a Rails application, separating concerns helps us mitigate the impact of potential changes in the framework. By encapsulating the framework-specific code in a specific layer, we can adapt to changes and ensure that our application's core logic remains stable.

By following this approach, you can create a more maintainable and scalable Rails application, leveraging Active Record while adhering to best practices in software design.

## What to Include in an Active Record Model

An Active Record model should contain only the essentials:

1. Basic Validations: To enforce data integrity.

   - Validations ensure that the data stored in the database meets certain criteria, such as presence, uniqueness, format, or length.

   - Validations should be kept simple and focused on data integrity. Complex validations that depend on specific contexts should be handled using form objects or service objects.

   - Example:

     ```ruby

     class User < ApplicationRecord

       validates :email, presence: true, uniqueness: true

       validates :name, presence: true, length: { minimum: 3 }

     end

     ```

2. Associations: Definitions for relationships with other models.

   - Associations define the relationships between models, such as one-to-one, one-to-many, or many-to-many.

   - Associations help maintain data consistency and make it easier to navigate and query related data.

   - Defining associations in Active Record models allows you to leverage the built-in methods provided by Rails, such as `has_many`, `belongs_to`, or `has_and_belongs_to_many`.

   - Example:

     ```ruby

     class User < ApplicationRecord

       has_many :posts

       has_one :profile

     end

     class Post < ApplicationRecord

       belongs_to :user

     end

     ```

3. Convenience Methods: Useful methods to find or manipulate records (scopes).

   - Scopes are class-level methods that encapsulate common queries or filters.

   - Scopes help keep your code DRY (Don't Repeat Yourself) by providing reusable ways to retrieve specific sets of records.

   - Scopes should be named and should focus on a single responsibility.

   - Example:

     ```ruby

     class User < ApplicationRecord

       scope :active, -> { where(active: true) }

       scope :by_email, ->(email) { where(email: email) }

     end

     ```

### Example of a Simple Active Record Model

```ruby

class User < ApplicationRecord

  # Validations

  validates :email, presence: true, uniqueness: true

  validates :name, presence: true, length: { minimum: 3 }

  # Associations

  has_many :posts

  has_one :profile

  # Scopes

  scope :active, -> { where(active: true) }

  scope :by_email, ->(email) { where(email: email) }

end

```

By keeping Active Record models focused on these essentials, you can maintain a clean separation of concerns and make your code more maintainable and testable in the long run.

Here’s an expanded version of the content on what not to include in an Active Record model, with detailed explanations and examples:

## What Not to Include in an Active Record Model

When designing your Active Record models, it's essential to maintain a clean separation of concerns. This helps ensure that your models remain focused on data access and manipulation without becoming bloated or overly complex. Here are key elements to avoid:

### 1. Form-Specific Validations

**Avoid:** Including validations that are specific to a particular form or context directly in your Active Record models.

**Why:** Form-specific validations can lead to tightly coupled code and make it difficult to reuse models across different forms or contexts. Instead, use form objects to encapsulate these validations.

**Example of What to Avoid:**

```ruby
class User < ApplicationRecord
  validates :password, presence: true, if: :new_record?
end
```

**Suggested Approach Using Form Objects:**

```ruby
class UserForm
  include ActiveModel::Model

  attr_accessor :name, :email, :password

  validates :email, presence: true, uniqueness: true
  validates :name, presence: true
  validates :password, presence: true, if: :new_record?

  def save
    return false unless valid?

    User.create(name: name, email: email, password: password)
  end
end
```

### 2. Private Methods

**Avoid:** Including private methods that contain business logic within your Active Record models.

**Why:** Private methods can obscure the purpose of the model and make it harder to understand and test. Instead, consider moving complex logic to value objects or service objects that can be reused and tested independently.

**Example of What to Avoid:**

```ruby
class User < ApplicationRecord
  private

  def calculate_discount
    # Some complex discount logic
  end
end
```

**Suggested Approach Using Service Objects:**

```ruby
class DiscountCalculator
  def initialize(user)
    @user = user
  end

  def calculate
    # Complex discount logic based on user attributes
  end
end

# Usage
user = User.find(1)
discount = DiscountCalculator.new(user).calculate
```

### 3. Callbacks

**Avoid:** Using callbacks (like `after_save`, `before_destroy`, etc.) in your Active Record models.

**Why:** Callbacks can create hidden dependencies and make the flow of your application harder to follow. They can lead to unexpected side effects and tightly coupled code. Instead, perform operations explicitly in service objects or controllers to maintain clarity and reduce coupling.

**Example of What to Avoid:**

```ruby
class User < ApplicationRecord
  after_create :send_welcome_email

  private

  def send_welcome_email
    UserMailer.welcome_email(self).deliver_now
  end
end
```

**Suggested Approach Using Service Objects:**

```ruby
class UserCreationService
  def initialize(user_params)
    @user_params = user_params
  end

  def call
    user = User.create(@user_params)
    send_welcome_email(user) if user.persisted?
  end

  private

  def send_welcome_email(user)
    UserMailer.welcome_email(user).deliver_now
  end
end

# Usage
UserCreationService.new(user_params).call
```

### 4. External Calls

**Avoid:** Making calls to objects outside of the persistence layer within your Active Record models (e.g., calling external services, workers, views, or controllers).

**Why:** This practice can lead to tight coupling between different layers of your application, making it harder to test and maintain. Instead, handle these interactions in context-dependent components, such as service objects or controllers.

**Example of What to Avoid:**

```ruby
class User < ApplicationRecord
  def publish_user_info(key)
    ::Hutch::Publisher.publish(key, UserSerializer.new(self).as_json)
  end
end
```

**Suggested Approach Using Service Objects:**

```ruby
class UserPublisher
  def initialize(user)
    @user = user
  end

  def publish(key)
    ::Hutch::Publisher.publish(key, UserSerializer.new(@user).as_json)
  end
end

# Usage
user = User.find(1)
UserPublisher.new(user).publish('user_info_key')
```

By avoiding these practices in your Active Record models, you can maintain a clean separation of concerns, making your codebase more modular, testable, and maintainable. This approach allows you to leverage Active Record's strengths while keeping your business logic organized and easy to manage.

Here’s a revised version of the query object explanation and examples, ensuring originality and clarity:

## Utilizing Query Objects for Advanced Queries

Query objects serve as powerful tools for isolating complex SQL queries from your Active Record models. By doing so, you help maintain your models' focus on their primary responsibilities while also enhancing the readability and maintainability of your code.

Consider a scenario where you have an Active Record model designed to retrieve a list of trending articles:

```ruby
class Article < ApplicationRecord
  scope :trending,
        -> { where(trending: true).where('likes_count > ?', 50) }
end
```

This scope can be refactored into a dedicated query object, which encapsulates the logic for fetching trending articles:

```ruby
module Articles
  class TrendingQuery
    def initialize(relation = Article.all)
      @relation = relation
    end

    def call
      @relation.where(trending: true).where('likes_count > ?', 50)
    end
  end
end
```

Now, instead of invoking the scope directly from the model, you can utilize the query object:

```ruby
trending_articles = Articles::TrendingQuery.new.call
```

### Maintaining Interface Consistency

If the `Article.trending` scope is used throughout your application, you might want to keep that interface intact while delegating the logic to the query object. This way, you won't need to change every occurrence of `Article.trending`.

To achieve this, you can modify the `scope` implementation in the `Article` model to utilize the query object:

```ruby
class Article < ApplicationRecord
  scope :trending, Articles::TrendingQuery.new
end
```

### Making the Query Object Callable

To streamline the integration further, you can make the query object callable by defining a `call` method:

```ruby
module Articles
  class TrendingQuery
    class << self
      def call(relation = Article.all)
        new(relation).call
      end
    end

    def initialize(relation)
      @relation = relation
    end

    def call
      @relation.where(trending: true).where('likes_count > ?', 50)
    end
  end
end
```

With this setup, you can now simply reference the query object in your scope:

```ruby
class Article < ApplicationRecord
  scope :trending, Articles::TrendingQuery
end
```

Leveraging query objects effectively separates complex query logic from your Active Record models. This approach keeps your models clean and focused and allows for greater flexibility and reusability of your query logic across different parts of your application. Using query objects enhances maintainability and clarity, making it easier for developers to understand and modify the codebase in the future.

Here’s an expanded version of the content on value objects:

## Value Objects

Value objects are a design pattern used to encapsulate specific data and associated behavior without the overhead of a full Active Record model. They are handy for representing concepts that do not have a unique identity but are defined by their attributes. Value objects help maintain clarity and encapsulation in your code by providing a clear structure for handling related data.

- Value objects are often designed to be immutable, meaning their state cannot change once created. This helps avoid unintended side effects and makes it easier to reason about the code.
- Value objects are compared based on their attributes rather than their object identity. Two value objects are considered equal if all their attributes are the same.
- Value objects encapsulate data and behavior, allowing you to define methods that operate on their data. This keeps related logic together and improves code organization.
- Unlike Active Record models, value objects are not tied to a database table. They are typically used in memory and can be passed around as needed.

### Example of a Value Object

Here’s an example of a `Money` value object that encapsulates an amount and a currency:

```ruby
class Money
  attr_reader :amount, :currency

  def initialize(amount, currency)
    @amount = amount
    @currency = currency
  end

  def convert_to(new_currency, exchange_rate)
    converted_amount = @amount * exchange_rate
    Money.new(converted_amount, new_currency)
  end

  def ==(other)
    other.is_a?(Money) && amount == other.amount && currency == other.currency
  end

  def to_s
    "#{amount} #{currency}"
  end
end
```

Value objects can be used in various scenarios where you need to represent a specific concept without the overhead of a full model. For example, you could use the `Money` value object in a financial application to handle monetary values consistently:

```ruby
# Creating money instances
usd_amount = Money.new(100, 'USD')
eur_amount = usd_amount.convert_to('EUR', 0.85)

puts usd_amount.to_s   # Output: "100 USD"
puts eur_amount.to_s    # Output: "85.0 EUR"
```

Value objects are a powerful tool for encapsulating related data and behavior cleanly and efficiently. By using value objects, you can improve the organization of your code, reduce the complexity of your models, and promote immutability and equality in your application. This design pattern is particularly beneficial in domains where specific concepts must be represented without persistent storage.

## View Models

View models are a design pattern that transforms and presents data for display in the user interface (UI). They act as an intermediary between the domain models and the view layer, encapsulating the logic required for rendering specific views. By separating the presentation logic from the domain models, view models promote a cleaner separation of concerns and make the code more testable and maintainable.

View models are beneficial when the UI requires a specific representation of data that differs from the structure of the underlying domain models. They allow you to transform and combine data from multiple sources to create a view-specific representation, making it easier to render the desired UI elements.

Here's an example of a `UserViewModel` that transforms user data for display in a profile view:

```ruby
class UserViewModel
  def initialize(user)
    @user = user
  end

  def display_name
    "#{@user.first_name} #{@user.last_name}"
  end

  def profile_info
    {
      email: @user.email,
      full_name: display_name,
      avatar_url: avatar_url,
      created_at: @user.created_at.strftime('%B %d, %Y'),
      last_login: @user.last_login.strftime('%B %d, %Y at %I:%M %p')
    }
  end

  private

  def avatar_url
    if @user.avatar.attached?
      Rails.application.routes.url_helpers.url_for(@user.avatar)
    else
      'default_avatar.png'
    end
  end
end
```

In this example, the `UserViewModel` takes a `user` object as input during initialization. It defines methods to transform the user data into a view-specific representation:

- `display_name` combines the `first_name` and `last_name` attributes to create a full name.
- `profile_info` returns a hash containing the transformed user data, including the `display_name`, `email`, `avatar_url`, `created_at`, and `last_login`.

The `avatar_url` method is a private method that handles the logic for generating the URL of the user's avatar image.

To use the view model, you can create an instance and call the desired methods to retrieve the transformed data:

```ruby
view_model = UserViewModel.new(user)
profile_info = view_model.profile_info
```

The `profile_info` hash can then be passed to the view template to render the user's profile information.

Using view models lets you keep your views focused on rendering the UI elements and delegate the data transformation logic to dedicated classes. This approach promotes a cleaner separation of concerns, making the code more testable, maintainable, and easier to understand.

Here's an expanded version of the testing guidelines for Active Record models:

## Testing Guidelines

Here are some key guidelines to keep in mind when testing Active Record models:

1. Since validations are a fundamental part of data integrity, they should be tested as side effects from higher-level application layers such as form objects or service objects. By testing validations in these contexts, you ensure that they are enforced without duplicating the tests in the model specs.

2. Associations between models should be tested through top-level acceptance tests or integration tests. These tests ensure that the relationships between models are defined and functioning as expected. Avoid making assertions about associations in individual model specs.

3. Active Record provides a comprehensive suite of methods for interacting with the database, such as `find`, `where`, `create`, and more. Rails has an extensive test suite that ensures these methods work as expected. Thus, you can assume that these methods work and focus your testing efforts on the specific use cases and business logic in your application.

### Example of a Simple Test

Here's an example of a simple test for an Active Record model:

```ruby

RSpec.describe User, type: :model do

  it 'validates presence of email' do

    user = User.new(email: nil)

    expect(user.valid?).to be_falsey

    expect(user.errors[:email]).to include("can't be blank")

  end

end

```

In this example, we test the presence validation for the `email` attribute. Yet, as mentioned earlier, this validation should ideally be tested in a higher-level context, such as a form object or service object spec.

### Optimizing Testing by Loading Only Active Record

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

In this setup:

1. The `active_record` library is loaded explicitly, ensuring that only the necessary components are included.

2. The database connection is established using the test configuration from the `database.yml` file.

3. RSpec is configured to wrap each test in a transaction and automatically roll it back after the test completes. This ensures that the test database remains in a consistent state between tests.

## Resources

- [Delegating to query objects through Active Record scopes](http://craftingruby.com/posts/2015/06/29/query-objects-through-scopes.html)
- [7 patterns to refactor fat Active Record models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)
- [Growing Rails applications in practice](https://pragprog.com/book/d-kegrap/growing-rails-applications-in-practice)
- [Active Record spec helper](https://gist.github.com/coreyhaines/2068977)

By following these guidelines, you can create a more maintainable and scalable Rails application, leveraging Active Record effectively while adhering to best practices in software design.
