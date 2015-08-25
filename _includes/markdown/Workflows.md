We version control all projects using [Git](http://git-scm.com/). Version control allows us to track codebase history, maintain parallel tracks of development, and collaborate without stomping out each others changes.

We consider standardizing a workflow to be a very important part of the development process. Utilizing an effective workflow ensures efficient collaboration and quicker project onboarding. For this reason we use the following workflows company-wide both for internal and client projects.

#### Commits

Commits should be small and independent items of work. Distinct items of work are essential in keeping features separate, which will aid in reversing or rolling back commits if necessary or pushing specific features forward.

#### Merges

In order to avoid large merge conflicts, merges should occur early and often. Do not wait until a feature is complete to merge ```master``` into it.

If your commits haven't been pushed yet for others to see, it is a good idea to update your branch from `master` by means of `git rebase master`. This prevents your branch from getting cluttered with merge commits from `master`. Be careful if you rebase commits after having pushed to a remote, as if others have the feature branch on their machines, they will likely get ugly merge conflicts when they try to update their feature branch. This is because `git-rebase` rewrites the history. (These same cautions go for using `git commit --amend`.)

#### Themes

All new development should take place on feature branches that branch off ```master```. For many projects, when a new feature or bugfix is deemed complete we will do merge from that branch to the```preview``` branch and push to verify the feature or fix on the stage environment before it gets merged into `master` and locked into a path to production. We have the `preview` branch set up to auto-deploy to our preview environment.

When things are absolutely ready to go (i.e. it has passed code review, QA and UAT), we'll then merge the pull request into `master`.

##### Branching

All theme projects will treat the ```master``` branch as the canonical source for live, production code. Feature branches will branch off ```master```.

The `preview` branch is a dead-end branch; it should never get merged back into `master` or any other branch. It's purpose is to review changes to feature/bugfix branches before they get merged into `master`. The `preview` branch should always have a superset of the commits that are in `master`.

Periodically the `preview` branch will need to get cleaned up and reset to `master` (via `git checkout preview && git reset --hard master && git push -f`). When this is done, make sure that any open pull request branches get re-merged into `preview`.

###### Complex Feature Branches

In some cases, a feature will be large enough to warrant multiple developers working on it at the same time. In order to enable testing the feature as a cohesive unit and avoid merge conflicts when pushing to ```staging``` and ```master``` it is recommended to create a feature branch to act as a staging area. We do this by branching from ```master``` to create the primary feature branch, and then as necessary, create additional branches from the feature branch for distinct items of work. When individual items are complete, merge back to the feature branch. To pull work from ```master```, merge ```master``` into the feature branch and then merge the feature branch into the individual branches. When all work has been merged back into the feature branch, the feature branch can then be merged into ```preview``` and ```master``` as an entire unit of work.

##### Working with WordPress.com VIP

Sites hosted by WordPress.com VIP use SVN for version control and deployments. For development, however, we continue to use Git. We do not have SVN commits match 1:1 with the commits in Git, as Git commits are often far more granular than is normal for SVN. Instead, we aim for entire Git feature branch merges to correspond to a single SVN commit. In this way, commits to SVN are analogous Git [squashed commits](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History#Squashing-Commits).

##### Backporting VIP

In the event that VIP makes a change to the repository, we'll capture the diff of their changeset and import it to our development repository by:

* Grab a diff of their changes.
* Create a new temporary branch off ```master```.
* Apply the patch; if it doesn't apply, reset the branch to the commit where it doesm and re-apply.
* Commit the changes indicating the Zendesk ticket and the VIP Code Wrangler's' authorship (via `git commit --author`).
* Cherry-pick the branch into `master`.

##### Branch Cleanup

This workflow will inevitably build up a large list of branches in the repository. To prevent a large number of unused branches living in the repository, we'll archive them after feature development is complete. Since all feature branches should get merged by means of pull requests, there will be a persistent reference to the original branch in the pull request. So after merging the pull request, delete the branch from Git. It can be restored later via the pull request.

#### Plugins

Unlike theme development, the ```master``` branch represents a stable, released, versioned product. Ongoing development will happen on a ```develop``` branch, which is itself branched off ```master```.

##### Branching

New features should be branched off ```develop``` and, once complete, merged back into ```develop``` using a non-fast-forward merge.

##### Deploying

When a new version is complete and ready to ship, update version slugs on ```develop```, then merge ```develop``` back to ```master``` (using a non-fast-forward merge). Tag the merge commit with the version number being released so we can keep track of where new versions land.

Once a version is tagged and released, the tag must never be removed. If there is a problem with the project requiring a re-deployment, create a new version and tag to reflect the change.
