
---

description: Best practices for using local development environment.

---

# WP-ENV


[wp-env](https://developer.wordpress.org/block-editor/reference-guides/packages/packages-env/) provides easy mechanism for spinning WordPress instance on your local machine. It allows developing and testing plugins and themes, and it is easy to use.

## Requiremens:

- [Docker](https://www.docker.com)
- [Node.js](https://nodejs.org)

## Instalation:

To be able to use `wp-env` we need to install it.

We can install it globally by running:

	npm -g i @wordpress/env

or locally by running:

	npm i @wordpress/env --save-dev


## Usage

After ensuring that our Docker is running, several commands could be used for `wp-env` manipulation as follows:

- `wp-env start` - starts it
- `wp-env stop` - stops it
- `wp-env clean all` - resets the database (permanently deleting posts, pages, media in the local WP installation)
- `wp-env destroy` - forcibly remove all of the underlying Docker container and volumes. This is a good option for starting from the scratch.

Once the console reports that wp-env is successfully running, we will see the url to access it (by default it is http://localhost:8888). Default login credentials are username: **admin**, password: **admin**.

## Configuration

We can add it to our package.json scripts to:

	"scripts": {
		"env":"wp-env"
	}

To be able to customise `wp-env` we can use `.wp-env.json` in the directory where we run the `wp-env` from.
Bellow is an example of .wp-env.json file that defines:

- php version 7.4 is used,
- the WP files will be stored into /tools/wordpress folder
- defines mapping of the plugins
- defines themes from current folder to be used.
```
{
 	"phpVersion": "7.4",
  	"core": "./tools/wordpress",
  	"plugins": [],
  	"mappings": {
  		"wp-content/plugins": "../../plugins"
  	},
  	"themes": [
  		"."
  	]
}
```
# Docker images

We can have indepth controll of the environment we use, to be able to replicate deployment environment on our local host. In this tutorial, we will use repository for [Pantheon site template](https://github.com/xwp/pantheon-site).

## Requiremens:

- [Docker](https://www.docker.com)
- [Node.js](https://nodejs.org)
- [Composer](https://getcomposer.org)
- [mkcert](https://github.com/FiloSottile/mkcert)  for simple local TLS setup.

Use a package manager like  [Homebrew](https://brew.sh/)  to install project dependencies:
```
brew install git docker php@7.4 composer node@14 mkcert
```
The WordPress core installer package  `johnpbloch/wordpress-core-installer`  isn't compatible with Composer Plugin API version 2 so we require Composer version 1 which you can install by running  `composer self-update --1`.

## Configuration

The first thing to define is [docker-compose.yml](https://github.com/compose-spec/compose-spec/blob/master/spec.md) file. We created one example of such file https://github.com/xwp/pantheon-site/blob/main/docker-compose.yml that contains multiple services that provide us with the development environment. Those services include:
- [NGINX](https://www.nginx.com/) - web server that can also be used as a reverse proxy, load balancer, mail proxy and HTTP cache
- [dnsmasq](https://thekelleys.org.uk/dnsmasq/doc.html) - Create a local DNS server that maps the development URL to this environment.
- [MKCert](https://github.com/FiloSottile/mkcert) - simple tool for making locally-trusted development certificates
- [MySQL](https://www.mysql.com/) - MySQL is an open-source relational database management system.
- [MailHog](https://github.com/mailhog/MailHog) - an email testing tool that makes it super easy to install and configure a local email server
- [Redis](https://redis.io/) - an open source (BSD licensed), in-memory data structure store, used as a database, cache, and message broker
- [WordPress](https://hub.docker.com/_/wordpress)

Docker can build images automatically by reading the instructions from a `Dockerfile`. Example of such files could be find in https://github.com/xwp/pantheon-site/tree/main/tools/docker.

## Running the environment

In this project, we install WordPress inside `/web/` folder. That is configured inside `composer.json` and `wp-cli.yml` file.

To run the environment the user first needs to run:

 - `npm install` - which installs all the scripts. This will trigger post-install `composer install` and install WordPress inside `/web/` directory.

After this is done, several other commands could be used:

- `npm run start` - starts all Docker images
- `npm run stop` - stops containers and removes containers, networks, volumes, and images created by `up`
- `npm run stop-all` - retrieve all running container IDs and pass those to docker stop
- `npm run clean` - delete temporary folders

**Note:** Our themes and plugins should be stored inside `wp-content` folder, which will be linked with dev folder `/web/` after initialisaion. None of the file changes should be done inside `/web/` folder as it is temporary folder and will be recreated every time you run `npm run clean` and `npm install`.

## Accessing local environent

With specific domains (instead of `localhost`) we get reliable autocomplete, browser history and links to project locations. On the other hand`localhost` doesn’t support subdomains so we can’t reliably test a multisite instance, for example.

To allow using valid ssl certificates and running multisite environment on our localhost, we use inhouse domain `wpenn.net` which points to our localhost. This allows us to use any subdomain in our project (`*.wpenv.net`), which will be defined in `DEV_HOSTNAME` environment variable inside [.env.example](https://github.com/xwp/pantheon-site/blob/main/.env.example) file.

Once the development environment is started with `npm run start`, and all of the images are running, we will be able to access it via `xwp.wpenv.net` (or any other domain if defined otherwise), and it will be accessable via https protocol.


