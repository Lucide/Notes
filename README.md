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
* `add <pattern>` start with `/` to avoid recursivity, end with `/` to specify a directory, negate with `!`, `*` 0 or more chars, `[abc]` any char inside brackets, `[0-9]` any char between, `/**/` nested directories
* `reset HEAD <file>` unstage a file
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
* `mv <file1> <file2>` move or rename a file, same thing as
  ```
  mv README.md README  
  git rm README.md
  git add README
  ```
* `log` display commit history
  * `-p` (patch) differences in each commit
  * `-<n>` show last n commits
  * `-stats` print some statistics
  * `--pretty="<format>"` format following a preset
    * `oneline`
    * `short/full/fuller`
    * `format: <pattern>` custom format preset
  * `--graph` ascii graph
  * `--decorate` show you where the branch pointers are pointing.
* `show` display and filter things
* `checkout`
  * `--<file>` discard uncommitted changes
  * `<tagname>` set working tree to the tag's version (detached HEAD)
  * `-b <branch>` in “detached HEAD” state, commit changes to a new branch
  * `<branch>` switch to branch
  * `-b <branch>` create and switch to branch
* `remote` list remotes
  * `-v` show urls
  * `add <shortname> <url>` add a remote repository
  * `show <remote>` show more info
* `fetch <remote>` download all the new data
* `pull` if branch is set up to track a remote branch, it fetches and merges
* `push <remote>` upload to server
  * `<branch>` upload branch to server
  * `<tagname>` push tag to server
  * `--tags` push all tags (annotated and lightweight)
  * `-d <tagname>` delete tag from server
* `tag` list tags
  * `-l "<pattern>"` (list) filter by pattern
    * `<tagname>` add a lightweight tag
    * `-a <tagname>` add an annotated tag
      * `-m` specify message inline
    * `<commit checksum>` if adding a tag, it adds it to specified commit
  * `-d <tagname>` delete a tag
* `branch` lists branches
  * `-v` see the last commit on each branch
  * `--merged/no-merged` filter branches merged or not to the current branch
  * `<branch>` create a new branch
  * `-d <branch>` delete a branch
  * `-D <branch>` force delete a branch
* `merge <branch>` merge branch to current branch
* `mergetool` open a visual merge tool