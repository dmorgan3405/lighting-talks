#Hotfix Branching

- Checkout master, and pull the latest.

	git checkout master

	git pull

- Branch from master. (ie. hotfix_branch_for_10202014)

	git checkout -b hotfix_branch_for_10202014

- Make necessary changes and commits.

- Checkout master and merge your hot fix changes.

	git checkout master 

	git merge hotfix_branch_for_10202014
	
- Checkout and pull the latest from develop. Then go back to the hotfix branch. 

	git checkout develop

	git pull

	git checkout hotfix_branch_for_10202014

- Squash micro commits to single Hotfix Commit
	
	8934e8cb44864628cf45dcd988b3084dfd6bb2b7 Hot Fix Commit Two
	
	76cbb597c4d4fdb94b08d3acd3642347d7d2142b Hot Fix Commit One
	
	...
	
	git rebase -i develop
	
	db5b16ce41cfd9038b8a7025e2c2acb40639a786  Hotfix Commit: Added lines to the readme file.
	
	...
	
- Merge and push hotfix branch into develop
	
	git checkout develop

	git merge hotfix_branch_for_10202014

	git push origin develop


***Once CI and Dev Builds are green***

- Merge hotfix branch into master, and push master

	git 

This results in the same commit for the hotfix being on develop and master and will easily allow for a fast-forward merge in the next deployment. 

