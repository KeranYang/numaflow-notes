### Program against a remote feature branch

Let's say the parent repo has a feature branch called `partitions`

1. Fork the repo, Sync.
2. Manually create branch `partitions` at the forked repo.
3. Click on sync fork.
4. Create local branch `partitions`

```
git checkout -b partitions
```

5. Set upstream to origin/partitions

```
git branch -u origin/partitions
// branch 'partitions' set up to track 'origin/partitions'.
```

6. Now, the local branch could be very different from the tracking branch. In this case, we don't mind having conflicts, the goal is to sync.

```
Your branch and 'origin/partitions' have diverged,
and have 187 and 4 different commits each, respectively.
  (use "git pull" if you want to integrate the remote branch with yours)
```

7. Tell Git to reset the head of `partitions` to the same commit as `origin/partitions`, and update the working tree to match.

```
git reset --hard origin/partitions
```

8. Now, create a new branch to start my work.

```
git checkout -b partitions-1
```

9. Make changes and commit.
10. Push.

```
git push --set-upstream origin partition-1
```

