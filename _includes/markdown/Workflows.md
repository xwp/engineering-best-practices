We consider standardizing a workflow to be a very important part of the development process. Utilizing an effective workflow ensures efficient collaboration and quicker project onboarding. For this reason we use the following workflows company-wide both for internal and client projects.

<h3 id="automated-testing">Automated Testing {% include Util/top %}</h3>

The number one line of defense for good quality code that adheres to best practices and coding standards is *automated testing*. Your code must adhere to the coding standards for [PHP](../php/#code-style), [JS](../javascript/#code-style), [HTML](../markup/), and [CSS](../css/#syntax-formatting)

Configure your [IDE integration for code checkers](../tools/#editors). Our repos should each include the [wp-dev-lib](https://github.com/xwp/wp-dev-lib) which includes a [`pre-commit`](https://github.com/xwp/wp-dev-lib/blob/master/pre-commit) hook that runs before a commit is made, blocking it it encounters issues (note this can be bypassed via the `--no-verify` arg to `git-commit`). The `pre-commit` hook is not installed automatically when a Git repo is cloned. To install it you just run:

```bash
git submodule update --init && cd .git/hooks && ln -s ../../dev-lib/pre-commit . && cd -
```

Travis CI should also be configured to automatically run the PHPUnit tests for the code in the pull request so that GitHub can indicate whether the tests pass. Ask your friendly GitHub admin to set this up for you.

<h4 id="unit-and-integration-testing"> Unit and Integration Testing</h4>

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

<h3 id="code-review">Code Review {% include Util/top %}</h3>

Nobody is perfect. Even the most experienced engineer can forget some detail. We try to mitigate this as much as possible with [automated testing](#automated-testing), but code checkers can only catch low-hanging fruit. A fellow engineer is really needed to do a review of the changes before it gets sent along for QA and the client. With a fresh pair of eyes, a peer code reviewer can easily spot things that don't make sense or implementations that don't satisfy the requirements as stated in the issue being addressed (e.g. in JIRA), as interpreted by the reviewer.

The code reviewer shouldn't necessarily be testing the code in depth and making sure it satisfies all of the requirements: when a [testing (QA)](#qa) team is involved in a project this is their responsibility. In any case, the key thing is that code should be reviewed by a colleague at least once before it gets sent along for client review (or WordPress.com VIP commit), and especially before it goes to production.

Code review should especially identify security and performance issues (as would get raised by WordPress VIP) that may not be apparent through testing at QA level or not easy for a tester to articulate. For WordPress.com, identifying technical issues that violate VIP's [requirements](https://vip.wordpress.com/documentation/code-review-what-we-look-for/) will speed up the deployment process since Zendesk tickets will be less-frequently opened for needed fixes; this makes our friends at VIP happy and it makes clients happy since commits will be deployed sooner and even in line with a timetable they have.

Code review is also an opportunity for ensuring that another engineers on the project team have familiarity with the code being contributed, thus increasing the [bus factor](https://en.wikipedia.org/wiki/Bus_factor) and improving our ability/availability to maintain the codebase.

Code reviewers should make sure that [unit tests](#unit-and-integration-testing) are included, and that they cover the key changes introduced in the branch. The `pre-commit` hook and Travis CI will have already checked the coding conventions and whether any included unit tests are failing.

All code review should be done in the context of a *pull request* (PR). Feedback on the changes should be provided as comments on the pull request, especially as inline comments for the specific lines that need to be addressed. When feedback is added, the PR should be assigned back to the developer to fix, if the code reviewer doesn't feel they can fix up the issues themselves. However, it is the responsibility of the original engineer to only assign a pull request for code review once they are confident that it is in a state where it can be merged. The developer should not rely on the code reviewer to harden and fix up issues that they know are present in the code; in the same way, if Travis reports failures on the pull request, it cannot be assigned for code review.

This does not mean that pull requests should not be opened before they are in a mergable state. Not at all. It is very good to *open your PRs early*, but leave them unassigned and marked as WIP (work in progress): this WIP PR provides a way for the engineer to solicit feedback during development to make sure they are going down the right track.

<h3 id="qa">QA {% include Util/top %}</h3>

Since developers have a [local development environment](../tools/#local-development) powered by Vagrant, code reviewers will be able to QA of the changes on their machines with confidence that it will work properly if pushed directly to production (with the big caveat that the content from the production database probably wouldn't be used in testing).

As noted in [code review](#code-review), when there is dedicated QA team involved in a project, the code reviewer should not do in-depth validation that the changes satisfy the requirements since this is the QA responsibility. They are also equipped to do all the cross-browser testing.

Since the QA team most likely doesn't have a local development environment, and neither should they be expected to, it is critical for there to be an external preview environment available where code changes can be deployed for review before the code gets pushed to production.

For projects hosting on *WordPress.com VIP*, the [Quickstart environment](https://vip.wordpress.com/documentation/quickstart/) they provide for local development (via Vagrant) is also designed to be [provisioned on AWS EC2 instance](https://vip.wordpress.com/documentation/quickstart/staging-environment/), and so this is what we use. We install a given site's theme into this Quickstart environment by cloning it from GitHub. Now, the `master` branch for these site repos corresponds to what is currently in SVN, which makes this branch not suitable for our preview environment.

So we have standardized on each theme repo having a `preview` branch which is what is checked out on the preview server. When new commits appear in the `preview` branch, a GitHub webhook kicks in and pings the server to checkout the latest from this branch via `git fetch && git checkout origin/preview`. Note that new commits should not be made directly to the `preview` branch. Commits should only ever be made to feature branches off of `master`. When a feature branch is ready for QA, it can be merged into `preview` so that it will appear on the preview environment. The pull request for the feature branch into `master` will remain open until it passes QA.

Again, the `preview` branch is a dead-end branch; it should never get merged back into `master` or any other branch. It's purpose is to review changes to feature/bugfix branches before they get merged into `master`. The `preview` branch should always have a superset of the commits that are in `master`. Periodically the `preview` branch will need to get cleaned up and reset to `master` (via `git checkout preview && git reset --hard master && git push -f`). When this is done, make sure that any open pull request branches get re-merged into `preview`.

For sites hosted on *Pantheon*, this is part of the [their platform](https://pantheon.io/docs/articles/sites/code/using-the-pantheon-workflow/): all sites have a dev environment, test (preview/staging) environment, and live (production) environment. We still house our Pantheon site projects on GitHub, but [configure Travis CI](https://github.com/xwp/pantheon-wordpress-upstream/blob/master/.travis.yml) to automatically push code from GitHub to the Pantheon dev environment after Travis CI passes all of the [code checks](../tools/#code-checkers) (in `after_success`). We can also set up Travis CI to automatically pick up pushes to the `preview` branch and push them to a Pantheon `preview` [multidev environment](https://pantheon.io/docs/articles/sites/multidev/), which will allow us to keep features from being merged into `master` before they have passed QA.

<h3 id="version-control">Version Control {% include Util/top %}</h3>

We version control all projects using [Git](http://git-scm.com/). Version control allows us to track codebase history, maintain parallel tracks of development, and collaborate without stomping out each others changes.

#### Commits

Commits should be small and independent items of work. Distinct items of work are essential in keeping features separate, which will aid in reversing or rolling back commits if necessary or pushing specific features forward.

#### Merges

In order to avoid large merge conflicts, merges should occur early and often. Do not wait until a long-in-development feature is complete to merge ```master``` into it.

If your commits haven't been pushed yet for others to see, it can be a good idea to update your branch from `master` by means of `git rebase master`. This prevents your branch from getting cluttered with merge commits from `master`. Be careful if you rebase commits after having pushed to a remote, as if others have the feature branch on their machines, they will likely get ugly merge conflicts when they try to update their feature branch. This is because `git-rebase` rewrites the history. (These same cautions go for using `git commit --amend`.)

#### Themes

All new development should take place on feature branches that branch off ```master```. For many projects, when a new feature or bugfix is deemed complete we will do merge from that branch to the```preview``` branch and push to verify the feature or fix on the stage environment before it gets merged into `master` and locked into a path to production. We have the `preview` branch set up to auto-deploy to our preview environment.

When things are absolutely ready to go (i.e. it has passed code review, QA and UAT), we'll then merge the pull request into `master`.

##### Branching

All theme projects will treat the ```master``` branch as the canonical source for live, production code. Feature branches will branch off ```master```.

For more information on the `preview` branch, see [QA](#qa).

###### Complex Feature Branches

In some cases, a feature will be large enough to warrant multiple developers working on it at the same time. In order to enable testing the feature as a cohesive unit and avoid merge conflicts when pushing to ```staging``` and ```master``` it is recommended to create a feature branch to act as a staging area. We do this by branching from ```master``` to create the primary feature branch, and then as necessary, create additional branches from the feature branch for distinct items of work. When individual items are complete, merge back to the feature branch. To pull work from ```master```, merge ```master``` into the feature branch and then merge the feature branch into the individual branches. When all work has been merged back into the feature branch, the feature branch can then be merged into ```preview``` and ```master``` as an entire unit of work.

##### Working with WordPress.com VIP

Sites hosted by WordPress.com VIP use SVN for version control and deployments. For development, however, we continue to use Git. We do not have SVN commits match 1:1 with the commits in Git, as Git commits are often far more granular than is normal for SVN. Instead, we aim for entire Git feature branch merges to correspond to a single SVN commit. In this way, commits to SVN are analogous Git [squashed commits](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History#Squashing-Commits).

##### Backporting VIP

In the event that VIP makes a change to the repository, we'll capture the diff of their changeset and import it to our development repository by:

* Grab a diff of their changes.
* Create a new temporary branch off ```master```.
* Apply the patch; if it doesn't apply, reset the branch to the commit where it doesn't and re-apply, or just manually fix the conflicts.
* Commit the changes indicating the Zendesk ticket and the VIP Code Wrangler's authorship (via `git commit --author="John Smith <john.smith@automattic.com>"`).
* Cherry-pick the commit into `master` and delete the temporary branch.

##### Branch Cleanup

This workflow will inevitably build up a large list of branches in the repository. To prevent a large number of unused branches living in the repository, we'll archive them after feature development is complete. Since all feature branches should get merged by means of pull requests, there will be a persistent reference to the original branch in the pull request. So after merging the pull request, delete the branch from Git. It can be restored later via the pull request.

#### Plugins

Unlike theme development, the ```master``` branch represents a stable, released, versioned product. Ongoing development will happen on a ```develop``` branch, which is itself branched off ```master```.

##### Branching

New features should be branched off ```develop``` and, once complete, merged back into ```develop``` using a non-fast-forward merge.

##### Deploying

When a new version is complete and ready to ship, update version slugs on ```develop```, then merge ```develop``` back to ```master``` (using a non-fast-forward merge). Tag the merge commit with the version number being released so we can keep track of where new versions land.

Once a version is tagged and released, the tag must never be removed. If there is a problem with the project requiring a re-deployment, create a new version and tag to reflect the change.
