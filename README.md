[![CircleCI](https://circleci.com/gh/pantheon-systems/example-drupal7-circle-composer/tree/master.svg?style=svg)](https://circleci.com/gh/pantheon-systems/example-drupal7-circle-composer/tree/master)

## About

This project template will create a Drupal 7 site that is managed by Composer and is tested on every commit on Circle CI using Behat, with the Drupal site either running on Pantheon or the Circle server.  Only a few quick steps are required to set up either an entirely new site, or convert an existing site.

Adding new tests to your project will also be very easy; just follow the example of the included tests.

## Getting Started

Before you begin, you should first [install composer](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx).

Once you have Composer on your system, create a new project:
```
$ composer create-project pantheon-systems/example-drupal7-circle-composer put-your-site-name-here
```
If you already have a Drupal 7 site running on Pantheon, you should use the same name for your project as you used for your Pantheon site. If you are starting fresh, and would like to test on Pantheon, you should [sign up for a free Agency account](https://pantheon.io/agencies/pantheon-for-agencies) if you have not already done so.

## Testing

There are four ways to test:

- Locally, with both Behat and the Drupal sites running on the local machine.
- Locally, with Behat running on the local machine, and the Drupal site on Pantheon.
- On Circle CI, with both Behat and the Drupal site running on the Circle server.
- On Circle CI, with Behat running on the Circle server, and the Drupal site on Pantheon.

#### Testing Locally

Running all tests with both Behat and the Drupal site running on the local system, all you need to do is run the local-test script:
```
$ ./bin/local-test
```
If you would like to run Behat on the local system, and test your Drupal site running on Pantheon, log on with Terminus, and then run the pantheon-test script:
```
$ terminus auth login
$ ./bin/pantheon-test
```
This will probably just work, but if it does not, see the Customization section below; you may need to set up your database credentials.

#### Testing on Circle CI
 
Set up your project to be tested by Circle CI:
 
* Log on to https://circleci.com in your web browser.
* Click on the "+", "Add Projects", on the left-hand menu bar.
* Find the repository you would like to configure to test.  Navigate to the right organization, if necessary, and use the "Filter repos..." field to reduce the size of the displayed list.
* Click "Build Project" to enable the repository and build it for the first time.

The provided circle.yml file is set up to first do a brief smoke-test on the Circle server, and, if it passes, it will then copy the site up to Pantheon, and run the rest of the Behat tests against the Drupal site running on Pantheon.  The Behat configuration files should be set up correctly to run the basic tests in this way.  See the Customization section below for information on how to add more tests, or change the way it behaves (e.g. to test only on Circle).

In order to have the tests run correctly on Circle, it is necessary to to define some "secret" environment variable values so that the Circle server can authenticate with Terminus.  The necessary variables are shown in the table below:
 
Variable Name      | Value
------------------ | --------------------------------------------
PEMAIL             | The email used to log in to a Pantheon User with access to the site
PPASS              | The password for the acount specified by PEMAIL
GITHUB_OAUTH_TOKEN | Authorization token to increase rate limit (RECOMMENDED)

Edit these in Project Settings > Environment Variables (https://circleci.com/gh/ORG/PROJECT/edit#env-vars). Once you set the values here, Circle CI will keep them secret, so other parties will not be able to see what they are, even if they log in to the Circle CI website.

n.b. These values can be exposed if code is committed to the repository that displays them; for this reason, Circle does not pass these values in for pull requests submitted from external sources. Therefore, the Pantheon tests will not run for these kinds of PRs.

You also need to set up an ssh key, so that the push-to-pantheon script can commit changes to the Pantheon git repository.
 
* [Create a public/private ssh key pair](https://help.github.com/articles/generating-ssh-keys/). You might want to use a different key than the one you use with GitHub.
* [Add the public ssh key to your Pantheon account](https://pantheon.io/docs/articles/users/loading-ssh-keys/).
* [Add the private ssh key to Circle CI](https://circleci.com/docs/permissions-and-access-during-deployment). You may either leave the "Host" field blank, or set it to "codeserver.$PENV.$PUUID.drush.in", where "$PENV" is replaced with the same value you used in the PENV environment variable, and "$PUUID" is replaced with the [UUID for your Pantheon site](https://pantheon.io/docs/articles/sites/).

See the [Circle CI documentation](https://circleci.com/docs/getting-started) for more information on configuration.

## Customization

Once you have created a new project, you will still need to do some customization steps to suit your  particular needs.  The create-project scripts do the best job they can to customize your new project, so it should run right out of the box.  The various customization options are very useful for adding in any additional items needed for your particular site.

The folders `behat` and `scripts` are copied into your project, and may be freely modified and then checked in to your github repository.  Any location listed in the provided .gitignore file is managed by Composer, and should not be modified or committed.

#### Environment Variables

There are three places to define environment variables.

- Circle CI Environment Variables Setting, for sensitive values
- circle.yml, for non-sensitive values
- In scripts/default-environment file, for non-sensitive values common to all environment

See the comments in [default-environment](scripts/default-environment) for descriptions of the different variables that can be set. Instructions for setting sensitive values in Circle CI are described above in the section "Testing on Circle CI".

#### Adding Modules to the Site

Additional modules may be added using Composer:
```
$ cd my-new-project-name
$ composer require drupal/devel '7.*'
```
This will put the specified module in `sites/all/modules/contrib`, and will also update your composer.json file with the information on the module that was just added.

If you have an existing site with existing modules, use the [composer-generate](https://www.drupal.org/project/composer_generate) Drush command to generate a composer.json file from the existing site. Copy the modules from the `require` section of the generated file, and ignore the other parts of this file.

#### Custom Modules

You may place your custom modules and themes in `drupal/sites/all/modules/custom` and `drupal/sites/all/themes/custom`, respectively, and commit them to the same repository that contains your composer.json file.

If you prefer, you may instead create a Composer project for your custom components, and add them to your composer.json file.  It is not necessary to make your code publicly accessible in order to do this; it is possible to create a local composer package definition that points to a private GitHub repository. See [Creating your very own Composer Package](https://knpuniversity.com/screencast/question-answer-day/create-composer-package) for details on different ways to create composer packages.

#### Post Installation

The [post-install](scripts/post-install) script will be executed after the Drupal site is installed. You can use this script to install your configuration, e.g. by reverting [features](https://www.drupal.org/project/features) or update master configuration via [ctools export bonus](https://www.drupal.org/project/drush_ctex_bonus).

#### Adding More Tests

The folder 'scripts' contain some starting scripts that you may use to customize the environment you need for your tests. See the [README](behat/README.md) file in the Behat directory for details on where to put your tests, and how to configure behat-pantheon.yml for the Pantheon tests.




