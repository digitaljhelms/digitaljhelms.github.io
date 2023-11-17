# Squash the First 2 Commits in a Git Repository

*Published on July 13, 2012*

## The scenario

Your repository has two commits:

```language-bash
$ git log --oneline
957fbfb No, I am your father.
9bb71ff A long time ago in a galaxy far, far away....
```

Use the interactive rebase tool to squash the two commits:

```language-bash
$ git rebase -i 9bb71ff
```

When your editor opens, only a single commit is listed:

    pick 957fbfb No, I am your father.

You change `pick` to `squash`, save & close your editor.

## The problem

Git complains...

```language-bash
Cannot 'squash' without a previous commit
```

## The fix

```language-bash
$ git rebase -i 9bb71ff
```

This time, when your editor opens, change `pick` to `edit` instead of `squash`, save & close your editor.

```language-bash
$ git reset --soft HEAD^
$ git commit --amend
```

Your editor again so that you can modify the commit message of the soon-to-be squashed commit; make your changes, save & close the editor.

```language-bash
$ git rebase --continue
```
