#Preventing Inconsistencies

A list of things to keep in mind. Not RULES. 

## Dealing with javascript heavy ui-controls

element.when_visible seems to be more restrictive then element.when_present

* Important for Kendo UI Modals

## Using the Page Object Api

Beinging familiar with the PageObject api and using the provided methods.

Example from code:

wait_until { my_element.attribute('disabled') == nil} 

Some assumptions were made. 
* When the attribute disabled does not exists, that it would return nil. 
* It actualy will return empty string. 

Use:
wait_until { my_element.disabled? }

It it a little simpler and also it also protects us as developers from having to know what the underlying watir driver is actually returning. 

## Leveraging Widgets 

Widgets allow us to encapsulate generic behaviour of ui controls. 

For example, always clicking a kendo select list opener and waiting for the options container to be visible before you select a value. 

Some things to keep in mind when:

* Adding or changing behaviour of a widget can have far reaching effects. 
	* Is this generic behaviour for this control?
	* Or is this behaviour unique to the page I am working on?

* Creating a new widget vs editing an existing widget should be a thought out indevaour. 
	* Overgeneralizing a widget, can lead to complex logic. 
	* Creating new widgets can lead to duplication. 

## Accesing table values

Try to only access the values once per step. 

It is much faster to get the values, filter the values, and compare the values. 

Than, search the table for a specific value, then compare the values. 

Using the == [order matters] and ~= [order does not matter] rspec matchers can help with this. 

Example. 

```ruby
some_cucumber_table.symbolic_hashes.each do |row|
	on_page(MyPage).get_value_for_x_from_table(row[:my_key]).should == row[:my_expected_value]
end
```

This could be written as such. 

```ruby
on_page(MyPage).get_table_values.should == some_cucumber_table.rows
```

## Wait For Knockout

Mockey Patched PageObject to include a wait_for_knockout method. 

page.wait_for_knockout('NOTIFY_KEY')

After applying bindings, in the javascript side of things simply call notifyKnockoutCompleteFor('NOTIFY_KEY')

Would a better design prevent the need for this?

## Questions

* Can we take more advantage of page_populator?
* Would a button widget that always waits_for_ajax on click be nice?
	* ajax_button?
	* async_event_button?