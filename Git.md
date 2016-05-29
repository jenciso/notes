##Save credential for long time
Step 1: 
```
git config credential.helper store
git config --global credential.helper 'cache --timeout 31104000'
```
Step 2: Make a push and it will save
```
git push -u origin master
```

