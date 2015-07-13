## About

This project utilizes the [pantheon-systems/circle-scripts](https://github.com/pantheon-systems/circle-scripts) project in order to achieve the following things:

* Specify the Drupal modules, themes and libraries you use in a composer.json file, and build them with Composer.
* Automatically build components via Circle CI every commit.
* Use Behat to run tests on your site from Circle.
* Automatically deploy your site to your Pantheon dev environment, or some other branch, every time the tests pass.

## Create a New Project

Before you begin, you should first [install composer](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx).

There are two ways to quickly create a new project for your Drupal site, using this project as a template.

#### Via GitHub

* [Fork this project in GitHub](https://github.com/pantheon-systems/example-drupal7-circle-composer#fork-destination-box)
* Clone your fork locally
* `$ cd example-drupal7-circle-composer`
* `composer install`
* `$ ./bin/init-new-project`

The `init-new-project` script will set up the local files you will need, and commit them to your local git repository.

#### Via Composer
```
$ composer create-project pantheon-systems/example-drupal7-circle-composer my-new-project-name
```

The `composer create-project` will download the base files you need for your new project, then run the `init-new-project` script to set things up.  The results are committed to a local git repository.  You might wish to [push your repository up to GitHub](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/).

## Configuration

Once you have created a new project, you will still need to do some customization steps to suit your  particular needs.  See the [circle-scripts README](https://github.com/pantheon-systems/circle-scripts) for instructions on how to do this.

## Testing Locally
```
$ ./bin/local-test
```

The `local-test` script will set up a local Drupal site, run it with the PHP built-in webserver, and then run the Behat test suite.  This is analagous to what happens on Travis on every commit.

## Repository Management

You may place your custom modules and themes in `drupal/sites/all/modules/custom` and `drupal/sites/all/themes/custom`, respectively, and commit them to the same repository that contains your composer.json file.

If you prefer, you may instead create a Composer project for your custom components, and add them to your composer.json file.  It is not necessary to make your code publicly accessible in order to do this; it is possible to create a local composer package definition that points to a private GitHub repository. See [Creating your very own Composer Package](https://knpuniversity.com/screencast/question-answer-day/create-composer-package) for details on different ways to create composer packages.

Additional modules may be added using Composer:
```
$ cd my-new-project-name
$ composer require drupal/devel '7.*'
```
This will put the specified module in `sites/all/modules/contrib`, and will also update your composer.json file with the information on the module that was just added.

