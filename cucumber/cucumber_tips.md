#tips

#Comparing a list of unordered values

```ruby
Scenario: Comparing a list of actual versus expecting
	Then I should see the following suggestions:
		| Suggested Search Term |
		|	Sky Blue Green		|
		|	Emerald Green		|
		|	Olive Green			|
```

##A good way to assert.

```ruby
Then(/^I should see the following suggestions:$/) do |suggestions_table|
	suggestions_table.hashes.each do |suggestion_hash|
		on_page(MySearchPage).search_suggestions.should include suggestion_hash['Suggested Search Term']
	end
end
```
##A good way to assert.

```ruby
Then(/^I should see the following suggestions:$/) do |suggestions_table|
    on_page(MySearchPage).search_suggestions.should =~ suggestions_table.rows.flatten
end
```

#Breaking down that example

Calling Cucumber::AST:Table.rows.

```ruby
suggestions_table.rows
```

Turns this

```ruby
 | Suggested Search Term |
 | Sky Blue Green        |
 | Emerald Green         |
 | Olive Green           |
 ```

Becomes....

```ruby
[['Sky Blue Green'],['Emerald Green'],['Olive Green']]
```

Notice, that the it is an Array of Arrays (or a multi-dimensional array if you want to be fancy)

Calling flatten removes the layers from the array, so it will become an array of strings: ['Sky Blue Green','Emerald Green','Olive Green']

So what happens when the arrays don't match?

When you have an extra item in the expected array:
RSpec::Expectations::ExpectationNotMetError: expected collection contained:  ["Emerald Green", "Sky Blue Green"]
actual collection contained:    ["Emerald Green", "Olive Green", "Sky Blue Green"]
the extra elements were:        ["Olive Green"]

When you are missing an item from the expected array:
RSpec::Expectations::ExpectationNotMetError: expected ["Sky Blue Green", "Emerald Green"] to include "Olive Green"

