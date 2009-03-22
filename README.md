# notes on the demos in my "why git" talk

This is a quick rundown on the demos that form part of 'gittalk.notes' in my
'git-notes' repo, (a presentation which can be seen in 'rendered' form at
<http://sitaramc.github.com/other-stuff/gittalk.html>).

The demos are a kludge, make no mistake.  Their primary purpose is to avoid
actually typing in a series of commands -- they save time as well as make sure
I don't forget a step in the demo.

This doesn't apply to the GUI parts -- I can't script them so easily, but
that's fine; the level of my audience is usually such that they would want to
see this being done properly.  YMMV.

### Prerequisites

In the (currently empty) directory called "external", get a clone of cobbler:

    cd external
    git clone git://git.fedorahosted.org/hosted/cobbler

I wanted a real-life project that wasn't too big in size or too complex in
activity, and cobbler was the first one I explored that fit the bill.  There
is nothing special about cobbler otherwise.  However, a lot of the actual
demos now depend on cobbler (for example, some pieces have hardcoded SHAs;
they allow me to directly jump to bits of the commit tree that are useful in
pointing out various aspects of git)

So if you want to use some other project, you certainly can -- but you'll have
to change a lot of things in the demo to fit...!

### Structure

#### Driver

`./demo` is the main program you run each time you want to do a demo, like so:

    ./demo 07

Each demo is scripted in a file with a name like `07-log`; when you type
`./demo XX`, a file whose name is `XX-*` is picked up (there should only be
one).

#### Scripts

Each script is just a series of shell commands.  If the command starts with a
`^` (hat), it is silently run by `./demo`.  Otherwise, the command is printed
to screen -- so you can point out to the audience what command you are
"running" -- and when you hit enter, it actually runs the command.

You can type `n` before you hit enter on one of those "prompts" and that
command won't be run.  However, although I put that feature in, I never
actually used it in all the times I have given this talk.

#### Special scripts

Some scripts call other ones to prep the repo for that particular demo, plus
there's a helper function (`02helper` -- note the lack of a hyphen in its
name) that other scripts often call.  This works behind the scenes; it copies
`external/cobbler` as `cobbler` and removes the external branches to avoid
confusing people when we show them `gitk` or something.

You can actually run/re-run the demos in any order -- there is no need to
stick to the sequence.

In addition, running `./demo 99` cleans up all temporary stuff and returns you
to a clean slate.

### Details of each demo

#### 01-simple

A very basic starter demo starting with just 3 files (hello.c, Makefile,
README.txt), git init, etc.

Also shows the use of gitignore.

When `vim .gitignore` runs, type the word "hello" into the editor and save it.
When the commit editor comes up, point out the contents of the editor (in the
"comments"), type up a decent commit message, and save.

ALTERNATIVE SCRIPT: `./demo 01a` does the same thing, but replaces the `vim`
with an `echo hello > .gitignore` so you don't even have to do that.  It also
does the commit the same way, using `git commit -m ...`

#### 02-clone

Calls `02helper` (see above).  This demo shows the git clone command, and a
dummy `git pull` and `git push` so you can tell them what they look like when
there are no changes to be propagated either way.

#### 03-normal-cycle

This starts up to `vim` sessions -- the first to change an existing file, the
second to create a new one.  It shows `git diff`, `git status`, `git add
filename`, etc.

It then starts up `gvim` (you may want to change this is you do not like/use
gvim), then `git gui`, `git status`, `git show`, and finally ends with a
`git push`.

Make some simple changes in each file that comes up in vim/gvim.  When `git
gui` runs, use it to make a commit (show them staging by clicking on the icon
next to the file, type in some comment text in the box, click the commmit
button)

#### 04-short-branch

This just shows a short branch in gitk so they get a feel for what it looks
like.  The SHA is hardcoded in the script, so if you use some other repo than
cobbler just pick an appropriate one and change it.

#### 05-long-branch

This shows a longer branch using `gitk`.  In addition, there is a second call
to `gitk` using `--date-order` -- it presents the same long branch in a subtly
different way that is sometimes useful to demonstrate.  As you page down the
tree in both modes you'll see the difference.

#### 06-merge-info

This opens gitk on cobbler at a point where you can see three merges very
close to each other; the first two are simple enough, and the third one has a
conflict, which was resolved as a part of the merge.  You can show, in each
case, the sort of information that is available -- the 2 parent SHAs, the fact
that the first 2 merges show no actual changes, and the contrast with the 3rd
one.

#### 07-log

This just shows various `git log` options.  The two commands showing log
"since a certain date" and "...date+time" may or may not show different things
based on your time zone.  In India, where I live, the former one shows one
extra commit.

#### 08-show

Ditto, for `git show`

#### 09-refactor+bug-fix

This is a nice demo; I usually make sure I have time for this one.

I use two branches, `dev1` and `dev2` to simulate two separate lines of
development by different developers.  I also use perl commands to simulate the
changes these developers would have normally made using vi, to save time.

Most importantly, the second dev, after making his changes, both renames *and*
moves the file to another directory.

When both these branches are merged into `master`, git manages to apply both
the sets of changes, including the rename, properly.

This is something that *no other VCS*, not even Mercurial -- which comes
closest to git in many ways -- can do.

SPEAKER ACTIONS: This is worth noting down carefully.

  * the demo starts with a `gitk &`.  Do not close this till then end.  At
    *each and every* command, I switch to this display and do a `Ctrl-F5` to
    demonstrate what the effect of the just-executed command.  For example:
    * the effect of a `git tag`
    * the effect of a `git checkout` -- point out that the new branch has
      become **bold** in the `gitk` display
    * what a fork looks like
    * what the first "merge" does (it's a fast-forward)
    * (etc)
  * make sure to point out that the perl commands are only a convenience,
    substituting for `vi` or some other editor
  * when the first `git gui` comes up, stage it and commit as normal, then
    close that window; you don't need it anymore
  * when the second `git gui` comes up, show them the state of the repo, as
    seen in `gitk`, at three points (you must hit Ctrl-F5 each time in gitk):
    * before you do anything in `git gui`
    * when you have staged just the "new" file
    * after you have staged both of the files -- gitk will suddenly realise
      (and show) that there is a 97% similarity between those 2 files and will
      show it as a rename plus some changes.  **Point out that you did not, at
      any time, do a `git mv` or equivalent!**
    * after you have committed the second set of changes via `git gui`
  * once the second `git gui` has been done, I let the other commands run,
    (each time showing the `gitk` output).

#### 10-cherry-pick

This one just shows a cherry-pick.  You just have to let the commands run, and
then -- using the `gitk`, show how to cherry-pick a couple of those commits
onto master.  You can also explain what would happen if you picked them in the
wrong order (that is, under what conditions can you expect "problems" by
changing the order of two commits -- if commit 2 is in an area of code that
was already changed by commit 1, you will have some trouble reversing them).

#### 11-rebase-interactive

A very straightforward demo, with lots of scope for git-evangelism :)  Show
them that although gitk is constrained to show you commits in a "newest at the
top" fashion, the `rebase -i` has to go "oldest at the top" because that's the
sequence in which the commits are going to be applied.  Pick a couple, swap
one with another, squash one, drop one by deleting it... then save.

Make sure they remember what you did in the editor, hit Ctrl-F5 on gitk, and
show them what happened.

#### 12-parallel-branches and 13-serial-branches

These two are meant to show the difference between merging and rebasing, when
faced with the common issue of a push being rejected due to being non-fast
forward.

You'll have to keep *two* gitks running in each of these demos, to show
graphically what is happening to the commit tree in each of the two
repositories (cobbler and cobbler-clone)
