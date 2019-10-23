# Useful Git Commands

## Create & add SSH Key

```
ssh-keygen -t rsa -C “your-email-address”
```
* -t stands for ‘type’ and rsa is type of encryption
* -C is for comment

Name the key:
```
.ssh/id_rsa_IDENTIFIABLE_NAME_HERE
```
Press "Enter" twice:
```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
Copy the pub key:
```
cat .ssh/id_rsa_IDENTIFIABLE_NAME_HERE
```

Navigate to your GitHub account → Settings → SSH and GPG keys, and paste above pub key.

Add this key in terminal:
```
ssh-add .ssh/id_rsa_IDENTIFIABLE_NAME_HERE
```
## Common Git Steps
Create a repository on github, copy the `repo_url`. Clone to local:
```
git clone repo_url
```
Before make changes, try to create a separate branch:
```
cd repo
git checkout -b branch_name
```
After making some changes of the repo, push the changes:
```
git add .
git status // check what have changed
git commit -m "comments"
git push origin branch_name
```
Review the changes, if everyting is good, merge to master branch.

## Discard the changes 
Discard all local changes, but save them for possible re-use later:
```
git stash
git stash drop
```
Discarding local changes (permanently) to a file:
```
git checkout -- file
```
Discard all local changes to all files permanently:
```
git checkout .
```
Discard all changes from last commit:
```
git reflog
git reset --hard HEAD@{<reflog index number>}
```

## Work with remote
Show remotes:
```
$ git remote -v
origin	https://github.com/xiangboli/notebooks.git (fetch)
origin	https://github.com/xiangboli/notebooks.git (push)
```
Add remote:
```
$ git remote
origin
$ git remote add bill https://github.com/bill/notebooks.git
$ git remote -v
origin	https://github.com/xiangboli/notebooks.git  (fetch)
origin	https://github.com/xiangboli/notebooks.git  (push)
bill	https://github.com/bill/notebooks.git (fetch)
bill	https://github.com/bill/notebooks.git (push)
```
Now you can use the string `bill` on the command line in lieu of the whole URL. For example, if you want to fetch all the information that Bill has but that you don’t yet have in your repository, you can run `git fetch bill`:
```
$ git fetch bill
remote: Counting objects: 43, done.
remote: Compressing objects: 100% (36/36), done.
remote: Total 43 (delta 10), reused 31 (delta 5)
Unpacking objects: 100% (43/43), done.
From https://github.com/bill/notesbooks
 * [new branch]      master        -> bill/master
 * [new branch]      notebooks     -> bill/notebooks
```
Bill’s master branch is now accessible locally as bill/master — you can merge it into one of your branches, or you can check out a local branch at that point if you want to inspect it. 

## Work with branch
Create a new branch:
```
git checkout -b branch_name
```
Show all branches:
```
git branch
```
Delete a branch
```
git branch -D branch_name
```