Adding Custom Element Widgets to Page Object

First things first. 

Why would I want to do this?

JavaScript has made it easier for developers to create custom controls that go above and beyond the behavior that only a single html element can provide. 

Libraries such jquery ui widgets and kendo ui offer example of these controls.

Testing these controls can involve a few things:
	- Complex wait conditions
	- Knowledge of hidden html elements
	- Knowlegde of multiple html elements

This leads to duplication of this logic in across the test suite where these elements are used. 

Using a widget allows us to encapsulate this behavior and make our tests more readable.  


________________________________________________________________________________________________________________________________________

As a simple example, lets say we have a telephone number control.  #Maybe? Can I think of a better simple example

To start, we need to choose which html element that best represents the new control. 

Defining our new element. 

	class MyNewElemenet < PageObject::Elements::Div

	end

Regestering our new widget with PageObject 

	PageObject.register_widget(:my_new_element, MyNewElement, 'div')

Now you could define your new element in any of your page objects, as if it was any other html element

class MyPage
	include PageObject

	my_new_element(:new, :id => 'MyElement')

end

This will define two methods we can use with in our page, 

	new_element - this will return the element as usual

	new? - returns wheter or not the element exists

#This is a reminder that page object defines methods for us based on our elements names, it does not create variables that we reference. 


So now that we have struture for our new element, lets add some more behavior.


One way we can do this is by defining a class level method in our new elements class

class MyNewElemenet < PageObject::Elements::Div

	def self.accessor_methods(accessor, name)

		#defines an accessor method name= that allows you to set the value 
		accessor.send :define_method, "#{name}=" do |amounts|
	    	container = self.send("#{name}_element")
	        text_fields = container.text_field_elements

	        #this
	        text_fields.each_with_index do |text_field, index|
	        	text_field.value = amounts[index]
	        end

	        #or
	        text_fields.zip(amounts) do |text_field, amount|
	        	text_field.value(amount)
	        end
	    end
	end

end

The other way is by adding an instance method.

class MyNewElemenet < PageObject::Elements::Div

	def set(amounts)
	    #this
	    self.text_field_elements.each_with_index do |text_field, index|
	    	text_field.value = amounts[index]
	    end

	    #or
	    self.text_field_elements.zip(amounts) do |text_field, amount|
	    	text_field.value(amount)
	    end
	end

end


this would allow me to find my element dynamically on a page. (ex. in a table row)

table_row  = my_table[0]

table_row.my_new_element.set([867,5309])


If you want to dig into how pag object allows this behaviour it is located in the wigets.rb class. 

#metaprogramming #open/closed
