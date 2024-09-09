Prerequisites: You know of the website https://alice-doc.github.io/alice-analysis-tutorial/building/ and you followed the instructions

To update O2Physics, you have to do the following:
`cd ~/alice`
`cd O2Physics`

check that the branch you are on is the one you want to update, I like to keep a master branch without any local modifications

`git checkout master`
`git pull`

--> It will get all of the commits and your repository will be up-to-date

You can then create another branch with updated content (which you can later modify while keeping the master branch clean) by doing
`git checkout -b mybranch`

Finally, to update your O2Physics, you need to build this branch with aliBuild
`cd ~/alice`
`aliBuild build O2Physics`
## Common build problems

While building with aliBuild, many problems can occur. Those problems are often specific to your OS and architecture and therefore there is no "one size fits all" solution.
To solve most of them, follow the instructions below
1. Make sure you updated alidist by doing `cd alidist` and `git pull --rebase`
3. See if any other user has the same issue on https://alice-talk.web.cern.ch/ , on mattermost in the O2 Analysis channel https://mattermost.web.cern.ch/alice/channels/o2-analysis, or in email threads
4. Have a look at the log file of the error and always search for the issue on the internet, many issues are not specific to the ALICE softwares and are more general
5. If you have a mac, have a look at the solutions in the next section
6. If everything fails, you can send an email to alice-project-analysis-task-force@cern.ch or make a ticket in Alice Talk explaining your issue and detailing your OS

### Specific solutions and tips for macOS:
1. Make sure you updated the dependencies:
	Update brew
	`brew update`
2. Reinstall the required packages using the instructions https://alice-doc.github.io/alice-analysis-tutorial/building/custom.html#prerequisites
3. Make sure you don't have different versions of packages, it can cause issues: list the packages you have by doing `brew list`, uninstall the double that you think might be responsible of the error

## ~/alice takes too much disk space, help

If your ~/alice repository takes a lot of space, it is probably because you have a lot of obsolete builds that are now useless.
It can especially happen if like me you create a new branch each time you want to develop something, and then you build O2Physics in this branch.
If none of the previous builds will be useful to you, you can simply delete the ~/alice/sw/BUILD folder. 
Note: I tried to delete the ~/alice/sw folder once but it created a git issue in my O2Physics branches and I had to reupload some files to sw/MIRROR
## Oops I made some modifications on the master branch and I should not have

```
git fetch upstream
git reset --hard upstream/master
```

If you want to save your current branch's state before doing this (just in case), you can do:

```
git commit -a -m "Saving my work, just in case"
git branch my-saved-work
```

then `git checkout master`and do the reset