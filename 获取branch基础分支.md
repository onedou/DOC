refrence ![https://stackoverflow.com/questions/2255416/how-to-determine-when-a-git-branch-was-created](https://stackoverflow.com/questions/2255416/how-to-determine-when-a-git-branch-was-created)

![Pro Git § 3.1 Git Branching - What a Branch Is](https://git-scm.com/book/en/v1/Git-Branching-What-a-Branch-Is) has a good explanation of what a git branch really is

A branch in Git is simply a lightweight movable pointer to [a] commit.
Since a branch is just a lightweight pointer, git has no explicit notion of its history or creation date. "But hang on," I hear you say, "of course git knows my branch history!" Well, sort of.

If you run either of the following:

    git log <branch> --not master
    gitk <branch> --not master
    
you will see what looks like the "history of your branch", but is really a list of commits reachable from 'branch' that are not reachable from master. This gives you the information you want, but if and only if you have never merged 'branch' back to master, and have never merged master into 'branch' since you created it. If you have merged, then this history of differences will collapse.

Fortunately the reflog often contains the information you want, as explained in various other answers here. Use this:

    git reflog --date=local <branch>
    
to show the history of the branch. The last entry in this list is (probably) the point at which you created the branch.

If the branch has been deleted then 'branch' is no longer a valid git identifier, but you can use this instead, which may find what you want:

    git reflog --date=local | grep <branch>
    
Or in a Windows cmd shell:

    git reflog --date=local | find "<branch>"
    
Note that reflog won't work effectively on remote branches, only ones you have worked on locally.
