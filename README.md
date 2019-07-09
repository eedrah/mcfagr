# McFagr
Move Commits From Another Git Repository

![Thing](./mcfagr.jpeg)

Imagine you have two projects who do not share a git history, but have some relationship. For example: 
- perhaps you joined a company that did not have version control until recently, and were making manual changes to their Dev, Stage and Production environments and source code, treating them like separate git repositories. And now, as the git expert, you have the job of fixing the mess.
- maybe you and a friend were working on similar things, sharing files here and there, but you were each keeping your own git repositories. But now you realise they should be under one roof and you want to bring each version into the same repo.

_You are in a bit of a pickle, really._ You could just ignore git, copy over the files from a repo into another, manually examine each file (or use `diff`/`git diff`), do any merges required, and commit everything together. This is fine - we've all been there. Sometimes that's just the easiest way to move on. But sometimes the disadvantages cost you:
- You lose all git history from one or both of the repos, losing the logs, diffs, reasons for change, and who did them.
- You have to merge files manually, and this place of merging (at the two current versions of each file) might not be the best spot to merge.
- There might just be too many thousands of files to merge.

But perhaps sometime in the past these files were identical - perhaps the result of a long forgotten `rsync`? Or maybe they were a lot closer then they are now. Of course, finding this place is difficult. It might even be different for each file. But if you can find it and fix a much smaller or non-existent merge conflict there, you can have git automatically apply merges from that point on and save yourself a monstrosity of a merge conflict "later" - i.e. at the current version of the files.

# A solution

Call the two repos `repoA/` and `repoB/`. For each shared file, let's calculate the number of differences between the two. For example, the number of added and deleted lines between `repoA/file-1` and `repoB/file-1` might be 21. (You also should ignore whitespace.)

If we go back 2 commits in `repoA`, when comparing `repoA/file-1`@`{2 commits ago}` and `repoA/file-1`@`{current version}` there are only 10 line differences. It's easier to mannually merge here, and then use git to apply automatically the 2 commits worth of changes found in `repoA`.

But let's say we can go back 4 commits in `repoB`, in addition to the 2 commits in `repoA`. Now you find the files are identical (between `repoA/file-1`@`{2 commits ago}` and `repoB/file-1`@`{4 commits ago}` ). You can get git to merge these file histories automatically and then apply the 2 and 4 commits of changes automatically. *No manual merges are required.*

All we need is something to find us these locations for us (for every commit in `repoA`, every commit in `repoB` and every shared file), do the merges, let us fix up anything interactively, and then continue applying the commits to that file from there.

# The script

[The bash script in this repo](./mcfagr) has the results of toying around with my algorithm. Now you understand what the goal is, so I expect you can step through and use what you need. I don't expect people to run it blindly.

There's a few 'gotchas' on the way - e.g. after merging you will want to apply commits only to some files and not to others (there are git commands for this).

I successfully saved one of my employers about 2 months of manual merging and testing on thousands of files by this process. However, the script is left in the condition it was in when I achieved what I needed, so is somewhat incomplete - I welcome pull requests.

I can't imagine many people are in this situation, but if you are, I hope this helps - You can open an issue and let me know 😊

# Other stuff I needed to write down somewhere

Options:
--remote-folder-prefix - Doesn't exist yet. If there is sufficient interest I can add one or accept a pull request. This would accept a folder where the files are kept in the remote repository, in case that the local repo has the files in the root, while the remote repo has the files in a subdirectory with a different name.

