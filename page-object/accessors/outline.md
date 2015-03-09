
	The methods that are defined in the accessors module, enable us to ask questions about the elements we define in our PageObject.

	text_field(name, identifier={:index => 0}, &block)

	example usage:

	class MyPage
		include PageObject

		#this runs when Mypage.new is called!!!!
		text_field(:my_text_field, :id => 'myTextField')

	end

	Each element accesor dynamically defines a set of methods that include the name of the element you are defining.

	"#{name}", "#{name}=", "#{name}_element","#{name}?" are just a few examples. (Look in the Accessor.rb file to see what is defined for a certain element)

	It is also important to note, the methods that are defined are defined on the MyPage class. 
	
	This is why we are able to call on_page(MyPage).my_text_field from the step definitions. 

	## Further More (How is this accomplished)
	This is the case because the accessor methods are defined in a module within the PageObject module, 
	The PageObject module has been included in our MyPage class. (Ruby calls this a mixin)
	Therefore the context in which the methods are added, is the instance of the MyPage class. 
	Or more simply put self = the instance of MyPage. 
	##

	The most interesting method that is defined (in my opinion), is the "#{name}_element".

	This returns us an instance of the corresponding element class. (These classes can be found in the element folder in the PageObject Module)
	
	*In the example, my_text_field_element returns an instance of the PageObject::ElementsTextField class. 

	The preference is to us the defined methods from the accessors to enhance the readability of the code. 

	##Meaningful names

	This means the names we give our elements should be meaningful! 

	In the example I used the name :my_text_field, to put it blunt it is not a good name. 

	I should have been more descriptive about the behavior of that text field, what is its purpose. 

	Lets say we have a text field for a material we are adding to a price list! (We never have done that right?)

	A good name would be :material or :added_material, i think a better name would be material_to_add. Any other suggestions?