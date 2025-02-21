# What is git?
A stupid content tracker.  A persistent hash map that smells like a file system.

# Special Locations / Terms

## HEAD / Detached HEAD
`HEAD` is where you're at _right now_ in the repository and usually points to a branch.

If it's pointing at a branch, and you `commit`, then `HEAD` kind of "follows along" and remains pointing at the branch.

If it's not pointing to a branch (maybe a specific commit or a tag), you're in a "detached HEAD" state, and your commits will be at risk of loss
## workspace / working directory
This is the folder on disk where you do your development work.

Git *DOES NOT CARE* about your working directory; anything in here is free-game to be nuked from orbit.

## index
When you're satisfied with the state of a file, you "stage changes" into the index.  
This prepares the file to be committed.
## repostiory
This is the `.git` folder; it contains the entire history of your project and allows for fast switching between versions.

## remote
A copy of your repostiory on another machine.



# What's up with that .git folder?

`.git` inside of your workspace stores everything that git cares about in relation to your project.

Exploring this folder (with care) reveals the git object model


## Objects
Every object is identified by a SHA1 hash (often referred to as a pointer).

Full reference at: https://git-scm.com/book/en/v2/Git-Internals-Git-Objects

The object hash is the SHA1 of the following items concatenated:
* object type (blob, tree, commit)
* a space
* size in bytes of the content
* a null bytes
* the raw content

The same concatenated-string used in the hash is then `zlib-deflated` and written to disk in the `.git/objects` directory as follows

* A parent folder identified by the first two characters of the hash
* A file name identified by the last 38 characters of the hash

Example:
Content with SHA1 hash `bd9dbf5aae1a3862dd1526723246b20206e5fc37` would be stored at 
`.git/objects/bd/9dbf5aae1a3862dd1526723246b20206e5fc37`


### Blob
No structure; can be any content: this is all of your source files, images, artifacts, etc

### Tree
Specifically formatted object that stores a list of pointers to blobs and trees

Structure:
One pointer per line; each pointer contians:
  * "unix mode" (i.e. permissions)
  * object type (tree or blob)
  * object hash
  * name of file / folder 

Basically a directory - A representation of the files in your project
file names are stored in the tree and point to the hash of the file's content
Basically a snapshot of the state of the whole project.


### Commit
Specifically formatted object that stores a (hopefully meaningful) snapshot in time of your project's state

Structure:
* Optional pointer to a parent commit
  * kind of like a linked list - the first list element points to the second; the second to the third; and so forth
* tree pointer (the hash of the tree)
* author name / email address
* date time


## References / Refs
* Remote
* Branch
* Tag


### Pack
Not super relevant to daily usage, but good to know that it exists.

`git gc` will deduplicate objects by content to reduce disk space usage.

the `.pack` file contants the actual content, and the `.idx` file contains a "byte offset" into the pack file for each object contained within.

# Git Commands

## "Porcelain" - Everyday
### init
Starts a new repostiroy; initializes the `.git` folder

### checkout
### branch
### push
### status
### commit
### log



## "Plumbing" - Low-level
You'll likely never use the commands, but they're useful for illustrating what git's doing under the covers.
### hash-object
`echo "something" | git hash-object --stdin`
### cat-file
`git cat-file -t <sha1>` - Show object type
`git cat-file -p <sha1>` - Show object content

### update-index
### write-tree
### read-tree
### commit-tree
### ls-files 

`git ls-files --stage` shows a "treeish" of the current index (staged files)

# Git Hosting
* GitHub
* GitLab
* BitBucket

# Workflows and Teamwork
## Branching strategy
Most teams will use a `main` (historically `master`) and `develop` branch.

It's up to the team to define how features and fixes flow; however, a general convention is this:
 * `main` always represents a "stable" version of the project
 * `develop` represents ongoing work that may or may not be stable, but should generally be "complete"

From there, another common convention is to "prefix" branches with `feature/`, `fix/`, and `hotfix/`.
Since branches are just files inside `.git/refs/heads`, these forward-slash prefixes effectively provide a folder-like heirarchy for organzing in-progress work.

## Merge Requests / Pull Requests
Some teams want work to be "peer reviwed" before being included in the project - this is where mereg requests come in.

MRs are _NOT_ a concept within git itself; rather, a construct in the various Git Hosting services.

There is nothing materially different from a merge executed through a merge request compared with a merge exectued locally - the difference is only in the visibilty to the team and opportunity for review / comments / dialog

## Commit Message Formatting
There are lots of opinions on what to type in a commit message. I like to follow Conventional Commits (https://www.conventionalcommits.org/en/v1.0.0/)

## Semantic Versioning
For projects with muiltiple releases and stakeholders, SemVer (https://semver.org/) provides a way for authors and consumers to have some level of confidence in the changes to the version they're using.

`Major.Minor.Patch` indicates the compatibility and scope of changes

# Advanced Concepts

## Recovering Deleted work
## To the reflog!

Git silently records what your HEAD is every time you change it. Each time you commit or change branches, the reflog is updated.
`git reflog` or `git log -g`

commits referenced in the reflog are eligible for re-use as a new branch or tag.
## Dangling objects
* Look for dangling objects `git fsck --full`

# Resources
## Videos
*  https://www.youtube.com/watch?v=RwYbl_tpoVE

## Websites
*  https://git-scm.com/book/en/v2
*  https://githowto.com
*  https://gitimmersion.com/lab_01.html
*  https://github.blog/2015-06-08-how-to-undo-almost-anything-with-git/
*  https://mincong.io/2018/04/28/git-index/
*  https://www.conventionalcommits.org/en/v1.0.0/
*  https://semver.org/
##  Git Apps and Plugins
* VSCode - https://code.visualstudio.com/
  * GitLens - https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens
* GitExtensions - https://github.com/gitextensions/gitextensions
  * GUI for working with Git on Windows

## Non Git Related Favorites
* VSCode - https://code.visualstudio.com/
  * Debugger for Firefox - https://marketplace.visualstudio.com/items?itemName=firefox-devtools.vscode-firefox-debug
  * Dev Containers - https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers
  * Encode Decode - https://marketplace.visualstudio.com/items?itemName=mitchdenny.ecdc
  * Logcat (Android device debugging) - https://marketplace.visualstudio.com/items?itemName=abhiagr.logcat
  * nc-gcode (editing 3D printer gcode files) - https://marketplace.visualstudio.com/items?itemName=ML.nc-gcode
  * PlatformIO IDE (build tools for microcontrollers / embedded devices) - https://marketplace.visualstudio.com/items?itemName=platformio.platformio-ide
  * TypeScript Class Organizer - https://marketplace.visualstudio.com/items?itemName=aljazsim.tsco
  * XML Tools - https://marketplace.visualstudio.com/items?itemName=DotJoshJohnson.xml
* Devolutions Remote Desktop Manager - https://devolutions.net/remote-desktop-manager
  * Multi-protocol remote toolkit (SSH / RDP / etc)
* HxD Hex Editor - https://mh-nexus.de/en/hxd/
  * lightweight hex editor
* Screen2Gif - https://www.screentogif.com/
  * Screen capture - video
* Greenshot - https://getgreenshot.org/
  * Screen capture - still
* How to use Eclipse with GitHub - https://github.com/maxkratz/How-to-Eclipse-with-Github?tab=readme-ov-file


