+++
title = "Synchronizing Git repositories via email"
description = "Sometimes GitHub/GitLab/own-server is not an option - good old email to the rescue"
date = "2023-11-27"

[extra]
authors = "Radovan Bast"
+++

For a project with the [Department of Language and
Culture](https://en.uit.no/enhet/isk) at [UiT](https://uit.no/) we had the
challenge to find a way to synchronize two Git repositories where we wish to
end up with a **as-similar-as-possible history**, but where we cannot communicate
changes through GitHub or GitLab or similar servers.

To have a concrete example, let us assume that we have two computers
(**computer A** and **computer B**) which are far away and they can only talk
to each other **via email**. Both hold a Git repository which tracks some data or
code and both Git repository have the same starting point (they have a common
history).

{{ div(attributes='class="uk-child-width-expand@m" uk-grid') }}

{{ div() }}

On **computer A** we have the following Git history:
```
* 69bf25c (HEAD -> main) another improvement
* b202aa5 adding more data to my repo
* 5670bc4 initial commit
```
We got this information with `git log --oneline --graph`.
`69bf25c` is the newest commit and `5670bc4` the oldest.

{{ enddiv() }}

{{ div() }}

On **computer B** we have the following Git history:
```
* e6287b6 (HEAD -> main) another local modification
* 1120a75 one local modification
* 69bf25c another improvement
* b202aa5 adding more data to my repo
* 5670bc4 initial commit
```
The Git history on **computer B** has the same starting point (first 3 commits)
but we created two more commits which we wish to later share with the other
computer:

{{ enddiv() }}

{{ enddiv() }}

Below I will discuss 3 options to do this, but **I recommend the first** (using
`git bundle`) since that one preserves commit hashes (commit identifiers),
whereas the other two option don't.

<!-- toc -->


## Using [git bundle](https://git-scm.com/docs/git-bundle) (recommended)

On **computer B** we create a bundle from the entire `main` branch:
```bash
$ git bundle create project.bundle main
```

It can be useful to also tag the commit from which we created the bundle
so that we know where to bundle from next time:
```bash
$ git tag -f last-export-on-computer-B
```

The generated `project.bundle` file is the one that we can email over to the other computer.

On **computer A** we can now pull changes from the bundle into the `main` branch:
```bash
$ git pull project.bundle main --rebase
```

The `--rebase` part makes sure that we move any local changes we might have to
the end.  We can verify the result with `git log --oneline --graph`.  If
**computer A** has commits that **computer B** does not have, we can do the
same in reverse: create a bundle, tag the commit from which we created the
bundle, email the bundle over and apply it there (pull from it there).

Using the tags allows you to bundling commits only since that tag instead of
all commits:
```bash
$ git bundle create project.bundle last-export-on-computer-B..main
```

But don't forget to tag again after each bundling.

If both computers changed the same portion of the code/data in different ways
it is possible to get a conflict. Here it might be necessary that the humans
who created the two changes communicate and decide which of the two versions
they wish to keep and base their further work on. You can minimize the chance
of conflicts by emailing bundles right after making a new commit and not
waiting for too long.


## Using [git format-patch](https://git-scm.com/docs/git-format-patch)

Also here we start with **computer B** having two commits more. So let's create patches
from the two most recent commits:
```bash
$ git format-patch -2

0001-one-local-modification.patch
0002-another-local-modification.patch
```

Note that the patches are numbered. We can now email
`0001-one-local-modification.patch` and `0002-another-local-modification.patch`
and try to apply them on the other computer. If you need to send very many
files, you can zip them into one archive and email that one archive instead.

Now we move to **computer A** and try to apply the patches but we need to apply them in order:
```bash
$ git am 0001-one-local-modification.patch
$ git am 0002-another-local-modification.patch
```

One disadvantage of `git format-patch` compared to `git bundle` is however that
we will end up with **different Git commit hashes**.
To see this, compare histories on both. The content is the same, the commit messages
are the same but the commit hashes differ:

{{ div(attributes='class="uk-child-width-expand@m" uk-grid') }}

{{ div() }}

Git history on **computer A**:
```
* 87fe580 (HEAD -> main) another local modification
* 175d42c one local modification
* 69bf25c another improvement
* b202aa5 adding more data to my repo
* 5670bc4 initial commit
```

{{ enddiv() }}

{{ div() }}

Git history on **computer B**:
```
* e6287b6 (HEAD -> main) another local modification
* 1120a75 one local modification
* 69bf25c another improvement
* b202aa5 adding more data to my repo
* 5670bc4 initial commit
```

{{ enddiv() }}

{{ enddiv() }}


## Comparing and applying patches "manually"

If the data/software that is tracked in Git is not too large to be sent itself,
then one way to share changes is to send the data or software over and on each
side merge changes visually using programs like [Meld](https://meldmerge.org/)
and then commit the new changes:
- Email the data/software to computer A
- With Meld compare the new version with the last commit and merge changes
- Commit
- Email the merged result back to computer B
- With Meld compare the new version with the last commit and merge changes
- Commit
- ...

However, with this strategy we will end up with **different Git commit hashes**.
