The following are the tools we use. This list will grow and change over time and is not meant to be comprehensive. Generally, we encourage or require these tools to be used in favor of other ones. Rules governing tools to be used and packaged with a client site will be much stricter than those used on internal projects.

<h3 id="editors">Editors (IDEs)</h3>

The [most popular](http://www.sitepoint.com/best-php-ide-2014-survey-results/) PHP editor (IDE) is [PhpStorm](https://www.jetbrains.com/phpstorm/). It also has extensive support for JavaScript, CSS, HTML, Twig, and (with a plugin) Bash. We highly recommend adopting PhpStorm as well for consistency across our team and so that we can collaborate on optimal configuration and maximizing the features.

<h3 id="local-development">Local Development Environments</h3>

We use [Vagrant](https://www.vagrantup.com/) to build and interact with virtual environments that match production as closely as possible. There are many different Vagrant setups and configurations available. The following setups are the only ones we support internally.

[Varying Vagrant Vagrants](https://github.com/Varying-Vagrant-Vagrants/VVV) - Our standard Vagrant setup for client sites and local development.

[VIP Quickstart](https://github.com/Automattic/vip-quickstart) - A Vagrant setup meant to closely match the WordPress.com VIP environment. If you are working on a VIP project, it is good to have this installed to effectively test your website locally. We only recommend this Vagrant setup for VIP projects.

<h3 id="task-runners">Task Runners {% include Util/top %}</h3>

[Grunt](http://gruntjs.com/) - Grunt is a task runner built on Node that lets you automate tasks like Sass preprocessing and JS minification.

[Gulp](http://gulpjs.com/) - Another task runner also built on Node which is a newer alternative to Grunt. See [comparison](https://medium.com/@preslavrachev/gulp-vs-grunt-why-one-why-the-other-f5d3b398edc4).

<h3 id="package-managers">Package/Dependency Managers</h3>

[Bower](http://bower.io/) - A good tool to manage front end packages. Usually everything we need is bundled with WordPress. Sometimes we need something like "Chosen.js" that isn’t included. Bower is a good way to manage external libraries like that but is not necessary on most projects.

[Composer](https://getcomposer.org) - We use Composer for managing PHP dependencies. Usually everything we need is bundled with WordPress. Sometimes we need external libraries like "Patchwork". Composer is a great way to manage those external libraries but is not necessary on most projects

<h3 id="version-control">Version Control {% include Util/top %}</h3>

[Git](http://git-scm.com) - We use Git for version control. We encourage people to use the command line for interacting with Git. GUI’s are permitted but will not be supported internally.

[SVN](https://subversion.apache.org/) - We use SVN, but only in the context of WordPress.com VIP. Again, we encourage people to use the command line as we do not support GUI's internally.

<h3 id="command-line">Command Line Tools</h3>

[WP-CLI](http://wp-cli.org) - A command line interface for WordPress. This is an extremely powerful tool that allows us to do imports, exports, run custom scripts, and more via the command line. Often this is the only way we can affect a large database (WordPress.com VIP or WPEngine). This tool is installed by default on VVV and VIP Quickstart.


