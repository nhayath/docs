[x] remove local branch
```
$ git branch -d branch_name
# force
$ git branch -D branch_name
```

- remove remote branch
```
$ git push origin --delete {the_remote_branch}
```

- remove a folder from my git repository without deleting it from my local machine
```
$ git rm -r --cached path_to_your_folder/
$ git add . && commit && push
```

- Overwrite local changes
```
git stash push --include-untracked
git stash drop
```
