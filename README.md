# git-cheatsheet

#### git topic/story branch workflow ####
git maintainers maintain git with this topic branch workflow https://stuff.mit.edu/afs/sipb/project/git/git-doc/howto/maintain-git.html

#### how to checkout lsweb ####
1. manually upload your ssh public key to your gitlab profile here https://gitlab.com/profile/keys
 ```
 cat ~/.ssh/id_dsa.pub | pbcopy
 ```

2. clone lsweb and checkout LS_Stable_v3.0
 ```
 git clone git@gitlab.com:streambox/lsweb.git
 cd lsweb
 git branch --all # inspect what branches where just cloned (for shits and giggles)
 git checkout --track remotes/origin/LS_Stable_v3.0

 ```

#### workflow for a story branch ####
On laptop:
 ```
 # create a personal/temporary integration branch (potential updates).
 # We will use this integration branch later.
 cd lsweb
 git checkout -B lancepu origin/LS_Stable_v3.0
 git push --set-upstream gitlab lancepu

 # fork off stable branch:
 git checkout -B card71 origin/LS_Stable_v3.0
 git push --set-upstream gitlab card71
 ```

On server (ssh t3 for example):
 ```
 cd /c && git fetch && git reset --hard origin/card71
 ```

On laptop:
 ```
 # make edits to php
 git commit -am "Keep column widths static when adding serial numbers"
 git push
 ```

On server:
 ```
 cd /c && git pull
 # test changes using browser
 ```

On laptop:
 ```
 # make edits to php
 git commit -am "Keep column widths static when adding serial numbers"
 git push
 ```

On server:
 ```
 cd /c && git fetch && git reset --hard origin/card71
 ```

...repeat edit/test cycle until you're reasonably confident card71 is solved.

On laptop:
 ```
 # checkout your personal integration branch and merge your changes
 git checkout lancepu
 git merge card71
 ```

On server do final check of the integration branch which has the card71 story (and possibly other stories)
 ```
 cd /c && git fetch && git reset --hard origin/lancepu
 ```
#### in trello.com, how can you see the card numbers? ####
https://trello.com/c/PkIrgKzd/36-show-card-numbers

### how can I squash/condense the commits on my story branch? ###
 ```
 cd lsweb
 git checkout card71
 # move/rebase my card71 commits onto the tip of stable LS_Stable_v3.0
 git rebase LS_Stable_v3.0
 # show me the commits on this card71 branch:
 git log --decorate --pretty=tformat:'%h %d %ar %s' master...card71
 sha1=$(git merge-base LS_Stable_v3.0 card71)
 git reset --soft $sha1
 git commit --amend
 git push --force
 ```

### how can I clone and work on the the BCE prototype UI project? ###
Upload your ssh public key to gitlab (same step as step 1 in [how to checkout lsweb](#how-to-checkout-lsweb))
 ```
 # Work on master branch to simplify workflow since we're not even at
 # version 1.0 yet.  Normally though, working on master branch is BAD,
 # since mutual agreement is master is stable.  

 # When we think we're somewhat stable then we can squash master and
 # start forking topic branches from master.

 git clone git@gitlab.com:streambox/bcewebui.git
 cd bcewebui
 git checkout --track origin/master
 # verify what branch I'm on now
 git branch

 # edit, edit, edit
 git commit -am "Fix stuff"
 git push

 # realize need to fix more stuff...edit, edit...think...edit, edit...oh
 # duh! done
 git commit -am "Fix more stuff"
 git push

 # check for upstream changes from other humans on master branch (my
 # current branch)
 git fetch

 # check what upstream humans changed to see how they affected master
 # branch:
 git diff --color ..origin/master

 # their changes look ok, merge the changes from upstream to my local
 # master
 git merge origin/master
 git push
 ```

#### recomended stuff ####
1. tig shows history really nicely
 ```
 # osx:
 brew install tig
 ```