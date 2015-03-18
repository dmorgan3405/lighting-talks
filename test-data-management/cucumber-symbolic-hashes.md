#Cucumber::Ast::Table#symbolic_hashes

The Problem
Late last year my current team's cucumber test suite was growing at a consistent rate. (Shibby!) Except for one thing, we were creating a bit of a mess in the code that was responsible for arranging our test data.

Simply put: We were writing too much test data management code. 

For Example...

```ruby
Feature: Edit Employee Records
 
Scenario: One
  Given the following employees exist:
        | First Name | Last Name | Position        |
        | Jane       | Wayne     | VP of Marketing |
 
Scenario: Two
  Given the following employees with middle names exist:
        | First Name | Middle Name | Last Name | Position        |
        | Jane       | Elaine      | Wayne     | VP of Marketing |
```

```ruby
Given(/^the following employees exist:$/) do |employee_table|
  employee_table.hashes.each do |employee_hash|
    EmployeeFactory.create(employee_hash['First Name'],
                            employee_hash['Last Name'],
                            employee_hash['Position'])
  end
end
 
Given(/^the following employees with middle names exist:$/) do |employee_table|
  employee_table.hashes.each do |employee_hash|
    EmployeeFactory.create_with_middle_name(employee_hash['First Name'],
                                           employee_hash['Middle Name'],
                                           employee_hash['Last Name'],
                                           employee_hash['Position'])
  end
end
```

```ruby
class EmployeeFactory
 
  def self.create(first_name,last_name,position = "Software Developer")
    position_id = Position.find_by_name(position).position_id
    FactoryGirl.create(:employee,  first_name: first_name, 
                                    last_name: last_name, 
                                  position_id: position_id)
  end
 
  def self.create_with_middle_name(first_name, last_name,
                                    middle_name, position = "Software Developer")
                                    
    position_id = Position.find_by_name(position).position_id
    FactoryGirl.create(:employee,  first_name: first_name, 
                                    last_name: last_name, 
                                  middle_name: middle_name 
                                  position_id: position_id)
  end
 
  #...etc
  #It seemed like every permutation of properties had a method.
 
end
 
FactoryGirl.define do
  factory :employee do
    first_name 'John'
    last_name 'Doe'
    position_id 1
  end
end
```

*Please forgive the overly simple example, but I wanted to express the value that I see in this approach with a concrete example, and couldn't use an example from the code base I am currently working in.*

The issues I saw with this approach/pattern were: 
The default values for the factory were divided between two places.
Looking up the position in the EmployeeFactory.create method forced this.
The mapping of ["Key"] to [:key] seemed to be redundant. 
The number of methods needed to support a single table seemed too high. 
It was quickly becoming a mess, and hard to understand. 

##A New Approach

After fumbling through multiple approaches, and talking it through with teammates (Shout out to @RandyEppinger and @Apprentice), I finally found an approach that I thought would work.

I added a method to the Ast::Table class in Cucumber, similar to the existing hashes method, except that it mapped all the hash keys to symbols instead of strings (hence the name, symbolic hashes).

```ruby
class Cucumber::Ast::Table
 
  def symbolic_hashes
    @header_conversion_proc = lambda {|h| symbolize_key(h)}
    @symbolic_hashes ||= build_hashes
  end
 
  private
 
  def symbolize_key(key)
    key.downcase.gsub(' ','_').to_sym
  end
 
end
```

At first it seemed to be a small change, but it has become a very powerful tool for our team. It allows us to write step definitions that pass each "symbolic_hash" directly to the factory layer for creation. Also, by taking advantage of FactoryGirl's transient attributes, the factory itself can have control all default values for necessary columns.

Refactored scenarios:

```ruby
Feature: Edit Employee Records
 
Scenario: One
  Given the following employees exist:
    | First Name | Last Name | Position        |
    | Jane       | Wayne     | VP of Marketing |
          
Scenario: Two
  Given the following employees exist:
    | First Name | Middle Name | Last Name | Position        |
    | Jane       | Elaine      | Wayne     | VP of Marketing |
 ```
 
 
 ```ruby
Given(/^the following employees exist:$/) do |employee_table|
  employee_table.symbolic_hashes.each do |employee_hash|
    EmployeeFactory.create_from(employee_hash)
  end
end
```

```ruby
class EmployeeFactory
 
  #now only one method is needed
  def self.create_from(overrides = {})
    FactoryGirl.create(:employee, overrides)
  end
 
end
 
FactoryGirl.define do
  factory :employee do
    ignore do
      position 'Software Developer'
    end
 
    first_name 'John'
    middle_name 'George'
    last_name 'Doe'
    position_id {Position.find_by_name(position)}
  end
end
```

Using this approach, over the past ~six months, has allowed my current team to:
Write clean and reusable cucumber step definitions.
Write only a very thin factory layer, by taking full advantage of the functionality of Factory Girl.
In my opinion, the flexibility of this approach has allowed our team to add scenarios quicker and easier. Thus far it also seems to be a very flexible and extensible solution to our problem. 

Drawbacks that I have seen so far:
Promotes tight coupling of cucumber table headers to database column names.
Team members need to understand how lazy and transient attributes work in FactoryGirl. 
I welcome feedback on this approach, and would love to hear about your approaches and patterns for managing test data.
