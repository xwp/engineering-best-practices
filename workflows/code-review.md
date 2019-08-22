# Code Review

### Philosophy

Nobody is perfect. Even the most experienced engineers can forget some important details. We try to mitigate this as much as possible with [automated testing](automated-testing.md), but code checkers typically only catch low-hanging fruit. A fellow engineer is really needed to do a review of the changes before it gets sent along for [QA](quality-assurance.md) or for the client to review. With a fresh pair of eyes, a peer code reviewer can easily spot things that don't make sense or implementations that don't satisfy the acceptance criteria as stated in the issue being addressed. So for these reasons all code must go through a peer review process.

### Reviewing your Peers

The code reviewer shouldn't necessarily be testing the code in depth and making sure it satisfies all of the requirements when a [testing \(QA\)](https://xwp.github.io/engineering-best-practices/workflows/#qa) team is involved in a project — this is their responsibility. In any case, the key thing is that code should be reviewed by a colleague at least once before it gets sent along for client review \(or WordPress.com VIP commit\), and especially before it goes to production.

#### What to look for

Code review should especially identify _security_ and _performance_ issues \(as would get raised by WordPress VIP\) that may not be apparent through testing at QA level or not easy for a tester to articulate. For WordPress.com, identifying technical issues that violate VIP's [requirements](https://lobby.vip.wordpress.com/wordpress-com-documentation/code-review-what-we-look-for/) will speed up the deployment process since Zendesk tickets will be less-frequently opened for needed fixes; this makes our friends at VIP happy and it makes clients happy since commits will be deployed sooner and even in line with a timetable they have.

Code review is also an opportunity for ensuring that another engineers on the project team have familiarity with the code being contributed, thus increasing the [bus factor](https://en.wikipedia.org/wiki/Bus_factor) and improving our ability and availability to maintain the codebase.

Code reviewers should make sure that [unit tests](https://xwp.github.io/engineering-best-practices/workflows/#unit-and-integration-testing) are included, and that they cover the key changes introduced in the branch. The `pre-commit` hook and Travis CI build will have already checked the coding conventions and whether any included unit tests are failing.

#### Review lifecycle

All code review should be done in the context of a _pull request_ \(PR\). Feedback on the changes should be provided as comments on the pull request, especially as inline comments for the specific lines that need to be addressed. When feedback is added, the PR should be assigned back to the engineer to fix. In some cases the code reviewer could fix minor issues themselves. However, it is the responsibility of the original engineer to only assign a pull request for code review once they are confident that it is in a state where it can be merged. The engineer should not rely on the code reviewer to harden and fix up issues that they know are present in the code. Also, if Travis CI reports failures on the pull request, it cannot be assigned for code review.

Being a distributed team working across many timezones we have the advantage of being able to work on a feature in one timezone and then pass it along for code review in another timezone. When project teams are put together in such a way it allows for timezone collaboration it is a strength as we can have work happening around-the-clock speeding up our turnaround time. It can also be a disadvantage when code is not ready for merging when the code reviewer picks up the work at the start of their day — the code reviewer would have no choice but to assign the PR back for fixes, resulting in a drawn-out 24-hour feedback loop.

For minor issues, the code reviewer should therefore leave a comment and fix them during the review to limit the feedback loop. The original engineer should be sure to take note of the comment and review the changes that the code reviewer made so that they can refine their skills and apply them for the next time.

This does not mean that pull requests should not be opened before they are in a mergeable state. Not at all. It is very good to **open your PRs early**, but leave them unassigned and marked as `[WIP]` \(work in progress\): this WIP PR provides a way for the engineer to solicit feedback during development to make sure they are going down the right track.

