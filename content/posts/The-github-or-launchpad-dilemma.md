---
Categories:
  - ubuntu
Description: 'We want to use git, but where?'
Keywords:
  - vcs
  - source control
  - development
Tags:
  - snappy
date: 2015-06-25T01:27:20.000Z
title: The github or launchpad dilemma
---

We wanted to start a migration path from bazaar to git given how ubiquitous it
is and due to the fact that most in our team prefer it. A few months ago the
decision was easy, since launchpad did not support git, we would just switch to
github given it's popularity. _That's not true anymore..._

Today launchpad supports git and our comparison becomes finer grained and we
have to break it down a bit more.

So here are things I like github:

- Code is presented first.
- Documentation is easy to write and very nice to **read**.
- Non technical people can make edits and propose pull requests all from the
  web.
- It's a bit more social (e.g; you have mentions).
- Web hooks and many things embracing them.
- A big user base, mostly everyone is already on github.
- The code review interface.
- The UI layout in general.
- The API.

The things I like about launchpad:

- Direct link between the source and ubuntu.
- A very nice bug tracking system.
- Given we work with Ubuntu, a very big existing database. Every other team
  working on Ubuntu uses launchpad already.
- Very product oriented.
- A nice language translation system.

Most of the things a like about one are probably things that I don't like or
are missing in the other.

# snappy
Given we work on `lp:snappy` most of the time now, I want to have a look at the
_would be_ workflow when on launchad and on github.

# The launchpad workflow
First of all, if the codebase were moved to launchpad's git support we'd be
missing proper support to query merge proposal status and linking bug reports
to commits.

The flow with git would be as follows:

1. `cd $GOPATH/src/launchpad.net/snappy`.
2. `git branch -c <feature>`
3. edit/create/fix
4. `git commit -s -m '...'`
5. git push git+ssh://USER@git.launchpad.net/~USER/snappy`
6. Create merge proposal.
7. Manually merge.
8. Manually invoke test run.
9. `git push git+ssh://USER@git.launchpad.net/snappy`

It is an improvement over bzr (especially since branches are colocated and go
likes that), but we miss:

- unit test runs.
- unit test coverage tracking.
- automatic merging, launchpad support required and a new `tarmac`
  implementation.
- translation support, only supported for bazaar.
- package recipe to push latest trunk to a PPA, also requires launchpad support.

That said, things are coming along and most of this would be solved by either launchpad API enhancements to understand git or webhooks.

# The github workflow
Given github's popularity, mostly everything is already done for you, and since they have webhooks a chain of events that follow an action in github gives us a very neat experience.

This is what would happen:

1. `cd $GOPATH/src/launchpad.net/snappy`.
2. `git branch -c <feature>`
3. edit/create/fix
4. `git commit -s -m '...'`, if the issue is part of the
   comment it gets linked through github.
5. git push git@github.com:<snappy-org>/snappy.git
6. Create pull request.
7. travis is triggered by the event and runs everything we  
   tell it to:
    1. Run a test build.
    2. Runs unit tests.
    3. Runs sanity checks (go vet, lint, ...)
    4. Push the unit test coverage to corevalls.io
    5. Build deb.
8. Reviewer uses the data updated in real time aside from his human provided
   factor to determine if the PR should be merged. This data includes, travis
   passing unit tests and it's coverage increase or decrease among others with
   nice badges.
9. Click on Merge PR.
10. The master branch has it's status/sanity presented with badges as well.

# Closing thoughts
It is no secret I've been wanting to move to github for a while, it solves
many problems we have that we don't want to go around and solve ourselves. It
is not the panacea but it does seem fit for most of the things we need.

Given that now both launchpad and github support git we can ping pong between
them as seen fit (not out of spite though).

The biggest hurdle we'd be facing on every change is our `go` import paths which
are absolute to make `go get` straightforward (even if we don't take too many
other advantages for it) for which one solution I've been wanting to give a try
is http://getgb.io.

In some sense I sometimes get the feeling that github is like vim and launchpad
is like emacs, and I am a vim person.
