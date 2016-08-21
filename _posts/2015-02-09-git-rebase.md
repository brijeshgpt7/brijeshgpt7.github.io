---
layout: post
title: Git Rebase
date: 2015-02-09 20:34:26
image: '/assets/img/'
description: Might be some developer favorite command is git merge but its polluting our git history!!
main-class: git
color: '#005f97'
tags:
- DVCS
- Git
categories:
twitter_text: DO ONE THING, DO IT WELL.
introduction: Hate web-apps? Work from your terminal
---

As we know about `merge commit` not being so great. You've got merge commits from local branches, merge commits
from remote branches. And it might seem like it's __polluting your history__. That's not so good. But there's an
alternative to doing these sort of merges with `Merge Commits`, and that's called **rebase**.

#### Typically, what git rebase will do:

instead of mergin with other branch with `merge command`

{% highlight ruby %}
git checkout stage_branch
git merge branch_name
{% endhighlight ruby %}

Do with Rebase way ...

{% highlight ruby %}
First:  git fetch
Second: git rebase branch_name
{% endhighlight ruby %}

#### Internally `git rebase` does three thing.
>**First:** get the same commit in temporary storage.
>
>**Second:** Run all other branch commit.
>
>**Third:** Now it will run all commit in temporary one after another.
>
>After running git rebase it will open-up with default terminal with some command(Do check If you have't).

{% highlight ruby %}
A git rebase -- abort # IT MEANS ROLLBACK
B git rebase -- skip # IT MEANS Skip rebase
C git rebase -- continue # IT MEANS to continue that process
{% endhighlight ruby %}

save and exit and now if you check git log you will so only two commit will be present!!

#### Typically, you would use git rebase for other certain task:

>1: Revert or Delete single commit or multiple commits that are no longer necessary.
>
>2: Reorder the commits.
>
>3: Rename or Edit the commit messages.
>
>4: Split one commit in to many commits(usually into two commits).
>
>5: Squash a commits means making two commit in one.
>

All this task will done by `git rebase --i HEAD^`. Here `HEAD^` means one commit.
We can give as many commit numbers like `HEAD^4`

#### Enjoy!! keep reading
