# Tools

## Philosophy

The quality of a contributor's work can be reliably tied to the quality of their tools. Your editor \(IDE\), local development environment, code linters, unit testers, compilers, shell scripts, and other tools can all have a profound impact on your efficiency and quality. So it's important to always be thinking about how your tools and workflow can be improved.

* What are your pain points?
* What is tedious?
* What are things that you often miss or forget to do?

As much as possible, when you identify improvements, try and look for ways to automate their solutions with scripts. If a script isn't possible, write a checklist which you can follow to reduce your cognitive load and the dreaded context switching we all love so much.

The following are tools we use at XWP. This list will grow and change over time and is not meant to be comprehensive. Generally, we encourage, and in some cases require, these tools to be used in favor of other ones. Rules governing tools to be used and packaged with a client site will be much stricter than those used on internal projects.

## Editors \(IDEs\)

Our recommended PHP editor \(IDE\) is [PhpStorm](https://www.jetbrains.com/phpstorm/), there is also another that is becoming more and more wide spread as of late, which is [VS Code](https://code.visualstudio.com/) and is also a great alternative. They both have extensive support for JavaScript, CSS, HTML, Markdown, Twig, and Bash to name a few. We highly recommend adopting PhpStorm for consistency across our team and so that we can collaborate on optimal configuration and maximizing the features.

Please refer to [WordPress Development using PhpStorm](https://www.jetbrains.com/help/phpstorm/using-wordpress-content-management-system.html), and also see the [PhpStorm Docs & Demos](https://www.jetbrains.com/phpstorm/documentation/).

Also see the PhpStorm docs for integrating our [code checkers](tools.md#code-checkers):

* [PHP Code Sniffer](https://www.jetbrains.com/help/phpstorm/php-quality-tools.html#php_quality_tools_code_sniffer)
* [ESLint](https://www.jetbrains.com/help/phpstorm/eslint.html)
* [JSCS](https://www.jetbrains.com/phpstorm/help/jscs.html)

## Code Checkers

The following should all be integrated into [`wp-dev-lib`](https://github.com/xwp/wp-dev-lib) and pre-configured there for use in your plugin or site project; they should integrated into your IDE, be run automatically before committing via the [`pre-commit`](https://github.com/xwp/wp-dev-lib#automate) hook, and also run after pushing to a pull request via [Travis CI](https://github.com/xwp/wp-dev-lib#travis-ci):

* [**PHP\_CodeSniffer WordPress Coding Standards**](https://github.com/WordPress-Coding-Standards/WordPress-Coding-Standards) for [PHP coding standards](languages/php.md#code-style).
* [**PHPUnit**](https://phpunit.de/) for [PHP unit testing](languages/php.md#unit-testing).
* \*\*\*\*[**ESLint**](https://eslint.org/) for basic [code style](languages/js/#code-style-and-documentation) reporting.
* [**JSCS**](http://jscs.info/) for comprehensive [code style](languages/js/#code-style-and-documentation) reporting and fixing.
* [**QUnit**](https://qunitjs.com/) for [JS unit testing](languages/js/#unit-and-integration-testing).

Any additional references to code checker tools elsewhere in these best practices should be amended to this list.

## Local Development Environments

When starting out, many developers edit PHP files on their local machine and then upload the changes via FTP to their production site to test. This is bad! A slightly better workflow is to have a separate staging server to upload changes to and test there. This is less bad, but still not good. Both of these are examples of cowboy coding.

Proper development workflow is always centered around version control. Code being deployed to a shared environment must always be first committed to version control or else risk being lost or overwritten. In order to test code without first having to commit it, it is essential for all developers to have a local development environment — a web server and database running locally.

There are a variety of solutions out there to get a local development environment up and running \(e.g. MAMP and XAMPP\), but we've found that [Docker](https://www.docker.com/) is a far superior solution. We typically use [Docker](https://www.docker.com/) paired with [Lando](https://lando.dev/) or [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/) to build and interact with virtual environments that match production as closely as possible. There are many setups and configurations available, but here are a few that we suggest based on the project requirements:

* **Docker + Vagrant**

  The [Docker](https://www.docker.com/) with [Vagrant](https://www.vagrantup.com/) and [VirtualBox](https://www.virtualbox.org/) local development environment [`wpsh-local`](https://github.com/wpsh/wpsh-local) can be embedded within a plugin using [Composer](https://getcomposer.org/). Making it perfect for both open source or client plugins that have no other dependencies besides WordPress. You can see how [`wpsh-local`](https://github.com/wpsh/wpsh-local) is implemented in our [`block-extend`](https://github.com/xwp/block-extend) scaffolding plugin.

* **Docker + Lando** This [WordPress development](https://github.com/GoogleChromeLabs/wordpressdev) environment, built with [Docker](https://www.docker.com/) and [Lando](https://docs.devwithlando.io/) from the Google team that is focused on WordPress development, allows for [core development](https://make.wordpress.org/core/), [plugin development](https://make.wordpress.org/plugins/), and [theme development](https://make.wordpress.org/themes/). It is intended to largely be a Docker-based port of VVV.
* **WordPress Core**  
  Recently WordPress Core built a [local development environment](https://make.wordpress.org/core/2019/08/05/wordpress-local-environment/) directly into the repository that uses [Docker](https://www.docker.com/) and [NPM](https://www.npmjs.com/). To have WordPress Core running at [http://localhost:8889](http://localhost:8889/) you can do the following commands:  


  ```text
  git clone https://github.com/WordPress/wordpress-develop.git
  cd wordpress-develop/
  npm install
  npm run build:dev
  npm run env:start
  npm run env:install
  ```

* **WordPress VIP Go** Our [VIP Go Site Boilerplate](https://github.com/xwp/vip-go-site) is a modern setup for [WordPress VIP Go](https://vip.wordpress.com/documentation/vip-go/) hosted projects:
  * 🏭 Uses Composer for adding project dependencies, including plugins and themes.
  * 🌎 Uses Composer autoloader for using any of the popular PHP packages anywhere in the codebase.
  * 👩‍💻 Provides a local development environment based on Docker that can be run inside Vagrant without having to install Docker on the host machine.
  * 🚀 Includes automated build and deploy pipelines to WordPress VIP Go.
* **VVV** [Varying Vagrant Vagrants](https://varyingvagrantvagrants.org/) \(VVV\) use to be our standard Vagrant setup for client sites and local development. However, over the past few years has become used much less due to its slow provisioning.

## Scaffolding

* [`wp-dev-lib`](https://github.com/xwp/wp-dev-lib)  This is a development library that is great for adding coding standards, linting and automated testing even to legacy projects since checks are applied only to new code by default.
* **Gutenberg Plugin** The [`block-extend`](https://github.com/xwp/block-extend) plugin has been developed as a starter for creating Gutenberg centric plugins and is also used during candidate coding challenges.
* **Customizer Plugin** Our template plugin for scaffolding WordPress plugins called [`wp-foo-bar`](https://github.com/xwp/wp-foo-bar) has historically been used to scaffold Customizer centric plugins for both our clients and Core. However, it has not been kept up-to-date this past year with all the new shinny development and editorial tools coming into play. It either needs to be updated or deprecated. It's likely that the [`block-extend`](https://github.com/xwp/block-extend) plugin will somehow be merged back into this scaffolding.

## Task Runners

[Grunt](http://gruntjs.com/) - Grunt is a task runner built on Node that lets you automate tasks like Sass preprocessing and JS minification. Grunt is our default task runner and has a great community of plugins and solutions we use for on company and client projects.

[Gulp](http://gulpjs.com/) - Gulp is also a task runner build on Node that offers a similar suite of plugins and solutions to Grunt. The biggest difference is Gulp allows you direct access to the [stream](https://nodejs.org/api/stream.html) of information from your source files and allows you to modify this data directly.

[Webpack](https://webpack.github.io/) - Webpack is a bundler for JS/CSS. It’s extremely useful when building larger JavaScript applications \(i.e. React.js\).

## Package/Dependency Managers

[Composer](https://getcomposer.org/) - We use Composer for managing PHP dependencies. Usually everything we need is bundled with WordPress, but sometimes we need external PHP libraries like “Patchwork”. Composer is a great way to manage those external libraries.

When a WordPress install is managed and maintained by an engineering team, and when the infrastructure supports it, plugins in a WordPress project can be easily managed using Composer. [WordPress Packagist](https://wpackagist.org/) provides a Composer repository that mirrors all public WordPress plugins and themes.

[NPM](https://www.npmjs.com/) - We use `npm` for managing JavaScript dependencies and is also great for generating scripts to accommodate our DevOps workflows. For example, we typically create scripts that lint, test, build, deploy, and pretty much handle everything we need to automate our processes.

## Version Control

[Git](https://git-scm.com/) - We use Git for version control. We encourage people to use the command line for interacting with Git. GUIs are permitted but will not be supported internally.

[SVN](https://subversion.apache.org/) - We use SVN, but only in the context of WordPress.com VIP. Again, we encourage people to use the command line as we do not support GUIs internally.

## Command Line Tools

The mouse is the enemy. The book [Pragmatic Programmer](https://pragprog.com/book/tpp/the-pragmatic-programmer) highlights the inefficiencies of interacting with a computer via a mouse. A workflow can be drastically sped up via keyboard commands. But think beyond “keyboard shortcuts” and the convoluted finger gymnastics they often involve \(Ctrl-Alt-Command-Shift-A…\). Think instead about the code you might write from your keyboard every day in PHP:

```text
wp_insert_post( array( 'post_title' => 'Hello World' ) );
```

These commands are articulated in computer language, here interpreted by PHP. However, this isn't really optimized for repetitive commands you write over and over and over again. A better language for repetitive tasks is Bash. When you open your computer's Terminal \(e.g. [iTerm2](http://iterm2.com/)\) you are being dropped onto a Bash prompt: an interpreter for code written in the Bash programming language.

```text
git commit -m "First commit"
```

When all of your instructions are composed of commands that are read by an interpreter like PHP or Bash, then you can turn your commands into scripts to automate your repetitive tasks, such as setting up a brand new WordPress install, or syncing a database down from production and translating its hostnames.

For WordPress, there is a de facto tool for interfacing with WordPress from the command line: [WP-CLI](http://wp-cli.org/). Becoming familiar with the commands available, and even writing your own commands, can greatly speed up your development workflow and allow you to script tasks for others to run without having to share a long complicated set of manual instructions. This is an extremely powerful tool that allows us to do imports, exports, run custom scripts, and more via the command line. Often this is the only way we can affect a large database \(WordPress.com VIP or WPEngine\). This tool should be installed by default in a development environment, if it's not create an issue on GitHub.

## Accessibility Testing

We use a variety of tools to test our sites for accessibility issues. WebAim has some great resources on [how to evaluate sites](http://webaim.org/articles/screenreader_testing/) with a screen reader.

* [Using VoiceOver](http://webaim.org/articles/voiceover/)
* [Using NVDA](http://webaim.org/articles/nvda/)
* [Using JAWS](http://webaim.org/articles/jaws/)

We’re also a fan of a few browser tools that lend us a hand when it comes to testing areas like color contrast, heading hierarchy, and ARIA application.

* [Headings Map for Chrome](https://chrome.google.com/webstore/detail/headingsmap/flbjommegcjonpdmenkdiocclhjacmbi?hl=es) or [Headings Map for Firefox](https://addons.mozilla.org/en-us/firefox/addon/headingsmap/) - A browser extension that allows you to see the heading structure of a webpage.
* [The Visual ARIA Bookmarklet](http://whatsock.com/training/matrices/visual-aria.htm) - A bookmarklet that can be run on a webpage and color codes ARIA roles.
* [WAVE](http://wave.webaim.org/) - A toolkit from WebAIM, that also has an extension for Chrome/Firefox. It evaluates a webpage and returns accessibility errors.
* [Accessibility Developer Tools](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb) - A Chrome extension that adds an “Audit” tab in Chrome’s developer tools that can scan a webpage for accessibility issues.
* [Tota11y](https://khan.github.io/tota11y/) - A visualization toolkit that can be used as a bookmarklet, and reveals accessibility errors on a webpage.
* [Contrast Ratio](https://leaverou.github.io/contrast-ratio/) - A color contrast tool to compare two colors against [levels of conformance](https://www.w3.org/TR/UNDERSTANDING-WCAG20/conformance.html) and see if they pass.
* [Tanagaru Contrast Finder](http://contrast-finder.tanaguru.com/?lang=en) - Another color contrast tool that tests colors against the levels of conformance, but also provides you with alternatives should your provided colors fail.

## Visual Regression Testing

We use visual regression testing to ensure code changes don’t have unforeseen repercussions. This provides a helpful visual aid to check against CSS changes, plugin updates, and third-party script updates.

* [BackstopJS](https://github.com/garris/BackstopJS) - A tool used to run visual regression tests that compares known reference states against updates.

## VPN

Sometimes access to client and vendor resources is restricted to specific IP addresses so we use self-hosted [Outline VPN servers](https://getoutline.org/) to provide teams with fixed IP addresses in several geographical locations around the world. Please reach out to Kaspars or Derek in the `#technology` Slack channel to request access.

After installing and configuring [the Outline VPN client](https://getoutline.org/) and connecting to one of the servers, use a service like [ifconfig.io](https://ifconfig.io/) to confirm the IP address of the VPN endpoint. This IP address can be provided to the client or vendor for inclusion in the appropriate allow-list.

Note that all network traffic from your device to the public internet is routed through the VPN so please keep it connected only when necessary. 

## Secure Messaging

For exchanging sensitive information between teams, clients and vendors we suggest using [Keybase](https://keybase.io) chat. Alternatively, if your Keybase account is associated with a PGP key, the message can be encrypt with your public key via [this form](https://keybase.io/encrypt) and sent using any traditional channel such as email or Slack. Use `https://keybase.io/encrypt#kaspars` where `kaspars` is your Keybase username to prefill the form with the correct recipient.

