# Quality Assurance

### Philosophy

Our approach is very much a team effort. Everyone on the delivery team is in some way responsible for doing at least a little QA. Writing user stories & acceptance criteria along with adding automated processes of continuous integration and delivery or deployment, plus mandatory peer code reviews all contribute to lessening the burden on QA to identify defects. QA should be the last line of defense and focused more on the user experience and to validate the intent of an issue was fulfilled, while also ensuring bugs don't make it to production.

### Who does what

Since engineers will have a [local development environment](../tools.md#local-development), code reviewers will be able to QA the changes on their machines with confidence that it will work properly if pushed directly to production — with the caveat that content from the production database probably wouldn't be used in testing.

As noted in [code review](code-review.md), when there is a dedicated QA team or person involved in a project, the code reviewer should not do in-depth validation that the changes satisfy the requirements since this is the QA Engineers responsibility. QA should also be equipped to do all the cross-browser and accessibility testing.

### Preview

Since the QA team most likely doesn't have a local development environment, and neither should they be expected to, it is critical for there to be an external staging environment available where code changes can be deployed for review before the code gets pushed to production.

For projects hosting on **WordPress.com VIP**, the [Quickstart environment](https://vip.wordpress.com/documentation/quickstart/) they provide for local development \(via Vagrant\) is also designed to be provisioned on AWS EC2 instance, and so this is what we have historically used. We would install a given site's theme into this Quickstart environment by cloning it from GitHub. There are other ways to handle setting up a preview environment and when one is needed someone from the Technology team will assist you in getting it set up.

Now, the `master` branch for these site repos corresponds to what is currently in SVN, which makes this branch not suitable for our preview environment. So we have standardized on each theme repo having a `preview` branch which is what is checked out on the preview server. When new commits appear in the `preview` branch, a GitHub webhook kicks in and pings the server to checkout the latest via `git fetch && git checkout origin/preview`.

**Note**: that new commits should not be made directly to the `preview` branch.

Commits should only ever be made to feature branches off of `master`. When a feature branch is ready for QA, it can be merged into `preview` so that it will appear on the preview environment. The pull request for the feature branch into `master` will remain open until it passes QA.

**Protip**: Don't merge feature branches into `preview` by means of pull requests, since this just adds GitHub notification noise. Instead, do the merges directly from the command line, even [use a script to make it easy](https://gist.github.com/westonruter/dd264e10b1e2c2388a6a).

{% embed url="https://gist.github.com/westonruter/dd264e10b1e2c2388a6a" %}

The `preview` branch is a dead-end branch, it should never get merged back into `master` or any other branch. It's purpose is to review changes to `feature/bugfix` branches before they get merged into `master`. The `preview` branch should always have a superset of the commits that are in `master`. Periodically the `preview` branch will need to get cleaned up and reset to `master` \(via `git checkout preview && git reset --hard master && git push -f`\). When this is done, make sure that any open pull request branches get re-merged into `preview`.

For sites hosted on **Pantheon**, this is part of the [their platform](https://pantheon.io/docs/pantheon-workflow): all sites have a dev environment, test \(preview/staging\) environment, and live \(production\) environment. We still house our Pantheon site projects on GitHub, but [configure Travis CI](https://github.com/xwp/pantheon-wordpress-upstream/blob/master/.travis.yml) to automatically push code from GitHub to the Pantheon dev environment after Travis CI passes all of the [code checks](../tools.md#code-checkers) \(in `after_success`\). We can also set up Travis CI to automatically pick up pushes to the `preview`branch and push them to a Pantheon `preview` [multidev environment](https://pantheon.io/docs/multidev), which will allow us to keep features from being merged into `master` before they have passed QA.

