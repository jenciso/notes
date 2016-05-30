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
