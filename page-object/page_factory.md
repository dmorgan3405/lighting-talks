	#on_page 
		- on_page(MyPage) 
			This returns an instance of the class MyPage. 
		- on_page(MyPage) do |page|
			#page is an instance of the class MyPage
		  end
		  	This executes the block (do end or {}) you define in the context of a new instance of the page. 
	#visit_page
		- Does the same thing as on_page, however it uses the defined page_url to navigate the browser to that page first.

	
