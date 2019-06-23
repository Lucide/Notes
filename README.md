# GIT Commands

* `config`
  * `--list --show-origin` view all settings and from where they come from
  * `--global` make the setting global
    * `user.`
      * `name <name>`
      * `email <email>`
    * `alias.<shortname> <command>` set up a shorthand
* `help <verb>` show helps, -h can also ben used
* `clone <path>` clone a repository
* `status` display repository status
  * `-s` (short) left column: staging area, right column, working tree
* `add`
  * `-A` stages all changes, same things as `git add .; git add -u`
  * `.` stages new files and modifications, without deletions
  * `-u` stages modifications and deletions, without new files
  * `<pattern>` start with `/` to avoid recursivity, end with `/` to specify a directory, negate with `!`, `*` 0 or more chars, `[abc]` any char inside brackets, `[0-9]` any char between, `/**/` nested directories
* `reset HEAD <file>` unstage `file`
* `diff` compare what has what has changed between working tree and staging area
  * `--cached` compare what has what has changed between staging area and last commit
* `commit`
  * `-v` show also `diff --cached`
  * `-a` add all already tracked files
  * `-m` specify the message inline
  * `--amend` replace previous commit
* `rm <pattern>` stage a file removal and delete it from the working tree
  * `-f` force deletion if file was was just added or modified
  * `--cached` remove only from staging area
  * `-r` recursive
* `mv <path1> <path2>` move or rename a file, same thing as

      mv <path1> <path2>  
      git rm <path1>
      git add <path2>
* `log` display commit history
  * `-p` (patch) differences in each commit
  * `-<n>` show last `n` commits
  * `-stats` print some statistics
  * `--oneline`
  * `--short/full/fuller`
  * `--pretty=<format>` format following a `format` pattern
    * `oneline`
    * `short/full/fuller`
    * `format: <pattern>` custom format `pattern`
  * `--graph` ascii graph
  * `--decorate` show you where the branch pointers are pointing.
* `show` display and filter things
* `checkout`
  * `--<file>` replace `file` with the most recently-committed version
  * `<tagname>` set working tree to commit pointed by `tagname` (detached HEAD)
  * `<branch>` switch to `branch`, if the branch name you’re trying to checkout doesn’t exist and exactly matches a name on only one remote, it will create a tracking branch
  * `-b <branch>` create, switch, and commit to `branch`
    * `<remote>/<branch>` merge `remote`/`branch` into branch
  * `--track <remote>/<branch>` alternative to avoid repeating local branch name when adding a tracking branch
* `remote` list remotes
  * `-v` show urls
  * `add <shortname> <url>` add a remote repository
  * `show <remote>` show more info
* `fetch`
  * `<remote>` fetch from remote
  * `--all` fetch from all remotes
* `pull` if branch is set up to track a remote branch, it fetches and merges
* `push` upload to tracked remote
  * `<remote>` upload to `remote`
  * `<branch>` push `branch` to remote
    * `--delete <branch>` delete remote/`branch`
    * `<tag>` push `tag` to remote
    * `--tags` push all tags (annotated and lightweight)
    * `-d <tag>` delete `tag` from server
* `tag` list tags
  * `-l <pattern>` (list) filtering by `pattern`
    * `<tagname>` add a lightweight tag
    * `-a <tagname>` add an annotated tag
      * `-m` specify message inline
    * `<commit checksum>` if adding a tag, it adds it to specified commit
  * `-d <tag>` delete `tag`
* `branch` lists branches
  * `-v` see the last commit on each branch
  * `--merged/no-merged` filter branches merged or not to the current branch
  * `<branchname>` create a new branch
  * `-d <branch>` delete `branch`
  * `-D <branch>` force delete `branch`
  * `-u <remote>/<branch>` (set-upstream-to) set (or change) an already existing local branch to to track `remote`/`branch`
  * `-vv` list your local branches with more information, including what each branch is tracking and if your local branch is ahead, behind or both
* `merge <branch>` merge `branch` to current branch
* `mergetool` open a visual merge tool
* `rebase`
  * `<branch>` rebase the first current branch commit after the branching point on top of the latest commit in `branch` (change its parent)
  * `--onto <newparent> <oldparent>` rebase the current branch commit whose parent is `oldparent` on top of `newparent` (change the parent)
    * `<until>` rebase the range of commits whose parent is oldparent up to `until` on top of `newparent`
