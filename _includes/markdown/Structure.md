### Modular Code

Every project-whether a plugin, a theme, or a standalone library—should be coded to be reusable and modular. The line between a theme and a plugin is often fuzzy in the WordPress community, but there should be a clear distinction between the two.

#### Themes

Themes should only handle presentation: templates, layouts, styles, and configuration (registering sidebars, menu locations, theme support, etc).

Any site-specific configuration that isn't directly related to a theme should go into a site-specific plugin (e.g. a `mu-plugin`). This doesn't apply to sites on WordPress.com VIP, plugins are usually bundled with the theme and `mu-plugin`s aren't supported.

Any theme dependencies on functionality plugins should be built with the use of `do_action()` or `apply_filters()`. Changing to the default theme should not trigger errors on a site; the theme can function when the functionality plugin is disabled, broken, or missing. Nor should disabling a functionality plugin - every piece of code should be decoupled and use standard WordPress paradigms (hooks) for interacting with one another.

Any logic in the theme's `functions.php` should be simple and directly related to presentation; any other logic should be put into a plugin (e.g. post types, taxonomies, rewrite rules).

Take a look at our [Config-Driven WP](https://github.com/xwp/config-driven-wp) project for some (dated) examples for a common approach we take for normalizing the theme's configuration into a [`config.php`](https://github.com/xwp/config-driven-wp/blob/master/docroot/wp-content/themes/radio/config.php) array. The config array can then be cleanly merged with a child theme's configuration array.

#### Plugins

A site's custom functionality should be provided by plugins. Plugins should be as generic and reusable as possible. Plugins should do one thing and do them well.

Each plugin should operate within its own namespace, both in terms of code isolation and in terms of internationalization.

Any site-specific configuration for a plugin should be added to a special `mu-plugin` that is specifically designated for this purpose.

Plugins should be standalone yet extensible. Plugins should provide extension hooks that allow other plugins to build upon their functionality. As such, plugins can have dependencies, although they should not cause an error if activated without the dependent plugin being active.

Plugins should have unit tests.

Any functions the plugin exposes for use in a theme should be done so through actions and filters - the plugin should contain multiple calls to `add_filter()` and `add_action()` as the hooks themselves will be defined in the theme.

Use our [`wp-foo-bar`](https://github.com/xwp/wp-foo-bar) plugin scaffold for initializing new plugins.

#### General Notes

Each theme/plugin/site repo should include a copy of the [wp-dev-lib](https://github.com/xwp/wp-dev-lib) submodule, along with the config files for PHPCS, JSHint, JSCS, Travis CI, and other tools. Follow the [readme](https://github.com/xwp/wp-dev-lib#readme) for installation and configuration instructions.

Every project, whether it includes Composer-managed dependencies or not, can contain a `composer.json` file defining the project so it can in turn be pulled in to other projects via Composer.  For example:

```json
{
	"name": "xwp/{project name}",
	"type": "wordpress-plugin",
	"minimum-stability": "dev",
	"require-dev": {},
	"require": {},
	"version": "1.0.1",
	"dist": {
		"url": "... stable archive package URI ...",
		"type": "zip"
	}
}
```

When code is being reused between projects, it should be abstracted into a standalone library that those projects can pull in through Composer. Generally, code is client- or project-specific, but if it's abstract enough to be reused we want to capture that and maintain the code in one place rather than copy-pasting it between repositories.

### Dependencies

Projects generally use three different classes of dependency management:

- [npm](http://npmjs.org) is used to manage build dependencies like Grunt and its related plugins
- [Composer](http://getcomposer.org) is used primarily for back-end (i.e. admin or PHP-based) dependencies
- [Bower](http://bower.io) is used to manage front-end (i.e. script and style framework) dependencies

Generally, dependencies pulled in via a manager are _not_ committed to the repository, just the `.json` file defining the dependencies. This allows all developers involved to pull down local copies of each library as needed, and keeps the repository fairly clean.

With some projects, using an automated dependency manager won't make sense. In server environments like VIP, running dependency software on the server is impossible. If required repositories are private (i.e. invisible to the clients' in-house developers), expecting the entire team to use a dependency manager is unreasonable. In these cases, the dependency, its version, and the reason for its inclusion in the project outside of a dependency manager should be documented.

#### Versioning

(*XWP Note:* We actually usually do version-control our external libraries, as this makes it easier to keep track of the external dependencies during testing and to manage the production deployments. )

The key philosophy that drives our repository structure is *we don't version control things that are version controlled elsewhere*. This means we don't version control WordPress core files or third party plugins. If we are building a theme, we version control only the theme and deploy to the themes directory. Similarly, if we are building a plugin, we version control only the plugin and deploy to the plugins directory. Rather than version control third party libraries, we use [package managers](/Engineering-Best-Practices/tools/#package-managers) to include those dependencies. There are of course exceptions to this.

The counter argument to this philosophy is "what if the latest version of ______ breaks the site? How will we revert to a working state if we don't version control WordPress core and plugins?". WordPress core is backwards compatible, and we believe in trusting it the same way we trust PHP or MySQL. Similarly, we only install and recommend plugins that we trust. These best practices coupled with our talented engineers gives us confidence that our code will work with core and plugin updates. We still test major updates to plugins and core on staging first. If we discover code in core or a plugin that has issues, we try our best to correct that code and push the changes upstream giving back to the open source community.

### File Organization

Project structure unity across projects improves engineering efficiency and maintainability. We believe the following structure is segmented enough to keep projects organized—and thus maintainable—but also flexible and open ended enough to enable engineers to comfortably modify as necessary. All projects should derive from this structure:

```
|- bin/ __________________________________ # WP-CLI and other scripts
|- node_modules/ _________________________ # npm/Grunt modules
|- bower_components/ _____________________ # Frontend dependencies
|- vendor/ _______________________________ # Composer dependencies
|- assets/
|  |- images/ ____________________________ # Theme images
|  |- fonts/ _____________________________ # Custom/hosted fonts
|  |- js/
|    |- src/ _____________________________ # Source JavaScript
|    |- project.js _______________________ # Concatenated JavaScript
|    |- project.min.js ___________________ # Minified JavaScript
|  |- css/
|    |- scss/ ____________________________ # See below for details
|    |- project.css
|    |- project.min.css
|    |- project-admin.css
|    |- project-admin.min.css
|    |- editor-style.css
|- includes/ _____________________________ # PHP classes and files
|- templates/ ____________________________ # Page templates
|- partials/ _____________________________ # Template parts
|- languages/ ____________________________ # Translations
|- tests/
|  |- php/ _______________________________ # PHP testing suite
|  |- js/ ________________________________ # JavaScript testing suite
```

The `scss` folder is described separately, below to improve readability:

```
|- assets/css/scss/
|  |- global/ ____________________________ # Functions, mixins, placeholders, and variables
|  |- base/
|    |- reset, normalize, or sanitize
|    |- typography
|    |- icons
|    |- wordpress ________________________ # Partial for WordPress default classes
|  |- components/
|    |- buttons
|    |- callouts
|    |- toggles
|    |- all other modular reusable UI components
|  |- layout/
|    |- header
|    |- footer
|    |- sidebar
|  |- templates/
|    |- home page
|    |- single
|    |- archives
|    |- blog
|    |- all page, post, and custom post type specific styles
|  |- admin/ _____________________________ # Admin specific partials
|  |- editor/ ____________________________ # Editor specific partials (leverage placeholders to use in front-end and admin area)
|  |- admin.scss
|  |- project.scss
|  |- editor-styles.scss
```

### Third-Party Integrations

Any and all third-party integrations need to be documented in an `INTEGRATIONS.md` file at the root of the project repository. This file includes a list of third-party services, which components of the project those services power, how the project interacts with the remote APIs, and when the interaction is triggered.

For example:

```
## CrunchBase
Remote service for fetching funding and other investment data related to tech startups.

### Scheduling
- The CrunchBase API is used via JS in a dynamic product/company search on post edit pages
- Funding data is pulled every 6 hours by WP Cron to update cached data

### Integration Points
- /assets/js/src/crunchbase-autocomplete.js
- /includes/classes/crunchbase.php
- /includes/classes/cron.php

### Development API
- See http://somesitethatrequireslogin.com/credentials-for-project
```

#### API Keys and Credentials

Authentication credentials and API keys should _never_ be hard-coded into a project. Hard-coding production credentials leads to embarrassing eventualities like posting development content to Twitter or emailing such content to clients' mail lists.

Where possible, the project should expose a UI for entering and managing third party credentials.

If a management UI is impossible due to the nature of the project, credentials should be loaded via either PHP constants or WordPress filters. These options can - and should - default to developer credentials in the absence of production data.

```php
<?php
// Production API keys should ideally be defined in wp-config.php
// This section should default to a development or noop key instead.
if ( ! defined( 'CLIENT_MANDRILL_API_KEY' ) && ! ENV_DEVELOPMENT ) {
	define( 'CLIENT_MANDRILL_API_KEY', '1234567890' );
}
```

The `ENV_DEVELOPMENT` constant should always be set to `true` for local development and should be used whenever and wherever possible to prevent production-only functionality from triggering in a local environment.

The location where other engineers can retrieve developer API keys (i.e. Basecamp thread) can and should be logged in the `INTEGRATIONS.md` file to aid in local testing. Production API keys must _never_ be stored in the repository, neither in text files or hard-coded into the project itself.
