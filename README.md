# Study on git tagging and git Diff
**Scenario**: multiple pipelines watch the same repo for changes, each pipeline 
looks for changes from a specific directory. Given the following 

- A Looks for changes in dir A
- B looks for changes in dir B
- C looks for changes in dir C

## Flow

1. User clones repo and makes 3 commits to A in dir_a
2. User makes 2 commits to B in dir_b
3. Pipeline A kicks off first and notices 3 commits since the last time it tagged 
   the repo with an `a-*` tag.  The pipeline completes its work creates a new `a-tag` tag and pushes.
4. Pipeline B kicks off next ( it could have very well kicked off first) and notices 2 commits to B in dir_b
   since the last `b-*` tag. This pipeline also completes its work and tags a new `b-tag` and pushes.
5. The same process happens for each pipeline A, B, C... and so on.


## Commands
**Determing the last tag**
Pipeline X should always look for tags belonging to pipeline X. This is the command pipeline B runs to find the last B tag.
```bash
git for-each-ref --sort=taggerdate --format '%(refname)' refs/tags  | grep "b-" |  tac | sed -n 1p
```
Odd Commands:
- `git for-each-ref --sort=taggerdate --format '%(refname)' refs/tags` Show all tags in time order 
- `grep "b-"` Just display B tags
- `tac` This command reverses the output of the last command so the latest is now on top.
- `sed -n 1p` This sed command plucks out the first line from the output of the previous command.

**Determing changes since the last tag**
Pipeline X should only operate on files which have been added, changed/modified since the last time X applied an X tag.
```bash
git diff HEAD $LATEST_TAG --name-only --diff-filter=ACM | grep b_dir
```
The above command will list all files which have been (A)dded (C)hanged Name or (M)odified, only for B.

**Tagging**
Once pipeline X completes it should tag the repo with an X-tag. For example, B shown below.

```bash
git tag -a "b-`date +%F-T%H-%M-%S`" -m "tag"
```
```bash
#output
b-2017-07-29-T01-16-11
```






 
