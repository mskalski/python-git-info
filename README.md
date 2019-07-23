# python-git-info

A very simple project to get information from the git repository of your project.
This package does not have any dependencies; it reads directly the data from the
.git repository.

## Installation

Just do a `pip install gitinfo`, or copy the `gitinfo/gitinfo.py` file to your
project directly. This project should work with both python 2.7 and 3.x.

## Usage

This app will search the current directory for a `.git` directory (which is
always contained inside the root directory of a project). If one is found
it will be used; else it will search the parent directory recursively until a
`.git` is found.

There's a single function name `get_git_info()` with an optional `dir` parameter.
If you leave it empty it will start the `.git` directory search from the current directory,
if you provide a value for `dir` it will start from that directory. The `get_git_info`
will return a dictionary with the following structure if everything works ok or
`None` if something fishy happend or no `.git` folder was found:

```
  {
    'parent_commit': 'd54743b6e7cf9dc36354fe2907f2f415b9988198', 
    'message': 'commit: Small restructuring\n', 
    'commiter': 'Serafeim <email@email.com>', 
    'commit_time': '2018-11-14 13:52:34', 
    'commit': '9e1eec364ad24df153ca36d1da8405bb6379e03b'
  }
```

## How it works

This project will return the info from the latest commit of your *current* branch. To do this, it will read the `.git/HEAD` file which contains your current branch (i.e something like `ref: refs/heads/master`). It will then read the file it found there (i.e `.git/refs/heads/master`) to retrieve the actual sha of the latest commit, something like `8f6223c849d4bba75f037aeeb8660d9e6e306862`. 

This object is located in`.git/objects/8f/6223c849d4bba75f037aeeb8660d9e6e306862` (notice
the first two characters are a directory name and the rest is the actual filename). This
is a zlib compressed folder. After it is uncompressed it has a simple format; I'm
copying from the git internals manual:

> The format for a commit object is simple: it specifies the top-level tree for the snapshot of the project at that point; the parent commits if any (the commit object described above does not have any parents); the author/committer information (which uses your user.name and user.email configuration settings and a timestamp); a blank line, and then the commit message.

So a sample commit message file would be something like this:

```
tree fa077d18fe3309aa12791dad2f733bfbb50fdee6
parent 943f6e8e3641ea38a9d9db3256944b46bcfc1f77
author Serafeim Papastefanos <spapas@example.com> 1562836041 +0300
committer Serafeim Papastefanos <spapas@example.com> 1562836041 +0300

prep new ver
```

## Rationale

This project may seem useless or very useful, depending on the way you deploy to your servers. If you, like me, push every changeset to your VCS *before* deploying and then pull the changes from the remote server to actually deploy then you'll find this project priceless: You can easily add the latest commit information to somewhere in your web application so you'll be able to see immediately which changeset is deployed to each server without the need to actually login to the server and do a `git log`.

Also it is important to add here that this project is pure python and does not have
any external dependencies (not even `git`); making it very easy to install and 
use in any project.

## Changes

0.5

* Change the parsing algorithm from using `.git/logs` to parse the real commit object inside the `.git/objects` folder.


0.4

* Add more error checks

0.3

* Make it work with '..'

0.2

* Initial

