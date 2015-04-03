Working with table elements
	
	table(:jedi_knights, :id => 'jediKnights')

	jedi_knights_element => This will return an Enumerator. (Think of it as a way to get to an array of table rows).

	This allows us to use the methods defined in the Enumerator and Enumerable modules.

	These methods are very powerful, they accept a block that operates on each element in the enumerator. 

	This keeps us from writing for loops, and allows our code to be less complex and more readable. (Of course this is my opinion!!! )

	The funny thing is that a page object table row, is also an enumerator. But this time it is an enumerator if Table Cells. 

	So really, it is an array of arrays. 

	table_element.map{|row| row.map{|cell| cell.text}} => 
	[
		["Column One Header", "Column Two Header"],
		["Row One Column One Text","Row One Column Two Text"],
		["Row Two Column One Text","Row Two Column Two Text"]
	]







	
