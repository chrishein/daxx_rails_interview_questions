# Answers for Interview Questions suggested by DAXX (2014)

Answers for questions suggested in the article *[How to Hire the Right Offshore Ruby on Rails Developer: Ruby on Rails Interview Questions](https://www.daxx.com/article/how-hire-offshore-ruby-on-rails-developer)*.

You may know all the answers and might have used most of these in your day to day work, but it helps to write it down again so that you may put it in words when interviewed.

Note that the article was published July 21, 2014.

## Questions

### Ruby Interview Questions

1. How do you define class, instance, and global variables?

    - Class Variables
    ```ruby
    class Song
      @@plays = 0
    end
    ```
    **Important note on this and Class Level Instance Variables [here](http://www.railstips.org/blog/archives/2006/11/18/class-and-instance-variables-in-ruby/).**
    - Instance variables
    ```ruby
    class Person
      def initialize
        @name = "Tom"
      end
    end
    ```
    Or if you want it defined with getter and setter methods
    ```ruby
    class Person
      attr_accessor :name

      def initialize
        @name = "Tom"
      end
    end
    ```

    More on [attr_accessor](http://stackoverflow.com/a/4371458/250703).
    - Global Variables
    ```ruby
    $global_variable = 10
    ```

2. What is rubygems?

    [RubyGems.org](https://rubygems.org/) is the Ruby community’s gem hosting service, where everyone can instantly publish gems and then install them.

3. What is a module?

    From [Ruby Docs](http://ruby-doc.org/core-2.2.0/Module.html).

    *A Module is a collection of methods and constants. The methods in a module may be instance methods or module methods. Instance methods appear as methods in a class when the module is included, module methods do not. Conversely, module methods may be called without creating an encapsulating object, while instance methods may not.*

    From [rubylearning.com](http://rubylearning.com/satishtalim/modules_mixins.html):
    Modules serve two purposes:
    - They act as namespace:
    ```ruby
    # p058mytrig.rb  
    module Trig  
      PI = 3.1416  
      # class methods  
      def Trig.sin(x)  
        # ...  
      end  
      def Trig.cos(x)  
        # ...  
      end  
    end  

    # p059mymoral.rb  
    module Moral  
      VERY_BAD = 0  
      BAD         = 1  
      def Moral.sin(badness)  
        # ...  
      end  
    end  

    # p060usemodule.rb  
    require_relative 'p058mytrig'  
    require_relative 'p059mymoral'  
    Trig.sin(Trig::PI/4)  
    Moral.sin(Moral::VERY_BAD)
    ```

    - They allow you to share functionality between classes (mixins):
    ```ruby
    module Persister
      def save_to_file
        ...
      end
    end

    class WorkSheet
      include Persister

      def do_stuff
        ...
      end
    end

    worksheet = WorkSheet.new
    worksheet.do_stuff
    worksheet.save_to_file
    ```
4. What is a Range?

    From [Ruby Docs](https://ruby-doc.org/core-2.2.0/Range.html).

    *A Range represents an interval—a set of values with a beginning and an end. Ranges may be constructed using the s..e and s...e literals, or with ::new. Ranges constructed using .. run from the beginning to the end inclusively. Those created using ... exclude the end value. When used as an iterator, ranges return each value in the sequence.*

    ```ruby
    (-1..-5).to_a      #=> []
    (-5..-1).to_a      #=> [-5, -4, -3, -2, -1]
    ('a'..'e').to_a    #=> ["a", "b", "c", "d", "e"]
    ('a'...'e').to_a   #=> ["a", "b", "c", "d"]
    ```

5. What is a Symbol?

    From [Ruby Docs](http://ruby-doc.org/core-2.2.0/Symbol.html).

    *Symbol objects represent names and some strings inside the Ruby interpreter. They are generated using the :name and :"string" literals syntax, and by the various to_sym methods. The same Symbol object will be created for a given name or string for the duration of a program's execution, regardless of the context or meaning of that name. Thus if Fred is a constant in one context, a method in another, and a class in a third, the Symbol :Fred will be the same object in all three contexts.*

    ```ruby
    (-1..-5).to_a      #=> []
    (-5..-1).to_a      #=> [-5, -4, -3, -2, -1]
    ('a'..'e').to_a    #=> ["a", "b", "c", "d", "e"]
    ('a'...'e').to_a   #=> ["a", "b", "c", "d"]
    ```

6. How are a Symbol and a String different?

    The same Symbol object will be created for a given name or string for the duration of a program's execution. This is not true with strings.

    ```ruby
    :my_symbol.object_id  #=> 544168
    :my_symbol.object_id  #=> 544168
    :my_symbol.object_id  #=> 544168

    "my_string".object_id #=> 70230778661160
    "my_string".object_id #=> 70230778572940
    "my_string".object_id #=> 70230778536700
    ```

7. How do “and” and “&&” operators differ?

    From [Difference between “and” and && in Ruby?](http://stackoverflow.com/a/1426835/250703).

    `and` is the same as `&&` but with [lower precedence](http://phrogz.net/ProgrammingRuby/language.html#table_18.4). They both use [short-circuit evaluation](http://en.wikipedia.org/wiki/Short-circuit_evaluation).

    **WARNING:** `and` even has lower precedence than `=` so you'll want to avoid `and` always


8. How do you define a custom Exception?

    ```ruby
    class MyError < StandardError
    end
    ```

### Rails Interview Questions
1. What is Scope in Rails?

    As stated in the [Rails Guides](http://guides.rubyonrails.org/active_record_querying.html#scopes), scoping allows you to specify commonly-used queries which can be referenced as method calls on the association objects or models. With these scopes, you can use every method such as where, joins and includes. All scope methods will return an ActiveRecord::Relation object which will allow for further methods (such as other scopes) to be called on it.

    ```ruby
    class Article < ApplicationRecord
      scope :published, -> { where(published: true) }
    end
    ```

2. What is a sweeper?

    From Action Controller Sweeper in [rails-observers](https://github.com/rails/rails-observers#action-controller-sweeper).

    *Sweepers are the terminators of the caching world and responsible for expiring caches when model objects change. They do this by being half-observers, half-filters and implementing callbacks for both roles. A Sweeper example:*

    ```ruby
    class ListSweeper < ActionController::Caching::Sweeper
      observe List, Item

      def after_save(record)
        list = record.is_a?(List) ? record : record.list
        expire_page(:controller => "lists", :action => %w( show public feed ), :id => list.id)
        expire_action(:controller => "lists", :action => "all")
        list.shares.each { |share| expire_page(:controller => "lists", :action => "show", :id => share.url_key) }
      end
    end
    ```

    *The sweeper is assigned in the controllers that wish to have its job performed using the cache_sweeper class method:*

    ```ruby
    class ListsController < ApplicationController
      caches_action :index, :show, :public, :feed
      cache_sweeper :list_sweeper, :only => [ :edit, :destroy, :share ]
    end
    ```

3. What is an observer?

    From Action Record Observer in [rails-observers](https://github.com/rails/rails-observers).

    *Observer classes respond to life cycle callbacks to implement trigger-like behavior outside the original class. This is a great way to reduce the clutter that normally comes when the model class is burdened with functionality that doesn't pertain to the core responsibility of the class. Observers are put in app/models (e.g. app/models/comment_observer.rb). Example:*

    ```ruby
    class CommentObserver < ActiveRecord::Observer
      def after_save(comment)
        Notifications.comment("admin@do.com", "New comment was posted", comment).deliver
      end
    end
    ```

    *This Observer sends an email when a Comment#save is finished.*

4. What is a filter and when is it called?

    From [Rails Guides](http://guides.rubyonrails.org/action_controller_overview.html#filters).

    *Filters are methods that are run "before", "after" or "around" a controller action.*

    *Filters are inherited, so if you set a filter on ApplicationController, it will be run on every controller in your application.*

    ```ruby
    class ApplicationController < ActionController::Base
      before_action :require_login

      private

      def require_login
        ...
      end
    end
    ```

5. How do you implement caching in Rails?

    See [Rails Guides: Caching with Rails: An Overview](http://guides.rubyonrails.org/caching_with_rails.html).

6. How do you create a REST API for your app?

    - [Building a RESTful API in a Rails Application](https://www.airpair.com/ruby-on-rails/posts/building-a-restful-api-in-a-rails-application) by Abraham Polishchuk.

    - [APIS ON RAILS](http://apionrails.icalialabs.com/book/chapter_one) by Abraham Kuri Vargas.

    - [Rails Guides: Using Rails for **API-only** Applications](http://edgeguides.rubyonrails.org/api_app.html).

7. What is a polymorphic association and how do you implement it?

    From [Rails Guides](http://guides.rubyonrails.org/association_basics.html#polymorphic-associations).

    *With polymorphic associations, a model can belong to more than one other model, on a single association. For example, you might have a picture model that belongs to either an employee model or a product model.*

    ```ruby
    class Picture < ApplicationRecord
      belongs_to :imageable, polymorphic: true
    end

    class Employee < ApplicationRecord
      has_many :pictures, as: :imageable
    end

    class Product < ApplicationRecord
      has_many :pictures, as: :imageable
    end
    ```

8. What is fields_for used for?

    From the [apidock Rails](http://apidock.com/rails/ActionView/Helpers/FormHelper/fields_for).

    *Creates a scope around a specific model object like form_for, but doesn’t create the form tags themselves. This makes fields_for suitable for specifying additional model objects in the same form.*

    ```ruby
    <%= form_for @person do |person_form| %>
      First name: <%= person_form.text_field :first_name %>
      Last name : <%= person_form.text_field :last_name %>

      <%= fields_for :permission, @person.permission do |permission_fields| %>
        Admin?  : <%= permission_fields.check_box :admin %>
      <% end %>

      <%= person_form.submit %>
    <% end %>
    ```

### Related

1. What is the difference between functional testing and unit testing?

    From Stackoverflow ["Unit tests vs Functional tests"](http://stackoverflow.com/questions/2741832/unit-tests-vs-functional-tests).

    *Unit Test - testing an individual unit, such as a method (function) in a class, with all dependencies mocked up.*

    *Functional Test - AKA Integration Test, testing a slice of functionality in a system. This will test many methods and may interact with dependencies like Databases or Web Services.*

2. Do you have experience using a mocking framework?

    - [RSpec Mocks](https://github.com/rspec/rspec-mocks)

    - [Mocha](https://github.com/freerange/mocha)

3. Do you have any experience with BDD using RSpec or Cucumber?

    - [Applying BDD to Ruby on Rails Web Applications](https://semaphoreci.com/community/tutorials/applying-bdd-to-ruby-on-rails-web-applications)

    - [RSpec](http://rspec.info/)

    - [Cucumber](https://cucumber.io/docs)

4. What plugin would you suggest for full-text search?

    Note: Not *plugins*.

    - [Thinking Sphinx](https://github.com/pat/thinking-sphinx)

    - [Sunspot](https://github.com/sunspot/sunspot)

    - [Searchkick](https://github.com/ankane/searchkick)

    - [elasticsearch-ruby](https://github.com/elastic/elasticsearch-ruby)

5. What is your most used plugin for user authorization?

    - [CanCan](https://github.com/ryanb/cancan)

    - [CanCanCan](https://github.com/CanCanCommunity/cancancan)

    - [Pundit](https://github.com/elabs/pundit)

6. How do you create a plugin?

    See [Rails Guides](http://guides.rubyonrails.org/plugins.html).

    ```ruby
    rails plugin new yaffle
    ```
