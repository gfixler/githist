# githist w/ ctags support

#### Simple, cross-repository change-tracking and tags generation w/ git

This repo provides a git template hooks directory with hooks that set up very
simple tracking of which git repositories have had commits, amended commits,
merges, and/or rebases (and thus which ones you need to remember to push
later). It also sets up completely automated ctags support.

### Requirements

* ctags installed and on your path

### Setup

1. clone this repo to ~/.git_template/
1. run: $ git config --global init.templatedir '~/.git_template'
1. add the following to your ~/.bashrc (or equivalent):


    function githist
    {
        when="${@-today}"
        grep $(date +%F --date="$when") ~/.githist
    }

### Usage (githist part)

    $ githist

The githist function, given no arguments will grep ~/.githist for lines
matching the current date and print them out. This is all githist is about -
it's just to show you which repos (by path) have had commits, merges, etc. The
hooks provided simply automate appending to ~/.githist the date, action, and
project path whenever you perform one of the hooked actions. Before you leave
work for the day, `$ githist` to see what repos might need to be pushed -
that's it!

All bare words following `$ githist` are given to `date`'s `--date` flag, so
you can easily get the actions history for all manner of past days. Examples:

    $ githist yesterday
    $ githist 2 days ago
    $ githist last week
    $ githist 1 year ago

### Usage (ctags part)

This should be entirely automatic. It's based [pretty much entirely] on the
post "[Effortless Ctags with Git][ecwg]" by Tim Pope.

[ecwg]: http://tbaggery.com/2011/08/08/effortless-ctags-with-git.html

I've used notes from the comments to get it working a bit better. Tags should
be automatically generated for any new repo created after this is all set up
(or in which you've later run `git init` (see Notes below)), and they are
tucked into the `.git` folder for each repo.

If you use Tim Pope's [fugitive] [fug] plugin for Vim, then ctags will be
completely automated. Simply work in new repos (in Vim, of course), and tags
will be generated as you make your commits and such. You *are* making nice,
granular commits as you go, right? (ed: stop editorializing)

[fug]: https://github.com/tpope/vim-fugitive

### Notes

* The init.templatedir setting above points git to the new template directory
  this repo provides. Git will automatically pull the hooks therein into any
  new repos created via `$ git init`. If you have existing repos, you can run
  `git init` again in each to pull in the hooks once you've set up the git
  config setting.
* if you already have a ~/.git_template dir setup, you probably know what
  you're doing. Use that knowledge to graft things you want from this repo
  (cloned elsewhere, obviously) into your setup :)
* `git init` is normally idempotent, safe to run again and again in an existing
  repo. While a newly-created template directory setup (as above) *will* be
  pulled in to an existing repo with `git init`, idempotency is then restored,
  and *further* changes to the template directory will *not* be pulled in. To
  restate that, any repo with a hooks directory that is not the stock/default
  will not be changed by future `git init` calls, whether or not the template
  dir has changes. Git doesn't know the source of your hooks dir changes
  (they're untracked), and thus plays it safe by not blowing away any non-stock
  changes. If you've made hooks changes in a repo, you'll have to fold in the
  hook lines provided in this repo manually.
* As hooks files are used directly by git, the ctags automation provided herein
  should 'just work' whether you use command line git, git GUIs, fugitive in
  vim, magit in emacs, etc...
* Make sure you've sourced your '.bashrc' (or equivalent) after adding the
  githist function to it.

