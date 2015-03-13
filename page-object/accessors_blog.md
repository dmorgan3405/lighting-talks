# PageObject::Accessors.rb

## Understanding basic usage

To take advantage of these methods, including the PageObject module is all that is needed.

```ruby
class LoginPage
  include PageObject

  text_field(:username, :id => 'Username')
end

```

Now from a step definition, you can do the following

```ruby
  on(LoginPage).username = 'user1' #sets the text field value
  on(LoginPage).username       #gets the current value  
```

This is fairly basic, but having a good idea of how to properly use these methods can help keep your pages clean. 
For a better understanding of the usage consider buying a copy of [Cucumber & Cheese ](https://leanpub.com/cucumber_and_cheese) or for a brief intro checkout the[PageObject wiki ](https://github.com/cheezy/page-object/wiki/Get-me-started-right-now!)

## Questions, not state

An important topic to note, is that the **PageObject::Accessors** methods enable us to ask questions about the elements current state.

In other words, the object itself has no element state. It only knows how to go about retrieveing it. 

## Dynamic Methods

Each element accesor dynamically defines a set of methods that include the name of the element you are defining.

```ruby
  def text_field(name, identifier={:index => 0}, &block) 
      standard_methods(name, identifier, 'text_field_for', &block)
      define_method(name) do
        return platform.text_field_value_for identifier.clone unless block_given?
        self.send("#{name}_element").value
      end
      define_method("#{name}=") do |value|
        return platform.text_field_value_set(identifier.clone, value) unless block_given?
        self.send("#{name}_element").value = value
      end
  end
```

"#{name}", "#{name}=", "#{name}_element","#{name}?" are just a few examples.

It is also important to note, the methods these dynamically defined methods are added on to object calling the method. 

This is why we are able to call on_page(LoginPage).username from the step definition. 

*For a list of generated methods for each element checkout the [PageObject wiki](https://github.com/cheezy/page-object/wiki/Elements) or  dive into [PageObject::Accessors.rb](https://github.com/cheezy/page-object/blob/master/lib/page-object/accessors.rb)*

## How is this accomplished

Ruby has meta programming features. (such as the send and define_method functions)

Since the PageObject module has been included in our class the 'self' context in which the methods are defined is the instance of the MyPage class. 

## define_method "#{name}_element"

This method returns an instance of the corresponding PageObject::Elements class. 
In the example, *username_element* returns an instance of the **PageObject::Elements::TextField** class.
Having access to the PageObject element allows us to ask additional state questions, and allows the PageObject to encapsulate complex element behaviors.

My preference is to use the defined accessor methods, when available, versus directly accessing the element.
I prefer using this methods to enhance the readability of the code and decrease the amount the code written. 

## Meaningful names

This means the names we give our elements should be meaningful! 

To me a good element, is describing the bevhavior not the type. 

Lets say we have a text field for a material we are adding to a price list. 
A good name would be :material or :add_material, i think a better name would be :material_to_add.