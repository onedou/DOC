### 使用命令git branch --set-upstream ;实例如下，其中debug为创建的分支

    git branch --set-upstream master origin/master  

### The --set-upstream flag is deprecated and will be removed. Consider using --track or --set-upstream-to

In Git a branch that tracks (or has an upstream) has the an entry similar to the following in the repository config file (/.git/config)

        [branch "feature"]
                remote = origin
                merge = refs/heads/feature
        The "--set-upstream" option you have been using has been directly replaced with "--track"

so

        git branch --track feature origin/feature
        
additionally there is a new syntax like

        git branch --set-upstream-to=origin/feature feature
        
and also

        git branch -u origin/feature feature
        
All forms function identically.

## Find out which remote branch a local branch is tracking

Here is a command that gives you the tracking branch:

        git branch -vv

You have to wade through the SHA and any long-wrapping commit messages, but it's quick to type and I get the tracking branches aligned vertically in the 3rd column.


