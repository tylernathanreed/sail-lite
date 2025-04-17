# Sail Lite

## Table of Contents

- [Introduction](#introduction)
- [Sail Lite vs Docker Compose](#sail-lite-vs-docker-compose)
- [Sail Lite vs Laravel Sail](#sail-lite-vs-laravel-sail)
- [Installation and Setup](#installation-and-setup)
  - [Installing into Existing PHP Packages](#installing-into-existing-php-packages)
  - [Configuring a Shell Alias](#configuring-a-shell-alias)
  - [Rebuilding Images](#rebuilding-images)
- [Starting and Stopping](#starting-and-stopping)
- [Executing Commands](#executing-commands)
- [PHP Versions](#php-versions)
- [Customization](#customization)

## Introduction
<a name="introduction"></a>

Sail Lite is a light-weight command-line interface for interacting a baseline [Docker](https://www.docker.com/) environment for package development.
Sail Lite was inspired by [Laravel Sail](https://github.com/laravel/sail), which offers a richer experience for Laravel development.
Sail Lite is intended to be a lighter-weight alternative to Laravel Sail, targeting package development, and isn't specific to Laravel development.

At its heart, Sail Lite is the `docker-compose.yml` file and the `sail` script that is stored at the root of your project. The `sail` script provides a CLI with convenient methods for interacting with the Docker containers defined by the `docker-compose.yml` file.

Sail Lite is supported on macOS, Linux, and Windows (via [WSL2](https://docs.microsoft.com/en-us/windows/wsl/about)).

## Sail Lite vs Docker Compose
<a name="sail-lite-vs-docker-compose"></a>

Under the hood, Sail Lite is just [Docker](https://www.docker.com/).
However, Sail Lite is pre-configured for PHP package development, and offers a `sail` binary with commands that are easier to work with.

| Sail Lite    | Docker Compose                         |
| ------------ | -------------------------------------- |
| `sail up`    | `docker compose up`                    |
| `sail down`  | `docker compose down`                  |
| `sail build` | `docker compose build`                 |
| `sail bash`  | `docker compose exec -u sail dev bash` |

## Sail Lite vs Laravel Sail
<a name="sail-lite-vs-laravel-sail"></a>

Sail Lite is intended for PHP package development, where as Laravel Sail targets the general Laravel developer experience.

If you intend to build Laravel applications, you should use Laravel Sail.

Sail Lite installs far fewer container dependencies than Laravel Sail (35 and counting), in that there's no database, cache, or node.js support.

Laravel Sail also requires several Illuminate and Symfony libraries, whereas Sail Lite has no package dependencies.

| Sail Lite                                      | Laravel Sail                                         |
| ---------------------------------------------- | ---------------------------------------------------- |
| ![Sail Lite Vendor](/img/sail-lite-vendor.png) | ![Laravel Sail Vendor](/img/laravel-sail-vendor.png) |

## Installation and Setup
<a name="installation-and-setup"></a>

### Installing into Existing PHP Packages
<a name="installing-into-existing-php-packages"></a>

If you are interested in using Sail Lite with an existing PHP package, you may simply install Sail Lite using the Composer package manager. Of course, these steps assume that your existing local development environment allows you to install Composer dependencies:

```bash
composer require reedware/sail-lite --dev
```

After Sail Lite has been installed, you may run the install command. This command will publish the `docker-compose.yml` file to the root of your application.

```bash
./vendor/bin/sail install
```

Finally, you may start Sail Lite.

### Configuring a Shell Alias
<a name="configuring-a-shell-alias"></a>

By default, Sail Lite commands are invoked using the `vendor/bin/sail` script:

```bash
./vendor/bin/sail up -d
```

However, instead of repeatedly typing `vendor/bin/sail` to execute Sail Lite commands, you may wish to configure a shell alias that allows you to execute Sail's commands more easily:

```bash
alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'
```

To make sure this is always available, you may add this to your shell configuration file in your home directory, such as `~/.zshrc` or `~/.bashrc`, and then restart your shell.

Once the shell alias has been configured, you may execute Sail Lite commands by simply typing `sail`. The remainder of this documentation's examples will assume that you have configured this alias:

```bash
sail up -d
```

### Rebuilding Images
<a name="rebuilding-images"></a>

Sometimes you may want to completely rebuild your Sail Lite images to ensure all of the image's packages and software are up to date. You may accomplish this using the `build` command:

```bash
sail down

sail build --no-cache

sail up
```

## Starting and Stopping
<a name="starting-and-stopping"></a>

Sail Lite's `docker-compose.yml` file defines a single development container to help you build PHP packages.

To start the development container, you should execute the up command:

```bash
sail up
```

To start the development container in the background, you may start Sail Lite in "detached" mode:

```bash
sail up -d
```

To stop the development container, you may simply press Control + C to stop the container's execution. Or, if the container is running in the background, you may use the stop command:

```bash
sail stop
```

## Executing Commands
<a name="executing-commands"></a>

When using Site Lite, you package is executing within a Docker container, and is isolated from your local computer.
You may use the `shell` command to connect to your development container, allowing you to execute arbitrary shell commands within the container.

```bash
sail shell

sail root-shell
```

## PHP Versions
<a name="php-versions"></a>

Sail Lite follows the [Supported PHP Versions](https://www.php.net/supported-versions.php), including versions only receiving Security Support. When a new version of PHP is released, a new major release of Sail Lite will be published, and any PHP versions that have reached End of Life will no longer be supported. If you need to use older versions of PHP, then you will need to use older versions of Sail Lite.

| Sail Lite | PHP Versions |
| --------- | ------------ |
| 1.x       | 8.1 - 8.4    |

To change the PHP version that is used to serve your application, you should update the `build` definition of the `dev` container in your package's `docker-compose.yml` file:

```bash
# PHP 8.4
context: ./vendor/reedware/sail-lite/runtimes/8.4

# PHP 8.3
context: ./vendor/reedware/sail-lite/runtimes/8.3

# PHP 8.2
context: ./vendor/reedware/sail-lite/runtimes/8.2

# PHP 8.1
context: ./vendor/reedware/sail-lite/runtimes/8.1
```

In addition, you may wish to update your `image` name to reflect the version of PHP being used by your package. This option is also defined in your package's `docker-compose.yml` file:

```bash
image: sail-8.4/dev
```

After updating your package's `docker-compose.yml` file, you should rebuild your container image

```bash
sail down

sail build --no-cache

sail up -d
```

## Customization
<a name="customization"></a>

Since Sail Lite is just Docker, you are free to customize nearly everything about it. To publish Sail Lite's own Dockerfiles, you may execute the publish command:

```bash
sail publish
```

After running this command, the Dockerfiles and other configuration files used by Sail Lite will be placed within a `docker` directory in your package's root directory. After customizing your Sail Lite installation, you may wish to change the image name for the development container in your package's `docker-compose.yml` file. After doing so, rebuild your development container using the `build` command. Assigning a unique name to the application image is particularly important if you are using Sail Lite to develop multiple PHP packages on a single machine:

```bash
sail build --no-cache
```
