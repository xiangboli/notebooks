# Useful Git Commands

## Create and add SSH Key

```bash
ssh-keygen -t rsa -C “your-email-address”
```
* -t stands for ‘type’ and rsa is type of encryption
* -C is for comment

Name the key:
```bash
.ssh/id_rsa_IDENTIFIABLE_NAME_HERE
```
Press "Enter" twice:
```
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```
Copy the pub key:
```bash
cat .ssh/id_rsa_IDENTIFIABLE_NAME_HERE
```

Navigate to your GitHub account → Settings → SSH and GPG keys, and paste above pub key.

Add this key in terminal:
```bash
ssh-add -D  //delete all keys
ssh-add .ssh/id_rsa_IDENTIFIABLE_NAME_HERE
```
## Common Git Steps
Create a repository on github, copy the `repo_url`. Clone to local:
```bash
git clone repo_url
```
Before make changes, try to create a separate branch:
```bash
cd repo
git checkout -b branch_name
```
After making some changes of the repo, push the changes:
```bash
git add .
git status // check what have changed
git commit -m "comments"
git push origin branch_name
```
Review the changes, if everyting is good, merge to master branch.

## Discard the changes 
Discard all local changes, but save them for possible re-use later:
```bash
git stash
git stash drop
```
Discarding local changes (permanently) to a file:
```bash
git checkout -- file
```
Discard all local changes to all files permanently:
```bash
git checkout .
```
Discard all changes from last commit:
```bash
git reflog
git reset --hard HEAD@{<reflog index number>}
```

### unable to access, 403 issue
1. edit `.git/config` file under your repo directory
2. find url=entry under section [remote "origin"]
3. change it from `url=https://MichaelDrogalis@github.com/derekerdmann/lunch_call.git` to `url=ssh://git@github.com/derekerdmann/lunch_call.git`. that is, change all the texts before @ symbol to ssh://git
4. Save config file and quit. now you could use git push origin master to sync your repo on GitHub

or 
```bash
git remote set-url origin "https://github-username@github.com/github-username/github-repository-name.git"
```

or
`git clone ssh_url instead of https`

or
add the following line in the ~/.gitconfig
```
[url "git@github.com:"]
  insteadOf = https://github.com/`
```

# switch among multiple accounts
1. create ssh key and add on github ([as shown above](#create-and-add-SSH-key))
2. create configuration files to manage keys
```bash
$ touch config
```
add 
```
# Personal account - default config
Host github.com
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_personal
# Work account
Host github.com-work
   HostName github.com
   User git
   IdentityFile ~/.ssh/id_rsa_work
```
3. edit gitconfig
```bash
$ cd ~
$ nano ~/.gitconfig
```
add
```
[url "git@github.com:"]
      insteadOf = https://github.com/
[user]
	name = username
	email = username@domain.com
[includeIf "gitdir:~/work/"]
    path = ~/work/.gitconfig
```
4. create work gitconfig
```
$ nano ~/work/.gitconfig
```
add
```
[user]
	name = username
	email = username@domain.com
```




## Work with remote
Show remotes:
```bash
$ git remote -v
origin	https://github.com/xiangboli/notebooks.git (fetch)
origin	https://github.com/xiangboli/notebooks.git (push)
```
Add remote:
```bash
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
```bash
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
```bash
git checkout -b branch_name
```
Show all branches:
```bash
git branch
```
Delete a branch
```bash
git branch -D branch_name
```