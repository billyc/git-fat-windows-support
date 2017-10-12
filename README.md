Git-fat Usage at TU/Berlin
==========================

How it Works
============

Git-fat is a git add-on that makes tracking and storing "fat" (large) files a lot easier. Full git-fat docs are available at https://github.com/jedbrown/git-fat

The key idea is git-fat provides a separate storage area for large files, and keeps them outside of the primary git repository. It instead checks in tiny "placeholder files" that point to the external storage. Once it's set up you don't need to think about that: all you need to do is remember one extra command to push/pull those large files when needed.

Git-fat uses a config file `.gitattributes` that specifies filenames that will automatically be handled by the plugin. You can also manually add files using `git fat add`.


Installation
============

* Depends on [Python](http://www.python.org) and `pip` python package manager. Install those first. If you're on Windows, the [Anaconda](https://www.anaconda.com/download/) python installer is by far the fastest path to success.

* Depends on rsync, which should already be installed on Mac and Linux. On Windows just run [this installer](https://drive.google.com/file/d/0B9pkf5GVhfeVTUhMbTVqdU51R3M/view?usp=sharing).

* Now run =pip install git-fat=

* That's it!


Working With an existing Git-Fat Repo
=====================================

**Initial setup**

* Clone the repo as you normally would.

* **Important!!** -- Run `git fat init` to activate the git-fat plugin *in this repo*. You only need to do this once in each repo.

* `git fat pull` will download all the latest versions of large files that are referenced in HEAD.

* The `.gitattributes` file lists the filetypes that are automatically handled by git-fat. This is where you would list, say, *.gz files to be stored outside the main git repo.

**Updating/adding the large files**

* When you change the large files, use `git add` and `git commit` as you normally would. Git-fat will automatically handle them. BUT, there is now one additional command you must use to push the large files so others can pull them:

* `git fat push` will push the large file objects to the separate shared storage used by git-fat. Here at VSP we have that set up at...

**Setting up a repo to start using git-fat**

Edit (or create) `.gitattributes` to regard any desired extensions as fat files.

    $ cd path-to-your-repository
    $ cat >> .gitattributes
    *.png filter=fat -crlf
    *.jpg filter=fat -crlf
    *.gz  filter=fat -crlf
    ^D

Run `git fat init` to activate the extension. Now add and commit as usual.
Matched files will be transparently stored externally, but will appear
complete in the working tree.

Set a remote store for the fat objects by editing `.gitfat`.

    [rsync]
    remote = your.remote-host.org:/share/fat-store

This file should typically be committed to the repository so that others
will automatically have their remote set. This remote address can use
any protocol supported by rsync. 

Most users will configure it to use remote ssh in a directory with shared 
access. To do this, set the `sshuser` and `sshport` variables in `.gitfat` 
configuration file. For example, to use rsync with ssh, with the default
port (22) and authenticate with the user "_fat_", your configuration would 
look like this: 

    [rsync]
    remote = your.remote-host.org:/share/fat-store
    sshuser = fat
    
   