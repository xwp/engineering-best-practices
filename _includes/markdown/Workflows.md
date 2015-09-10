We consider standardizing a workflow to be a very important part of the development process. Utilizing an effective workflow ensures efficient collaboration and quicker project onboarding. For this reason we use the following workflows company-wide both for internal and client projects.

### Automated Testing

The number one line of defense for good quality code that adheres to best practices and coding standards is *automated testing*. Your code must adhere to the coding standards for [PHP](../php/#code-style), [JS](../javascript/#code-style), [HTML](../markup/), and [CSS](../css/#syntax-formatting)

Configure your [IDE integration for code checkers](../tools/#editors). Our repos should each include the [wp-dev-lib](https://github.com/xwp/wp-dev-lib) which includes a [`pre-commit`](https://github.com/xwp/wp-dev-lib/blob/master/pre-commit) hook that runs before a commit is made, blocking it it encounters issues (note this can be bypassed via the `--no-verify` arg to `git-commit`). The `pre-commit` hook is not installed automatically when a Git repo is cloned. To install it you just run:

```bash
git submodule update --init && cd .git/hooks && ln -s ../../dev-lib/pre-commit . && cd -
```

Travis CI should also be configured to automatically run the PHPUnit tests for the code in the pull request so that GitHub can indicate whether the tests pass. Ask your friendly GitHub admin to set this up for you.

#### Unit and Integration Testing

Unit testing is the automated testing of units of source code against certain assertions. The goal of unit testing is to write test cases with assertions that test if a unit of code is truly working as intended. If an assertion fails, a potential issue is exposed, and code needs to be revised.

For specifics on how to write tests, see [PHP unit testing](../php/#unit-testing) and [JS unit testing](../javascript/#unit-and-integration-testing).

By definition, unit tests do not have dependencies on outside systems; in other words, only your code (a single unit of code) is being tested. Integration testing works similarly to unit tests but assumptions are tested against systems of code, moving parts, or an entire application. The phrases unit testing and integration testing are often misused to reference one another especially in the context of WordPress, since the tests in WordPress itself are a mix of unit and integration tests.

Unit tests should be written for all plugins, whether they are for public distribution or for private client projects. As noted in [modular code](../structure/#modular-code), themes should be devoid of functional logic and so they are not applicable for unit tests.

Write your (plugin) code in a way that makes it testable. It is much easier to write unit tests while (or even before, à la [TDD](https://en.wikipedia.org/wiki/Test-driven_development)) you write your functional code. Unit tests help guide best practices for code architecture: testable code is more often well-architected code.

* Organize your plugin code into objects that incorporate the [dependency injection pattern](http://jasonpolites.github.io/tao-of-testing/ch3-1.1.html); this allows unit tests to supply mocks for the objects that aren't specifically being tested.
* Write methods that are small as possible, that do one thing and do it well.
* Shy away interacting with global variables, though in WordPress this is often a painful reality we have to just live with.
* A functions should be a black box that takes an input and returns an output. You can then test the outputs for any given inputs.

It is always a challenge to ensure that unit tests have complete testing coverage for a plugin. It is often not worthwhile to require 100% coverage, but rather to focus on testing the key parts of the plugin's logic (as opposed to testing basic things like metabox registration).

When the issue being worked on us is a defect, a best practice is to first write a failing unit test that demonstrates the bug, and then fix the code so that the unit test passes.

### Code Review

Nobody is perfect. Even the most experienced engineer can forget some detail. We try to mitigate this as much as possible with [automated testing](#automated-testing), but code checkers can only catch low-hanging fruit. A fellow engineer is really needed to do a review of the changes before it gets sent along for QA and the client. With a fresh pair of eyes, a peer code reviewer can easily spot things that don't make sense or implementations that don't satisfy the requirements as stated in the issue being addressed (e.g. in JIRA), as interpreted by the reviewer.

The code reviewer shouldn't necessarily be testing the code in depth and making sure it satisfies all of the requirements: when a [testing (QA)](#qa) team is involved in a project this is their responsibility. In any case, the key thing is that code should be reviewed by a colleague at least once before it gets sent along for client review (or WordPress.com VIP commit), and especially before it goes to production.

Code review should especially identify security and performance issues (as would get raised by WordPress VIP) that may not be apparent through testing at QA level or not easy for a tester to articulate. For WordPress.com, identifying technical issues that violate VIP's [requirements](https://vip.wordpress.com/documentation/code-review-what-we-look-for/) will speed up the deployment process since Zendesk tickets will be less-frequently opened for needed fixes; this makes our friends at VIP happy and it makes clients happy since commits will be deployed sooner and even in line with a timetable they have.

Code review is also an opportunity for ensuring that another engineers on the project team have familiarity with the code being contributed, thus increasing the [bus factor](https://en.wikipedia.org/wiki/Bus_factor) and improving our ability/availability to maintain the codebase.

Code reviewers should make sure that [unit tests](#unit-and-integration-testing) are included, and that they cover the key changes introduced in the branch. The `pre-commit` hook and Travis CI will have already checked the coding conventions and whether any included unit tests are failing.

All code review should be done in the context of a *pull request* (PR). Feedback on the changes should be provided as comments on the pull request, especially as inline comments for the specific lines that need to be addressed. When feedback is added, the PR should be assigned back to the developer to fix, if the code reviewer doesn't feel they can fix up the issues themselves. However, it is the responsibility of the original engineer to only assign a pull request for code review once they are confident that it is in a state where it can be merged. The developer should not rely on the code reviewer to harden and fix up issues that they know are present in the code; in the same way, if Travis reports failures on the pull request, it cannot be assigned for code review.

Being a distributed team working across many timezones we have the advantage of being able to work on a feature in one timezone and then pass it along for code review in another timezone. When project teams are put together in such a way it allows for timezone collaboration it is a strength as we can have work happening around-the-clock speeding up our turnaround time. It can also be a disadvantage when code is not ready for merging when the code reviewer picks up the work at the start of his/her day: the code reviewer would have no choice but to assign the PR back for fixes, resulting in a drawn-out 24-hour feedback loop. For minor issues, the code reviewer should therefore fix them during the review and merge away to limit the feedback loop. The original developer should be sure to take note of the changes that the code reviewer made so that they can refine their skills and may apply them for the next time.

This does not mean that pull requests should not be opened before they are in a mergable state. Not at all. It is very good to *open your PRs early*, but leave them unassigned and marked as WIP (work in progress): this WIP PR provides a way for the engineer to solicit feedback during development to make sure they are going down the right track.

### QA

Since developers have a [local development environment](../tools/#local-development-environments) powered by Vagrant, code reviewers will be able to QA of the changes on their machines with confidence that it will work properly if pushed directly to production (with the big caveat that the content from the production database probably wouldn't be used in testing).

As noted in [code review](#code-review), when there is dedicated QA team involved in a project, the code reviewer should not do in-depth validation that the changes satisfy the requirements since this is the QA responsibility. They are also equipped to do all the cross-browser testing.

Since the QA team most likely doesn't have a local development environment, and neither should they be expected to, it is critical for there to be an external preview environment available where code changes can be deployed for review before the code gets pushed to production.

For projects hosting on *WordPress.com VIP*, the [Quickstart environment](https://vip.wordpress.com/documentation/quickstart/) they provide for local development (via Vagrant) is also designed to be [provisioned on AWS EC2 instance](https://vip.wordpress.com/documentation/quickstart/staging-environment/), and so this is what we use. We install a given site's theme into this Quickstart environment by cloning it from GitHub. Now, the `master` branch for these site repos corresponds to what is currently in SVN, which makes this branch not suitable for our preview environment.

So we have standardized on each theme repo having a `preview` branch which is what is checked out on the preview server. When new commits appear in the `preview` branch, a GitHub webhook kicks in and pings the server to checkout the latest from this branch via `git fetch && git checkout origin/preview`. Note that new commits should not be made directly to the `preview` branch. Commits should only ever be made to feature branches off of `master`. When a feature branch is ready for QA, it can be merged into `preview` so that it will appear on the preview environment. The pull request for the feature branch into `master` will remain open until it passes QA. (Protip: Don't merge feature branches into `preview` by means of pull requests since this just adds GitHub notification noise and the PRs would be merged immediately anyway without code review; so instead, do the merges directly from the command line, even [use a script to make it easy](https://gist.github.com/westonruter/dd264e10b1e2c2388a6a).)

Again, the `preview` branch is a dead-end branch; it should never get merged back into `master` or any other branch. It's purpose is to review changes to feature/bugfix branches before they get merged into `master`. The `preview` branch should always have a superset of the commits that are in `master`. Periodically the `preview` branch will need to get cleaned up and reset to `master` (via `git checkout preview && git reset --hard master && git push -f`). When this is done, make sure that any open pull request branches get re-merged into `preview`.

For sites hosted on *Pantheon*, this is part of the [their platform](https://pantheon.io/docs/articles/sites/code/using-the-pantheon-workflow/): all sites have a dev environment, test (preview/staging) environment, and live (production) environment. We still house our Pantheon site projects on GitHub, but [configure Travis CI](https://github.com/xwp/pantheon-wordpress-upstream/blob/master/.travis.yml) to automatically push code from GitHub to the Pantheon dev environment after Travis CI passes all of the [code checks](../tools/#code-checkers) (in `after_success`). We can also set up Travis CI to automatically pick up pushes to the `preview` branch and push them to a Pantheon `preview` [multidev environment](https://pantheon.io/docs/articles/sites/multidev/), which will allow us to keep features from being merged into `master` before they have passed QA.

### Version Control

We version control all projects using [Git](http://git-scm.com/). Version control allows us to track codebase history, maintain parallel tracks of development, and collaborate without stomping out each others changes.

#### Commits

Commits should be small and independent items of work. Distinct items of work are essential in keeping features separate, which will aid in reversing or rolling back commits if necessary or pushing specific features forward.

Do not wait more than a day to push commits. _Push early and push often._ Not only is it important to show progress for our work, it's also important for collaboration: when you sign off another developer may want to pick up where you left off, but this is not possible if you have not pushed up your latest work in progress.

Try to keep your commits as logically self-contained as possible. If you have made a lot of changes which you have not committed yet, you can step through the changes via `git add -p` to selectively stage chunks for committing. In this way you can group your changes into commits. Using `git add -p` is also just plain handy to stage changes for commit in general because it helps prevent accidentally committing something you do not intend; for the same reason, `git commit -a` should be avoided because you don't have the explicit step to review what you are committing. Remember you can do `git diff --staged` right before commit for one last review.

##### Commit Messages

The gist of a great commit message is this:

Capitalized, short (50 chars or less) summary

More detailed explanatory text, if necessary but most likely necessary if you want to be a good citizen! Wrap it to about 72 characters or so. In some contexts, the first line is treated as the subject of an email and the rest of the text as the body. The blank line separating the summary from the body is critical (unless you omit the body entirely); tools like rebase can get confused if you run the two together.

_JIRA-TASK-ID_ or _#github-issue-id_ or _#WordPress-Ticket-ID_

Write your commit message in the present tense: `"Fix bug"` and not `"Fixed bug"`. This convention matches up with commit messages generated by commands like git merge and git revert. Use the imperative mood (`"Move cursor to..."` not `"Moves cursor to..."`)

You may also see [Core Handbook on Commit Messages](https://make.wordpress.org/core/handbook/best-practices/commit-messages/)

Bonus points are given for starting the commit message with an applicable emoji when the code is pushed over to GitHub and the client is OK with them:

* `:art:` when improving the format/structure of the code.
* `:racehorse:` when improving performance.
* `:non-potable_water:` when plugging memory leaks.
* `:memo:` when writing docs
* `:bug:` when fixing a bug
* `:white_check_mark:` when adding tests


#### Branches

All projects will treat the ```master``` branch as the canonical source for live, released, stable, production code.

Projects may have a `develop` branch off of `master` where feature branches get merged and integrated before being merged into `master`. So feature branches would be made off of `develop` if it is used in a project, but otherwise feature branches would be made off of `master`. Take a look at [understanding](https://guides.github.com/introduction/flow/) the [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html) which is a more simplified than a more traditional [Git branching model](http://nvie.com/posts/a-successful-git-branching-model/). Compare these to help decide whether a `develop` branch is appropriate in your project.

In addition to the `master`, `develop`, and feature branches, we also utilize a `preview` branch for [QA](#qa).

#### Merges

In order to avoid large merge conflicts, merges should occur early and often. Do not wait until a long-in-development feature is complete to merge ```master``` into it. All merging should be done by means of pull requests, so you don't need to worry about non-fast-forward (`--no-ff`) merges; this doesn't apply for the `preview` branch, which is a throw-away branch anyway.

If your commits haven't been pushed yet for others to see, it can be a good idea to update your branch from `master` by means of `git rebase master`. This prevents your branch from getting cluttered with merge commits from `master`. Be careful if you rebase commits after having pushed to a remote, as if others have the feature branch on their machines, they will likely get ugly merge conflicts when they try to update their feature branch. This is because `git-rebase` rewrites the history. (These same cautions go for using `git commit --amend`.)

When things are absolutely ready to go (i.e. it has passed code review, QA and UAT), we'll then merge the pull request into `master`.

When a merge is made into `master` and the project uses [semantic versioning](http://semver.org/), then the merge should be accompanied by a Git *tag*.

###### Complex Feature Branches

In some cases, a feature will be large enough to warrant multiple developers working on it at the same time. In order to enable testing the feature as a cohesive unit and avoid merge conflicts when pushing to ```preview``` and ```master``` it is recommended to create a feature branch to act as a staging area: this is the primary feature branch. Then, as necessary, create additional branches from the feature branch for distinct items of work. When individual items are complete, merge back to the feature branch. To pull work from ```master```, merge ```master``` into the feature branch and then merge the feature branch into the individual branches. When all work has been merged back into the feature branch, the feature branch can then be merged into ```preview``` and ```master``` as an entire unit of work.

##### Branch Cleanup

This workflow will inevitably build up a large list of branches in the repository. To prevent a large number of unused branches living in the repository, we'll delete them after feature development is complete. Since all feature branches should get merged by means of pull requests, there will be a persistent reference to the original branch in the pull request. So after merging the pull request, delete the branch from Git. It can be restored later via the pull request.

#### Working with WordPress.com VIP

Sites hosted by WordPress.com VIP use SVN for version control and deployments. For development, however, we continue to use Git. We do not have SVN commits match 1:1 with the commits in Git, as Git commits are often far more granular than is normal for SVN. Instead, we aim for entire Git feature branch merges to correspond to a single SVN commit. In this way, commits to SVN are analogous Git [squashed commits](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History#Squashing-Commits).

##### Backporting VIP

In the event that VIP makes a change to the repository, we'll capture the diff of their changeset and import it to our development repository by:

* Grab a diff of their changes.
* Create a new temporary branch off ```master```.
* Apply the patch; if it doesn't apply, reset the branch to the commit where it doesn't and re-apply, or just manually fix the conflicts.
* Commit the changes indicating the Zendesk ticket and the VIP Code Wrangler's authorship (via `git commit --author="John Smith <john.smith@automattic.com>"`).
* Cherry-pick the commit into `master` and delete the temporary branch.

### Deploying Plugins

When a new version is complete and ready to ship, update version slugs on ```develop```, then merge ```develop``` back to ```master``` (using a non-fast-forward merge). You can tag the merge commit with the version number being released so we can keep track of where new versions land. You can use the [`svn-push`](https://github.com/xwp/wp-dev-lib/blob/master/svn-push) script in [wp-dev-lib](https://github.com/xwp/wp-dev-lib) to facilitate the syncing of plugins to WordPress.org. This integration can be even [automated by Travis CI](https://github.com/xwp/wp-dev-lib/issues/106).

### Issue Lifecycle

For plugin projects we generally just use GitHub issues, but for client projects and internal sites we almost always use *JIRA* for issue tracking.

Our normal workflow consists of the following JIRA workflow, where a ticket will generally transition from start to finish, although some backtracking does happen, for instance an ticket moving to QA but then getting sent back for development. The JIRA workflow is:

1. *To Do*: Issue has not been worked on yet.
1. *In Development*: Issue is assigned to an engineer and work is being done.
1. *Code Review*: Engineer has finished the work and has opened a pull request, re-assigning the issue to a peer for review.
1. *QA*: Feature branch has been merged into `preview` and is now on the preview server, and issue is assigned to testers for verification.
1. *UAT*: Issue is assigned to client for review.
1. *Queued for Deployment*: Issue has passed client review and the PR can be merged into `master`.
1. *In VIP Review*: (WordPress.com VIP only) Code for issue has been committed to SVN and is now in the deploy queue.
1. *Production*: Issue has been deployed. QA and client can re-verify.
1. *Done*: Issue is closed.
