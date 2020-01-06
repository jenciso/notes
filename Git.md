## WRONG BRANCH NAME?

```
git branch -m wrong correct         
# Rename branch locally   
git push origin :wrong                  
# Delete the wrong branch   
git push --set-upstream origin correct   
# Push the new branch and set local branch to track the correct remote
```

## WHAT TO DO IF I FORGOT TO CHANGE BRANCH

```
git branch correctbranch
git reset --hard HEAD~1 
# Go back to previous commit
# (but remove all uncommitted work)
git checkout correctbranch
```

## Git flow resume

Create the repo:

    git checkout -b migracao_repos

if it exist, then:

    git checkout migracao_repos

To do all the changes:

    git merge migracao_repos
    git tag -a 1.6.2-RELEASE -m "Release - migração config-repo-prd para o TFS"
    git show 1.6.2-RELEASE
    git push origin 1.6.2-RELEASE


## Git Finding diff between current and last version?

```
git diff HEAD^ HEAD
```

## Git Submodules 

Add:

    git submodule add https://github.com/<user>/rock rock

Update:

    git submodule update --init --recursive

Clone a existent repo:

    git clone --recursive <project url>
  
https://blog.github.com/2016-02-01-working-with-submodules/

https://stackoverflow.com/questions/5828324/update-git-submodule-to-latest-commit-on-origin


## Changing Your Git Author Identity

globally
```
$ git config --global user.name "John Doe"
$ git config --global user.email "john@doe.org"
```
for one repo only
```
$ git config user.name "John Doe"
$ git config user.email "john@doe.org"
```
Changing the Author Information Just for the Next Commit
```
git commit --author="John Doe <john@doe.org>"
```

## Save credential for long time
Step 1: 
```
git config credential.helper store
git config --global credential.helper 'cache --timeout 31104000'
```
Step 2: Make a push and it will save
```
git push -u origin master
```
## Force git to overwrite local files on pull
```
git fetch --all
git reset --hard origin/master
```
or If you are on some other branch
```
git reset --hard origin/your_branch
```
To save olds commits creating a branch from master before resetting
```
git checkout master
git branch new-branch-to-save-current-commits
git fetch --all
git reset --hard origin/master
```
Source: http://stackoverflow.com/questions/1125968/force-git-to-overwrite-local-files-on-pull

## Add some comment or fix comment before of a git push 
```
git commit --amend -m "New commit message"
```

## Changing the message of a commit that you have already pushed to your remote branch
```
git push <remote> <branch> --force
# Or
git push <remote> <branch> -f
```

## Change repository URL

Example:
```
git remote set-url origin git@github.com:jenciso/notes.git
```

## Git Bash Prompt

Step 1: Download

```sh
cd ~
git clone https://github.com/jenciso/bash-git-prompt.git .bash-git-prompt --depth=1
```

Step 2: Add to following lines into `~/.bashrc`

```sh
## Bash-git
GIT_PROMPT_ONLY_IN_REPO=0
GIT_PROMPT_THEME=Single_line_Minimalist
source ~/.bash-git-prompt/gitprompt.sh
```

## Git - howto save temporal changes in your branches

To save:
```
git stash
```
To recover:
```
git stash pop
``` 


## Git - When you want to remove files from git commit 

```
git reset --soft HEAD^ 
```
or
``` 
git reset --soft HEAD~1
``` 

## how-to-fetch-all-git-branches

```
git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done
git fetch --all
git pull --all
```

Source: https://stackoverflow.com/questions/10312521/how-to-fetch-all-git-branches
