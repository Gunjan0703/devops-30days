# Branching
Enumerating objects: 5, done.
PS D:\git> git branch
* main
PS D:\git> git branch new_branch
PS D:\git> git branch
* main
  new_branch
PS D:\git> git checkout mew_branch
error: pathspec 'mew_branch' did not match any file(s) known to git
PS D:\git> git checkout new_branch
Switched to branch 'new_branch'
PS D:\git> git branch
  main
* new_branch
PS D:\git> git status
On branch new_branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        style.css

no changes added to commit (use "git add" and/or "git commit -a")
PS D:\git> git add *
PS D:\git> git status
On branch new_branch
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   index.html
        new file:   style.css

PS D:\git> git commit -m"added design foe website"
[new_branch 1538f0f] added design foe website
 2 files changed, 31 insertions(+), 1 deletion(-)
 create mode 100644 style.css
PS D:\git> git log
commit 1538f0f5fc2b2eddd4d830754679b72d08f45838 (HEAD -> new_branch)
Author: Gunjan <gunjanshikha512@gmail.com>
Date:   Fri Jul 18 15:22:49 2025 +0530

    added design foe website

commit e619096926dd6d48dc9aa764a6160d1beb99af2a (origin/main, origin/HEAD, main)
Author: Gunjan <gunjanshikha512@gmail.com>
Date:   Fri Jul 18 15:04:05 2025 +0530

    v6

commit 523af98772b03b916a665031f8c8f94ba73883ef
Author: Gunjan0703 <164139238+Gunjan0703@users.noreply.github.com>
Date:   Fri Jul 18 14:44:36 2025 +0530

    Create test file

commit ed5f33fec40953ab755081d2a9f13124aa45941a
Author: Gunjan <gunjanshikha512@gmail.com>
Date:   Fri Jul 18 14:39:46 2025 +0530

    v5

commit 5f1e2fa7e687284b0b9ab8a400a3f0d73df24fa7
Author: Gunjan <gunjanshikha512@gmail.com>
Date:   Fri Jul 18 12:10:12 2025 +0530

    v4

commit 9499cb0c894c0e09e3ddc0779eaed3466feeb3f1
Author: Gunjan <gunjanshikha512@gmail.com>
Date:   Fri Jul 18 11:36:30 2025 +0530

    this is v3

commit 50c92fa12a53eca8fcba30494ceb8af5d8f3b27e
Author: Gunjan <gunjanshikha512@gmail.com>
Date:   Fri Jul 18 11:33:27 2025 +0530

    this is version 2

commit cdb613b48d14729a7d7e387623cca70d4d40c2ad
Author: Gunjan <gunjanshikha512@gmail.com>
Date:   Tue Jul 1 13:01:08 2025 +0530

    first v1 of index.html
PS D:\git> git branch
  main
* new_branch
PS D:\git> git branch main
fatal: a branch named 'main' already exists
PS D:\git> git chekout main
git: 'chekout' is not a git command. See 'git --help'.

The most similar command is
        checkout
#Merging
PS D:\git> git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
PS D:\git> git checkout new_branch
Switched to branch 'new_branch'
PS D:\git> git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
PS D:\git> git merge new_branch
Updating e619096..1538f0f
Fast-forward
 index.html |  4 +++-
 style.css  | 28 ++++++++++++++++++++++++++++
 2 files changed, 31 insertions(+), 1 deletion(-)
 create mode 100644 style.css
PS D:\git> git push
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 8 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 845 bytes | 281.00 KiB/s, done.
Total 4 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/Gunjan0703/myrepo.git
   e619096..1538f0f  main -> main
PS D:\git> git checkout new_branch
Switched to branch 'new_branch'
PS D:\git> git add
Nothing specified, nothing added.
hint: Maybe you wanted to say 'git add .'?
hint: Disable this message with "git config set advice.addEmptyPathspec false"
PS D:\git> git add index.html 
PS D:\git> git status
On branch new_branch
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   index.html

PS D:\git> git commit -m"changed h1"
[new_branch db5250b] changed h1
 1 file changed, 1 insertion(+), 1 deletion(-)
PS D:\git> git push origin design
error: src refspec design does not match any
error: failed to push some refs to 'https://github.com/Gunjan0703/myrepo.git'
PS D:\git> git push origin new_branch
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 8 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 358 bytes | 358.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
remote:
remote: Create a pull request for 'new_branch' on GitHub by visiting:
remote:      https://github.com/Gunjan0703/myrepo/pull/new/new_branch
remote:
To https://github.com/Gunjan0703/myrepo.git
 * [new branch]      new_branch -> new_branch
PS D:\git> git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
PS D:\git> git pull 
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (1/1), 896 bytes | 298.00 KiB/s, done.
From https://github.com/Gunjan0703/myrepo
   1538f0f..b2359a1  main       -> origin/main
Updating 1538f0f..b2359a1
Fast-forward
 index.html | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
PS D:\git> git checkout new_branch
Switched to branch 'new_branch'
PS D:\git> git status
On branch new_branch
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html

no changes added to commit (use "git add" and/or "git commit -a")
PS D:\git> git add * 
PS D:\git> git commit -m"change version to 6.1"
[new_branch 8685011] change version to 6.1
 1 file changed, 1 insertion(+), 1 deletion(-)
PS D:\git> git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
PS D:\git> git status
On branch main
Your branch is up to date with 'origin/main'.

On branch main
Your branch is up to date with 'origin/main'.

Your branch is up to date with 'origin/main'.


Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   index.html

no changes added to commit (use "git add" and/or "git commit -a")
no changes added to commit (use "git add" and/or "git commit -a")
PS D:\git> git add *
PS D:\git> git commit -m"changed version to 7"
[main 7526fc5] changed version to 7
[main 7526fc5] changed version to 7
 1 file changed, 1 insertion(+), 1 deletion(-)
 1 file changed, 1 insertion(+), 1 deletion(-)
PS D:\git> git merge new_branch
PS D:\git> git merge new_branch
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
PS D:\git> git status
On branch main
Your branch is ahead of 'origin/main' by 3 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
PS D:\git> git commit
On branch main
Your branch is ahead of 'origin/main' by 3 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
PS D:\git> git checkout new_branch
Switched to branch 'new_branch'
PS D:\git> git checkout main
Switched to branch 'main'
Your branch is ahead of 'origin/main' by 3 commits.
  (use "git push" to publish your local commits)
PS D:\git> git push
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 8 threads
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 854 bytes | 284.00 KiB/s, done.
Total 7 (delta 3), reused 0 (delta 0), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (3/3), completed with 1 local object.
To https://github.com/Gunjan0703/myrepo.git
   b2359a1..6329832  main -> main
PS D:\git> git pull
Already up to date.
PS D:\git> 
