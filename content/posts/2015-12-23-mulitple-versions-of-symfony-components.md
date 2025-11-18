+++
title = 'Supporting Multiple Versions of Symfony Components'
slug = 'mulitple-versions-of-symfony-components'
categories = ['symfony', 'php']
summary = """
With the release of Symfony 3.0 how do you support users that may be running
either 2 or 3? We talk about how to support multiple versions of Symfony
components and dive into how to test it on TravisCI.
"""
proof = false
+++
Symfony recently released version 3.0 of their components and framework. I use
these components in a couple of my own packages, along with a bunch of other
packages. How do we allow our package to work with multiple versions of the
Symfony components?

If your code is compatible in both 2.x and 3.0, you can support both of those
in your `composer.json` file. If not, you will likely want to manage different
releases of your library and let the user know which one to use for their
version of Symfony.

We can use a pipe operator as an _or_ in composer. For example in my
[FileLoader][file-loader] package, I use the Config and YAML components and I
want to support 2.3 and up. In my `composer.json` file, I changed these lines.

    // Old
    "symfony/config": "~2.3",
    "symfony/yaml": "~2.3"

    // New
    "symfony/config": "~2.3|~3.0",
    "symfony/yaml": "~2.3|~3.0"

If the project already has an existing version of the Symfony component, it will
use that, otherwise it will use the latest allowed, which in this case is 3.0.

### Testing

Our package now supports multiple versions, but how do we verify that it works
in all those packages? We're going to look at how to do this in
[TravisCI][travis]. If you're not using Travis, I would highly recommend it for
open source projects. It makes continuous integration a breeze.

Let's take a look at a basic `.travis.yml` file for a PHP project.

{{< highlight yaml >}}
language: php

php:
  - '5.4'
  - '5.5'
  - '5.6'
  - '7.0'
  - hhvm

sudo: false

before_script:
  - composer install --dev --no-interaction
{{< /highlight >}}

With this it will only test against the latest version of Symfony components in
each PHP version we specify. We can use
[environment variables][travis-env-variables] to test against multiple versions
of Symfony components. Let's add one for each version we are allowing in our
`composer.json`, in this case 2.3 and above.

{{< highlight yaml >}}
env:
  - SYMFONY_VERSION=2.3.*
  - SYMFONY_VERSION=2.4.*
  - SYMFONY_VERSION=2.5.*
  - SYMFONY_VERSION=2.6.*
  - SYMFONY_VERSION=2.7.*
  - SYMFONY_VERSION=2.8.*
  - SYMFONY_VERSION=3.0.*
{{< /highlight >}}

Then in our `before_script` section, we're going to tell composer to require
that version of Symfony before we run composer.

{{< highlight yaml >}}
before_script:
  - composer self-update
  - if [ "$SYMFONY_VERSION" != "" ]; then composer require "symfony/symfony:${SYMFONY_VERSION}" --no-update; fi;
  - composer update
{{< /highlight >}}

We also told composer to self-update, as travis is running an old version of
composer.

For each one of the `env` values and PHP version we are testing against, Travis
will create a build. This is our build Matrix. Every time we push, it will
create our Matrix and look something like this.

{{< image "travis-builds.png" "Travis builds" >}}

Our Matrix can also be customized manually using the `matrix` option in our
Travis config. We are going to do that to tell Travis not to create a build for
PHP 5.4 and Symfony 3.0, as Symfony 3.0 requires PHP 5.5 or above.

{{< highlight yaml >}}
matrix:
  exclude:
    - php: '5.4'
      env: SYMFONY_VERSION=3.0.*
{{< /highlight >}}

You can add any number of things to exclude or even include here. Just check out
the [travis docs][travis-matrix].

One final thing we are going to do is cache the global Composer directory. This
will allow Composer to use a cached version of the Symfony component, rather
than having to fetch it every time.

With all that said and done, our completed `.travis.yml` file should look like
this.

{{< highlight yaml >}}
language: php

php:
  - '5.4'
  - '5.5'
  - '5.6'
  - '7.0'
  - hhvm

sudo: false

cache:
  directories:
    - $HOME/.composer

env:
  - SYMFONY_VERSION=2.3.*
  - SYMFONY_VERSION=2.4.*
  - SYMFONY_VERSION=2.5.*
  - SYMFONY_VERSION=2.6.*
  - SYMFONY_VERSION=2.7.*
  - SYMFONY_VERSION=2.8.*
  - SYMFONY_VERSION=3.0.*

matrix:
  exclude:
    - php: '5.4'
      env: SYMFONY_VERSION=3.0.*

before_script:
  - composer self-update
  - if [ "$SYMFONY_VERSION" != "" ]; then composer require "symfony/symfony:${SYMFONY_VERSION}" --no-update; fi;
  - composer update
{{< /highlight >}}

Commit, push, and verify that your code works with all versions of the Symfony
components.

#### Bonus: Getting Coveralls Working

If you are using [php-coveralls][php-coveralls] and happen to be using the
Config Symfony component (like FileLoader is), then you will not be able to test
against Symfony 2.3, as php-coveralls requires 2.4 or later. To fix this, I've
opted to install it globally in composer. You could also download the `.phar`
and use that instead.

{{< highlight yaml >}}
after_success:
  - composer global require satooshi/php-coveralls
  - travis_retry php $HOME/.composer/vendor/bin/coveralls -v
{{< /highlight >}}

_Update 12/27/15_: As some commenters have pointed out, Symfony 2.4, 2.5, and 2.6
are no longer supported. You can skip these in your Travis builds if you please.

[file-loader]: https://github.com/ivorisoutdoors/FileLoader
[travis]: https://travis-ci.org/
[travis-env-variables]: https://docs.travis-ci.com/user/environment-variables/
[travis-matrix]: https://docs.travis-ci.com/user/customizing-the-build/#Build-Matrix
[php-coveralls]: https://github.com/satooshi/php-coveralls
