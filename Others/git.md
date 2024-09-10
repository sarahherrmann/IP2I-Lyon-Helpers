# git

## Push a modification to the official repository

Example : I modified AnalysisDataModel.h in ``~/alice/O2/Framework/Core/include/Framework/``

Under this directory if you don't have your own branch and remote do : 
```
git remote add sarah https://github.com/sarahherrmann/AliceO2
git checkout -b branchSarah
```

:::info
The link ``https://github.com/sarahherrmann/AliceO2`` must point to your fork of AliceO2 (or any other repository in which you are working), so you have to fork it before doing that
:::

```git checkout -b branch``` is to add a new branch named "branch". To only switch to an existing branch do ``git checkout branch``

To see what you modified do ```git status```
Here in our example it returns :

```
On branch branchSarah
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   AnalysisDataModel.h

no changes added to commit (use "git add" and/or "git commit -a")
```

You modified `AnalysisDataModel.h` and now you want to commit these changes, to then make a pull request under the official AliceO2 repository. 

You then have to do : 

```C
clang-format -style=file -i <file>
```
This ensures that you have the right clang format for your pull request, and then you can add, commit your changes and push.

For example here I would do :

```git
clang-format -style=file -i AnalysisDataModel.h 
git add AnalysisDataModel.h 
git commit -m "Some message here"
```
 
And finally ```git push sarah branchSarah```

Now you can go on github and make a pull request.



## git pull from dev

under the branch dev3 you can do `git pull upstream dev` to update dev3

## Infos

If you modify locally a file (no matter on which branch), and you do ``git pull`` it will NOT reverse your modifications, but it will update the repository


If you are under your own branch and you want to update O2Physics (for example), you have to to, under `branchSarah`: `git fetch upstream`



:::danger
If you are under branchSarah and you do your update of O2Physics in this branch, to enter the environment, the command is not the same, it is:

`alienv enter O2Physics/latest-branchSarah-o2`
:::


herrmann@lyoext081 O2Physics % git branch
* branchSarah
  master
herrmann@lyoext081 O2Physics % git checkout master
error: Your local changes to the following files would be overwritten by checkout:
	PWGMM/Tasks/CMakeLists.txt
Please commit your changes or stash them before you switch branches.
Aborting
herrmann@lyoext081 O2Physics % git pull sarah branchSarah
hint: Pulling without specifying how to reconcile divergent branches is
hint: discouraged. You can squelch this message by running one of the following
hint: commands sometime before your next pull:
hint: 
hint:   git config pull.rebase false  # merge (the default strategy)
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint: 
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
From https://github.com/sarahherrmann/O2Physics
 * branch            branchSarah -> FETCH_HEAD
Already up to date.
herrmann@lyoext081 O2Physics % git fetch upstream
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 5 (delta 4), reused 4 (delta 4), pack-reused 0
Unpacking objects: 100% (5/5), 858 bytes | 122.00 KiB/s, done.
From https://github.com/AliceO2Group/O2Physics
   d31224d..40641b6  master     -> upstream/master
herrmann@lyoext081 O2Physics % git pull sarah branchSarah
hint: Pulling without specifying how to reconcile divergent branches is
hint: discouraged. You can squelch this message by running one of the following
hint: commands sometime before your next pull:
hint: 
hint:   git config pull.rebase false  # merge (the default strategy)
hint:   git config pull.rebase true   # rebase
hint:   git config pull.ff only       # fast-forward only
hint: 
hint: You can replace "git config" with "git config --global" to set a default
hint: preference for all repositories. You can also pass --rebase, --no-rebase,
hint: or --ff-only on the command line to override the configured default per
hint: invocation.
From https://github.com/sarahherrmann/O2Physics
 * branch            branchSarah -> FETCH_HEAD
Already up to date.
herrmann@lyoext081 O2Physics % git remote
sarah
upstream
herrmann@lyoext081 O2Physics % cd ..
herrmann@lyoext081 alice % aliBuild build O2Physics --defaults o2 --debug







## Tutorials to watch or rewatch

