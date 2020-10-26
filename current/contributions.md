---
title: Contributions
layout: default
nav_order: 800
---

# Contributing

First off, thank you for considering contributing to Rejoice. You are awesome. It's people like you that make Rejoice such a wonderful framework.

## Reporting bug, fixing bug, creating new feature

If you've noticed a bug or have a feature request, [make one][new issue]! It's generally best if you get confirmation of your bug or approval for your feature request this way before starting to code.

## Security vulnerability

If you have discover a security vulnerability inside Rejoice, please DO NOT open an issue. Send an email to Prince Dorcis at <a href="mailto:princedorcis@gmail.com">princedorcis@gmail.com</a>. It will be addressed with high priority.

## Support and general questions
If you need help or have a general question about rejoice, you can post it on:

- [Stack Overflow],
- [Discord](https://discord.gg/92JVJUf).

The issue tracker is only for bugs and feature requests.

## <a name="code-of-conduct"></a>Code of conduct

The Rejoice code of conduct is derived from the Laravel framework code of conduct. By participating, you are expected to uphold this code. Any violations of the code of conduct may be reported to Prince Dorcis (princedorcis@gmail.com):

- Participants will be tolerant of opposing views.
- Participants must ensure that their language and actions are free of personal attacks and disparaging personal remarks.
- When interpreting the words and actions of others, participants should always assume good intentions.
- Behavior that can be reasonably considered harassment will not be tolerated.

## Fork & create a branch

If you can fix the issue, then [fork Rejoice] and create a branch with a descriptive name.

A good branch name would be:

```sh
git checkout -b 732-fix-default-menu-not-used
```

## Get the test suite running

Make sure to have *PHPUnit* test cases for any fix or feature you will write.

## Implement your fix or feature

At this point, you're ready to make your changes! Feel free to ask for help; everyone is a beginner at first :smile_cat:

## Get the style right

Your patch should follow the same conventions & pass the same code quality
checks as the rest of the project. Rejoice uses [StyleCI](https://styleci.io) to checks for codinf style. You can check & fix style issues when commiting to your branch.

## Make a Pull Request (a.k.a PR)

At this point, you should switch back to your master branch and make sure it's up to date with Rejoice's master branch:

```sh
git remote add upstream git@github.com:rejoice/rejoice.git
git checkout master
git pull upstream master
```

Then update your feature branch from your local copy of master, and push it!

```sh
git checkout 732-fix-default-menu-not-used
git rebase master
git push --set-upstream origin 732-fix-default-menu-not-used
```

Finally, go to GitHub and [make a Pull Request][] :D

TravisCI will run our test suite against supported PHP versions. We care
about quality, so your PR won't be merged until all tests pass.

## Keeping your Pull Request updated

If a maintainer asks you to "rebase" your PR, they're saying that a lot of code
has changed, and that you need to update your branch so it's easier to merge.

To learn more about rebasing in Git, there are a lot of [good][git rebasing]
[resources][interactive rebase] but here's the suggested workflow:

```sh
git checkout 732-fix-default-menu-not-used
git pull --rebase upstream master
git push --force-with-lease 732-fix-default-menu-not-used
```

## Merging a PR (maintainers only)

A PR can only be merged into master by a maintainer if:

- It is passing CI.
- It has been approved by a maintainer.
- It has no requested changes.
- It is up to date with current master.

Any maintainer is allowed to merge a PR if all of these conditions are
met.

## Shipping a release (maintainers only)

Maintainers need to do the following to push out a release:

- Switch to the master branch and make sure it's up to date.
- Review and merge the PR. The generated changelog in the PR should include all user visible changes you intend to ship.

[Stack Overflow]: http://stackoverflow.com/questions/tagged/rejoice
[new issue]: https://github.com/rejoice/rejoice/issues/new
[fork Rejoice]: https://help.github.com/articles/fork-a-repo
[make a pull request]: https://help.github.com/articles/creating-a-pull-request
[git rebasing]: http://git-scm.com/book/en/Git-Branching-Rebasing
[interactive rebase]: https://help.github.com/en/github/using-git/about-git-rebase