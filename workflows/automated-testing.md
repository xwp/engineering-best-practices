# Automated Testing

### Philosophy

The number one line of defense for good quality code, that adheres to best practices and coding standards, is _automated testing_. Because your code must adhere to the coding standards for [PHP](../languages/php.md#code-style), [JavaScript](../languages/js/#code-style-and-documentation), [HTML](../languages/markup.md#following-wordpress-standards), and [CSS](../languages/css.md#follow-wordpress-standards) and pass a [peer code review](code-review.md) in our projects, it is very important that you learn how to setup and perform automated testing. Automated testing is used to to catch small but potentially critical issues, so don't write code without it.

### wp-dev-lib

Our repos should always include the [`wp-dev-lib`](https://github.com/xwp/wp-dev-lib) which includes a [`pre-commit`](https://github.com/xwp/wp-dev-lib/#automate) hook that runs before a commit is made, blocking a commit when it encounters issues.

**Note**: this can be bypassed using `--no-verify`.

The `pre-commit` hook is not installed automatically when a Git repo is cloned. You can find more information in the [automate](https://github.com/xwp/wp-dev-lib/#automate) section of the `wp-dev-lib` repository on how to install the the `pre-commit` hook. As well, you can configure your [IDE integration for code checkers](../tools.md#editors-ides). 

Travis CI should also be configured to automatically run the PHPUnit tests and static analysis for the code in the pull request so GitHub can indicate whether the tests pass. Ask your friendly GitHub admin to setup the Travis CI GitHub App for you and then follow the directions found in the `wp-dev-lib` repository to configure it. `wp-dev-lib` can be installed with either Composer or NPM, which makes it simple to use in any project.

### **Unit and Integration Testing**

Unit testing is the automated testing of units of source code against certain assertions. The goal of unit testing is to write test cases with assertions that test if a unit of code is truly working as intended. If an assertion fails, a potential issue is exposed, and code needs to be revised.

For specifics on how to write tests, see [PHP unit testing](../languages/php.md#unit-testing) and [JavaScript unit testing](../languages/js/#unit-and-integration-testing).

By definition, unit tests do not have dependencies on outside systems; in other words, only your code \(a single unit of code\) is being tested. Integration testing works similarly to unit tests but assumptions are tested against systems of code, moving parts, or an entire application. The phrases unit testing and integration testing are often misused to reference one another especially in the context of WordPress, since the tests in WordPress itself are a mix of unit and integration tests.

Unit tests should be written for all plugins, whether they are for public distribution or for private client projects. As noted in [modular code](../structure.md#modular-code), themes should be devoid of functional logic and so they are not applicable for unit tests.

Write your \(plugin\) code in a way that makes it testable. It is much easier to write unit tests while \(or even before, à la [TDD](https://en.wikipedia.org/wiki/Test-driven_development)\) you write your functional code. Unit and integration tests help guide best practices for code architecture — testable code is more often well-architected code.

* Organize your plugin code into objects that incorporate the [dependency injection pattern](http://jasonpolites.github.io/tao-of-testing/ch3-1.1.html); this allows unit tests to supply mocks for the objects that aren't specifically being tested.
* Write methods that are as small as possible, that do one thing and do it well.
* Shy away from interacting with global variables, though in WordPress this is often a painful reality we have to just live with.
* A function should be a black box that takes an input and returns an output. You can then test the outputs for any given inputs.

It is always a challenge to ensure that unit tests have complete testing coverage for a plugin. It is often not worthwhile to require 100% coverage, but rather to focus on testing the key parts of the plugin's logic \(as opposed to testing basic things like metabox registration\). We should still strive to achieve &gt;= 90% to get the Coveralls.io badge to turn green — makes most of us feel warm and fuzzy inside.

When the issue being worked on is a defect, a best practice is to first write a failing unit test that demonstrates the bug, and then fix the code so that the unit test passes. If you are working on a legacy project that doesn't have any tests ask the technical lead to prioritize adding them so you can follow this best practice. If you are the technical lead, add a test suite already, `wp-dev-lib` makes this very easy!

