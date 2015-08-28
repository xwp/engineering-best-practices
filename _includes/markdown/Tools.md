The quality of a contributor's work can be reliably tied the quality of their tools. Your editor (IDE), local dev environment, code linters, unit testers, compilers, shell scripts, and other tools can all have a profound impact on your efficiency and quality. So it's important to always be thinking about how your workflow can be improved. What are your pain points? What is tedious? What are things that you often miss or forget to do? As much as possible, when you identify these improvements look for ways to automate their solutions with scripts; if a script isn't possible, at least come up with checklists which you can follow: reduce your mental load.

There is perhaps no better way to improve our own respective workflows than to learn from each other by watching how each other works. So next time you're having a meetup, please do look over your colleague's shoulder to see what you can glean from them and suggest back to improve their workflow as well.

What follows are some of the tools we use. This list will grow and change over time and is not meant to be comprehensive. Generally, we encourage or require these tools to be used in favor of other ones.

### Editors (IDEs)

The [most popular](http://www.sitepoint.com/best-php-ide-2014-survey-results/) PHP editor (IDE) is [PhpStorm](https://www.jetbrains.com/phpstorm/). It also has extensive support for JavaScript, CSS, HTML, Twig, and (with a plugin) Bash. We highly recommend adopting PhpStorm as well for consistency across our team and so that we can collaborate on optimal configuration and maximizing the features.

Please refer to [WordPress Development using PhpStorm](https://confluence.jetbrains.com/display/PhpStorm/WordPress+Development+using+PhpStorm), and also see the [PhpStorm Docs & Demos](https://www.jetbrains.com/phpstorm/documentation/).

Also see the PhpStorm docs for integrating our [code checkers](#code-checkers):

* [PHP Code Sniffer](https://www.jetbrains.com/phpstorm/help/code-sniffer.html)
* [JSHint](https://www.jetbrains.com/phpstorm/help/jshint.html)
* [JSCS](https://www.jetbrains.com/phpstorm/help/jscs.html)
* [PHP Mess Detector](https://www.jetbrains.com/phpstorm/help/mess-detector.html) ([under investigation](https://github.com/xwp/wp-dev-lib/issues/4))

### Code Checkers

The following should all be integrated into [wp-dev-lib](https://github.com/xwp/wp-dev-lib) and pre-configured there for use in your plugin or site project; they should integrated into your IDE, be run automatically before committing via the [`pre-commit`](https://github.com/xwp/wp-dev-lib#pre-commit-hook) hook, and also run after pushing to a pull request via [Travis CI](https://github.com/xwp/wp-dev-lib#travis):

* **[PHP_CodeSniffer WordPress Coding Standards](https://github.com/WordPress-Coding-Standards/WordPress-Coding-Standards)** for [PHP coding standards](../php/#code-style).
* **[PHP MD](http://phpmd.org/) ([experimental](https://github.com/xwp/wp-dev-lib/issues/4))** for higher-level [PHP coding checks](../php/#code-style).
* **[PHPUnit](https://phpunit.de/)** for [PHP unit testing](../php/#unit-testing).
* **[JSHint](http://jshint.com/)** for basic [code style](../javascript/#code-style) reporting.
* **[JSCS](http://jscs.info/)** for comprehensive [code style](../javascript/#code-style) reporting and fixing.
* **[QUnit](https://qunitjs.com/)** for [JS unit testing](../javascript/#unit-and-integration-testing).

Any additional references to code checker tools elsewhere in these best practices should be amended to this list.

### Local Development Environments

When starting out, many developers edit PHP files on their local machine and then upload the changes via FTP to their production site to test. This is bad. A slightly better workflow is to have a separate staging server to upload changes to and test there. This is less bad, but still not good. Both of these are examples of cowboy coding.

Proper development workflow is always centered around version control. Code being deployed to a shared environment must always be first committed to version control or else risk being lost or overwritten. In order to test code without first having to commit it, it is essential for all developers to have a local development environment: a web server and database running locally.

There are a variety of solutions out there to get a local development environment up and running (e.g. MAMP and XAMPP), but we've found that [Vagrant](https://www.vagrantup.com/) is a far superior solution. We use Vagrant to build and interact with virtual environments that match production as closely as possible: it allows you to run a Linux server on your Windows or Mac, along with Nginx/Apache, PHP, MySQL, Memcached, etc. There are many different Vagrant setups and configurations available. The following setups are the only ones we support internally:

* [Varying Vagrant Vagrants](https://github.com/Varying-Vagrant-Vagrants/VVV) - Our standard Vagrant setup for client sites and local development.

* [VIP Quickstart](https://github.com/Automattic/vip-quickstart) - A Vagrant setup meant to closely match the WordPress.com VIP environment. If you are working on a VIP project, it is good to have this installed to effectively test your website locally. We only recommend this Vagrant setup for VIP projects.



### Task Runners

[Grunt](http://gruntjs.com/) - Grunt is a task runner built on Node that lets you automate tasks like Sass preprocessing and JS minification.

[Gulp](http://gulpjs.com/) - Another task runner also built on Node which is a newer alternative to Grunt. See [comparison](https://medium.com/@preslavrachev/gulp-vs-grunt-why-one-why-the-other-f5d3b398edc4).

### Package/Dependency Managers

[Bower](http://bower.io/) - A good tool to manage front end packages. Usually everything we need is bundled with WordPress. Sometimes we need something like "Chosen.js" that isn’t included. Bower is a good way to manage external libraries like that but is not necessary on most projects.

[Composer](https://getcomposer.org) - We use Composer for managing PHP dependencies. Usually everything we need is bundled with WordPress. Sometimes we need external libraries like "Patchwork". Composer is a great way to manage those external libraries but is not necessary on most projects

### Version Control

[Git](http://git-scm.com) - We use Git for version control. It is _critical_ that you are comfortable with Git: read [the book](https://git-scm.com/book/en/v2)! We encourage people to use the command line for interacting with Git. GUI’s are permitted but will not be supported internally. For more information on Git:

* [Hello World | GitHub Guides](https://guides.github.com/activities/hello-world/)
* [Understanding the GitHub Flow](https://guides.github.com/introduction/flow/)
* [Forking Projects | GitHub Guides](https://guides.github.com/activities/forking/)
* [A successful Git branching model](http://nvie.com/posts/a-successful-git-branching-model/)

[GitHub](https://github.com/) & [BitBucket](https://bitbucket.org/) - Our Git repos are primarily hosted in projects on GitHub, though some clients use BitBucket (or Stash). The pull request is the fundamental tool that these services provide which allows for code to be peer reviewed and for automated checks to be performed (via Travis CI). They also provide webhooks so that we can automate deployments to our preview environments and notify our Slack channels of activity.

[SVN](https://subversion.apache.org/) - We use SVN, but only in the context of WordPress.com VIP. Again, we encourage people to use the command line as we do not support GUI's internally.

### Command Line

The mouse is the enemy. The book [Pragmatic Programmer](https://pragprog.com/book/tpp/the-pragmatic-programmer) highlights the inefficiencies of interacting with a computer via a mouse. A workflow can be drastically sped up via keyboard commands. But think beyond “keyboard shortcuts” and the convoluted finger gymnastics they often involve (Ctrl-Alt-Command-Shift-A…). Think instead about the code you might write from your keyboard every day in PHP:

```php
wp_insert_post( array( 'post_title' => 'Hello World' ) );
```

These commands are articulated in computer language, here interpreted by PHP. It's not really optimized, however, for repetitive commands you write over and over and over again. Another language is, however: Bash. When you open your computer's Terminal (e.g. [iTerm2](http://iterm2.com/)) you are being dropped onto a Bash prompt: an interpreter for code written in the Bash programming language.

```bash
git commit -m "First commit"
```

When all of your instructions are composed of commands that are read by an interpreter like PHP or Bash, then you can turn your little commands into scripts to automate your repetitive tasks, such as setting up a whole brand new WordPress install, or syncing a database down from production and translating its hostnames.

For WordPress, there is a de facto tool for interfacing with WP from the command line: [WP-CLI](http://wp-cli.org). Becoming familiar with the commands available, and even writing your own commands, can greatly speed up your development workflow and allow you to script tasks for others to run without having to share a long complicated set of manual instructions. This is an extremely powerful tool that allows us to do imports, exports, run custom scripts, and more via the command line. Often this is the only way we can affect a large database (WordPress.com VIP or WPEngine). This tool is installed by default on VVV and VIP Quickstart.


