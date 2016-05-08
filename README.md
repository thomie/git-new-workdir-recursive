What: git-new-workdir [1] with submodule support.

Why: you want to have multiple full trees (checkouts) of your repository, so
that if you have a full build of a certain branch in checkout1, you don't have
to do any rebuilding when you switch back and forth to another branch in
checkout2.

There is logically only one local repository, and just many checkouts of it, so
it's very easy to share patches between various checkouts and your branches are
all in one centralized place.

How: it uses `git-new-workdir` under the hood. In addition to calling
`git-new-workdir` for the parent repository, it also recursively calls
`git-new-workdir` for all its (initialised and updated) submodules.

Usage:
  * Make sure `git-new-workdir` is on your path
  * Run `./git-new-workdir-recursive <repository> <new_workdir> [<branch>]`

Example usage:

`ghc-pristine` is an ordinary Git clone of the main GHC repository, the one
that you've been working on sofar. It has a `master` branch (currently checked
out) and a `some-bugfix` branch.

Create a new working directory to validate `some-bugfix`.

    $ git-new-workdir-recursive ghc-pristine ghc-validate-some-bugfix some-bugfix
    Creating 'ghc-validate-some-bugfix' <- 'ghc-pristine'
    Previous HEAD position was 4c9620f... TrieMap: Minor documentation fix
    Switched to branch 'some-bugfix'
    Creating 'ghc-validate-some-bugfix/.arc-linters/arcanist-external-json-linter' <- 'ghc-pristine/.arc-linters/arcanist-external-json-linter'
    Creating 'ghc-validate-some-bugfix/libffi-tarballs' <- 'ghc-pristine/libffi-tarballs'
    Creating 'ghc-validate-some-bugfix/libraries/Cabal' <- 'ghc-pristine/libraries/Cabal'
    Creating 'ghc-validate-some-bugfix/libraries/Win32' <- 'ghc-pristine/libraries/Win32'
    Creating 'ghc-validate-some-bugfix/libraries/array' <- 'ghc-pristine/libraries/array'
    Creating 'ghc-validate-some-bugfix/libraries/binary' <- 'ghc-pristine/libraries/binary'
    Creating 'ghc-validate-some-bugfix/libraries/bytestring' <- 'ghc-pristine/libraries/bytestring'
    Creating 'ghc-validate-some-bugfix/libraries/containers' <- 'ghc-pristine/libraries/containers'
    Creating 'ghc-validate-some-bugfix/libraries/deepseq' <- 'ghc-pristine/libraries/deepseq'
    Creating 'ghc-validate-some-bugfix/libraries/directory' <- 'ghc-pristine/libraries/directory'
    Creating 'ghc-validate-some-bugfix/libraries/filepath' <- 'ghc-pristine/libraries/filepath'
    Creating 'ghc-validate-some-bugfix/libraries/haskeline' <- 'ghc-pristine/libraries/haskeline'
    Creating 'ghc-validate-some-bugfix/libraries/hoopl' <- 'ghc-pristine/libraries/hoopl'
    Creating 'ghc-validate-some-bugfix/libraries/hpc' <- 'ghc-pristine/libraries/hpc'
    Creating 'ghc-validate-some-bugfix/libraries/parallel' <- 'ghc-pristine/libraries/parallel'
    Creating 'ghc-validate-some-bugfix/libraries/pretty' <- 'ghc-pristine/libraries/pretty'
    Creating 'ghc-validate-some-bugfix/libraries/primitive' <- 'ghc-pristine/libraries/primitive'
    Creating 'ghc-validate-some-bugfix/libraries/process' <- 'ghc-pristine/libraries/process'
    Creating 'ghc-validate-some-bugfix/libraries/random' <- 'ghc-pristine/libraries/random'
    Creating 'ghc-validate-some-bugfix/libraries/stm' <- 'ghc-pristine/libraries/stm'
    Creating 'ghc-validate-some-bugfix/libraries/terminfo' <- 'ghc-pristine/libraries/terminfo'
    Creating 'ghc-validate-some-bugfix/libraries/time' <- 'ghc-pristine/libraries/time'
    Creating 'ghc-validate-some-bugfix/libraries/transformers' <- 'ghc-pristine/libraries/transformers'
    Creating 'ghc-validate-some-bugfix/libraries/unix' <- 'ghc-pristine/libraries/unix'
    Creating 'ghc-validate-some-bugfix/libraries/vector' <- 'ghc-pristine/libraries/vector'
    Creating 'ghc-validate-some-bugfix/libraries/xhtml' <- 'ghc-pristine/libraries/xhtml'
    Creating 'ghc-validate-some-bugfix/nofib' <- 'ghc-pristine/nofib'
    Creating 'ghc-validate-some-bugfix/utils/haddock' <- 'ghc-pristine/utils/haddock'
    Creating 'ghc-validate-some-bugfix/utils/hsc2hs' <- 'ghc-pristine/utils/hsc2hs'

    $ cd ghc-validate-some-bugfix && ./validate

Caveat:
Make sure you don't checkout the same branch from different working
directories. It will confuse git if you commit to that branch from one working
directory, as the other working directory will now be out date.

I usually name my working directories after the branch that they have checked
out.

Bugs:
  * git-new-workdir-recursive unsets `core.worktree` for each submodule in the
  original repo. Certain scripts (e.g. vim-fugitive) will become unusable in
  the submodules of the original repository, but work fine in the new work
  directories.

Author: Thomas Miedema
Homepage: https://github.com/thomie/git-new-workdir-recursive

[1] https://github.com/git/git/blob/master/contrib/workdir/git-new-workdir
