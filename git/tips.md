# Git

### Staging files from the command line

````bash
git add -A  # stages ALL
git add .   # stages new and modified, without deleted
git add -u  # stages modified and deleted, without new
````




What's the difference between a standard merge and rebase? Does it affect our ability to roll back changes and/or cherry pick commits?

Is there a better branching strategy that we could be using? (yes, leading question)


What is the difference between git reset vs git revert?


- Reflog seems like a bit of a black box. Are there scenarios where I should be using / looking at reflog to recover some work or recover from a booboo quicker?
- Demo what's involved in contributing a good idea you have into an OSS project.... fork/pull request flow.



## References

- [Merge Versus Rebase](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
- [Checkout, Revert, and Reset](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting)
- [Reflog](https://www.atlassian.com/git/tutorials/refs-and-the-reflog)
