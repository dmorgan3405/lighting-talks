#PageObject::Widgets

Creating Custom Element Widgets to use with Page Object

## First things first. Why?

JavaScript has made it easier for developers to create custom controls that go above and beyond the behavior that only a single html element can provide. 

Libraries such jquery ui widgets and kendo ui offer an example of such controls.

Testing these controls can involve a few things:

* Complex wait conditions
* Knowledge of hidden html elements
* Knowlegde of multiple html elements

This leads to duplication of this logic across the test suite where these elements are used. 
Using a widget allows you to encapsulate this behavior and reduce the amount of duplication.  

## Where to start?

All widgets need to include **PageObject::Elements::Element** in its ancestry.

Therefore a good place to start is deciding what html best represents the new control 
and then create a new class

```ruby
	class AutoComplete < PageObject::Elements::TextField

	end
```

## Registration

Adding the widget is straight forward. The three pieces of infomation you need are:

* widget_tag => name to define widget accessor method as
* widget_class => class where widget is defined
* base_element_tag => tag of the element the widget inherits

```ruby
	PageObject.register_widget(:auto_complete, AutoComplete, 'text_Field')
```

## Usage

```ruby
class TestPage
	include PageObject

	auto_complete(:city, :id => 'City')

end
```

This will define two methods we can use with in our page, 

	city_element - this will return an instance of **PageObject::Elements::TextField**

	city? - returns whether or not the element exists

**For a more indepth look at the standard accessor methods checkout accessor_blog.md


## Adding Behaviour

Thus far, adding a new widget will not add much value. 

Lets change that, here are the ways to add behaviour to the widget.

## Accessor Methods

One way we can do this is by defining a class level method in our new elements class

```ruby
class AutoComplete < PageObject::Elements::TextField

	def self.accessor_methods(accessor, name)

		accessor.send :define_method, "#{name}=" do |value|
	        #code for desired behaviour
	    end

	    accessor.send :define_method, "#{name}" do |value|
	        #code for desired behaviour
	    end

	    #...

	end

end
```

This will define the "#{name}=", and "#{name}".

## Lets think meta

Calling other methods on the element, and interacting with the element is not as stright forward as normal
since the methods are dynamically generated based on the element name.

So inorder to interact with the element we need to think meta. 

```ruby
	def self.accessor_methods(accessor, name)

		accessor.send :define_method, "#{name}=" do |value|
			self.send("#{name}_element")
	    end

	end
```

In the above code example, the *self.send("#{name}_element")* is the interesting piece, because
self in the context of the define_method block is actually the object in which the accessor method was called on. 

## Huh? #metaprogramming

All this means is that in the example below, self is an instance of TestPage.

```ruby
class TestPage
	include PageObject

	auto_complete(:city, :id => 'City')

end
```

##The other way

Sometimes using the accessor methods is not a viable option, because we need to locate the element dynamically in a table.

To add behavior to an instance of a class, simply add instance methods 

```ruby
class AutoComplete < PageObject::Elements::TextField

	def set(value)
	    #code for desired behaviour
	end

end
```

## Gotchas

You should define dynamically created methods with name being part of the method name

	ex. "#{name}_options"

Failing to do so will result in runtime errors when you attempt to define two of the same widget in a PageObject

Sharing behaviour between dynamically defined methods and instance methods can be done with class level methods.

Encapsulating private behaviour is not as easy as using the **private** section. You need to send the private message to the new accessor module with a list of methods to make private. 

Example: 

```ruby
class AutoComplete < PageObject::Elements::TextField

	def self.accessor_methods(accessor, name)

		accessor.send :define_method, "#{name}=" do |value|
	        #code for desired behaviour
	    end

	    accessor.send :define_method, "#{name}" do |value|
	        #code for desired behaviour
	    end

	    #...

	    accessor.send :private, "#{name}", "#{name}="

	end
end
```