git merge branches and conflicts [here](https://www.youtube.com/watch?v=XX-Kct0PfFc)

git forks and pull requests [here](https://www.youtube.com/watch?v=nT8KGYVurIU)

Fork: when you don't have the rights to a repository, it is used to make a copy of the code in your own repository, so that you are able to change this code and later on make a PR and merge it


git fetch : different from git pull because it enables you to retrieve the new branches created on the onlie repository that you don't necesserally have

[Oh Shit Git](https://ohshitgit.com/)

### Git Forking & Fetch: How to Keep your Fork in Sync with an Upstream Repository

Youtube tutorial [here](https://www.youtube.com/watch?v=deEYHVpE1c8)

- upstream : the original remote where you forked from
- sarah : (for me) my forked repository
    - Branch master
    - Branch branchSarah

What to do to keep your fork up to date ?

In the branch you are working on, do

`git fetch upstream`

---

Me: I do that and after I do `git pull`
If I am in branch master, it will pull  master branch content in the remote upstream to my local branch master

--> NO it will pull the master branch content in the remote **sarah** to my local master branch

And this is not what we want. We need to update the online repository `sarah` first

---

#### Let's say I want to update my branch master in my fork 

What I think I need to do:

1. Go to my [fork of O2Physics](https://github.com/sarahherrmann/O2Physics/tree/master)
2. Click on `Fetch upstream` -> It updates the online fork remote (**sarah**)
3. Go to my local repository in branch master
4. `git pull` should pull my remote (now up to date) master branch into my local master branch

:::danger
BUT, if there are conflicts on my branch and I can't do step 2, then I should probably follow the tutorial. 
:::


git pull sarah master
git push sarah master


TRES IMPORTANT: avant de mettre à jour O2 ou O2Physics vérifier avec git status qu'il n'y ait pas de untracked files car sinon on ne peut pas faire git pull --rebase et ce n'est pas mis à jour

#### Update a local branch that does not exist online

Let's say I want to update a branch named branchMFTjet that is only available locally, and does not correspond to an official branch on the upstream remote. 

First of all you need to push the local branch to your remote repository (the fork sarah) if this branch does not exist online yet:

`git push sarah branchMFTjet`

Now your branch **branchMFTjet** exist on the online repository (of your fork), you can select it in the top left corner of your fork page on github, and you then have to update your fork by clicking on : `Fetch upstream` in the top right corner. 

![](https://codimd.web.cern.ch/uploads/upload_852c55ce2c706006d4c429a453dca9ea.png)


This will fetch the upstream remote and merge it with this online branch. 
To update your local branch you then have to do : 
`git pull sarah branchMFTjet`




#### Comments
Let's say you created locally a branch named branchMFTjet. You made some changes in it. To keep this branch up to date to the official upstream remote you can't do 
```
git pull upstream branchMFTjet
``` 
because the branch branchMFTjet does not exist in the upstream remote. 



### Good git practices

Never make any change in the original branch (master in O2Physics, dev in O2), this will lead to problems when creating new features. 

Let's say the original online remote is named ==upstream== and the original branch is named ==master==

If by mistake you did a change in the master branch, you can solve this by:

First of all add your changes, don't keep untracked files, and then, on your master branch do
`git checkout -b master-backup`

Then, go back to your master branch
`git checkout master`
then, fetch all on this branch:
`git fetch upstream master`
And reset it,
`git reset upstream/master --hard`


## Setting up your own git repository

https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github


## Debugging

If alibuild O2 doesn't work, you can try :
1. Update Xcode
2. cd alidist && git pull --rebase
3. Make sure your XCode dev tools are installed properly -- open XCode and see if it prompts you to install them. If so, click "install".

Diana's solution to a lib problem

1. Uninstall Xcode
2. Do [this](https://mac.install.guide/commandlinetools/6.html) to uninstall the command line tools
3. Re-install Xcode
4. Re-install alibuild with `brew reinstall alisw/system-deps/o2-full-deps alisw/system-deps/alibuild`

https://developer.apple.com/download/all/?q=command%20line%20tools

If `brew doctor` gives any warning, try to solve them



