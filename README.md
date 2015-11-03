## About

This project utilizes the [pantheon-systems/ci-scripts](https://github.com/pantheon-systems/ci-scripts) project in order to achieve the following things:

* Specify the Drupal modules, themes and libraries you use in a composer.json file, and build them with Composer.
* Automatically build components via Circle CI every commit.
* Use Behat to run tests on your site from Circle.
* Automatically deploy your site to your Pantheon dev environment, or some other branch, every time the tests pass, and run tests against the site running on Pantheon as well.

## Create a New Project

Before you begin, you should first [install composer](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-osx).

There are two ways to quickly create a new project for your Drupal site, using this project as a template.

#### Via GitHub

* [Fork this project in GitHub](https://github.com/pantheon-systems/example-drupal7-circle-composer#fork-destination-box)
* Clone your fork locally
* `$ cd example-drupal7-circle-composer`
* `composer install`

After `composer install` runs, the example Behat tests from ci scripts will be copied into the `behat` directory. You may thereafter modify these tests to suit, and commit them to your project git repository.

You will need to modify the file behat/behat-pantheon.yml to point at the Pantheon test site and environment that you have created for testing.

#### Via Composer
```
$ composer create-project pantheon-systems/example-drupal7-circle-composer my-new-project-name
```

The `composer create-project` will download the base files you need for your new project, then run the `init-new-project` script to set things up.  The results are committed to a new project git repository.  You might wish to [push your repository up to GitHub](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/).

## Configuration

Once you have created a new project, you will still need to do some customization steps to suit your  particular needs.

The folder 'scripts' contain some starting scripts that you may use to customize the environment you need for your tests.  Define values for the variables given in default-environment to control the behavior of the test scripts. Add commands to the install-configuration script to set up default configuration for your test site.

## Testing

There are four ways to test:

- Locally, with both Behat and the Drupal sites running on the local machine.
- Locally, with Behat running on the local machine, and the Drupal site on Pantheon.
- On Circle CI, with both Behat and the Drupal site running on the Circle server.
- On Circle CI, with Behat running on the Circle server, and the Drupal site on Pantheon.

See the [README](behat/README.md) file in the Behat directory for details on where to put your tests, and how to configure behat-pantheon.yml for the Pantheon tests.

#### Testing Locally
```
$ ./bin/local-test
```

The `local-test` script will set up a local Drupal site, run it with the PHP built-in webserver, and then run the Behat test suite.  This is analagous to what happens on Circle on every commit.

#### Testing on Circle CI

Set up your project to be tested by Circle CI:

* Log on to https://circleci.com in your web browser.
* Click on the "+", "Add Projects", on the left-hand menu bar.
* Find the repository you would like to configure to test.  Navigate to the right organization, if necessary, and use the "Filter repos..." field to reduce the size of the displayed list.
* Click "Build Project" to enable the repository and build it for the first time.

Next, set up the environment variables used by the push-to-pantheon script:

Variable Name      | Value
------------------ | --------------------------------------------
PEMAIL             | The email used to log in to a Pantheon User with access to the site
PPASS              | The password for the acount specified by PEMAIL
GITHUB_OAUTH_TOKEN | Authorization token to increase rate limit (RECOMMENDED)
SITE_NAME          | The name of the site used in "Site Install" (OPTIONAL)
PSITE              | The name of the Pantheon site to push to (OPTIONAL)
PENV               | The Pantheon site environment to overwrite on each test (OPTIONAL)
CI_BOT_EMAIL       | The email address to use in the git commit attribution (OPTIONAL)
CI_BOT_NAME        | The name to use in the git commit attribution (OPTIONAL)

Edit these in Project Settings > Environment Variables (https://circleci.com/gh/ORG/PROJECT/edit#env-vars).

PSITE and PENV will be read from the behat/behat-pantheon.yml file, if it exists. The values from the Drush Driver's 'alias:' entry is parsed for this information.

You also need to set up an ssh key, so that the push-to-pantheon script can commit changes to the Pantheon git repository.

* [Create a public/private ssh key pair](https://help.github.com/articles/generating-ssh-keys/). You might want to use a different key than the one you use with GitHub.
* [Add the public ssh key to your Pantheon account](https://pantheon.io/docs/articles/users/loading-ssh-keys/).
* [Add the private ssh key to Circle CI](https://circleci.com/docs/permissions-and-access-during-deployment). You may either leave the "Host" field blank, or set it to "codeserver.$PENV.$PUUID.drush.in", where "$PENV" is replaced with the same value you used in the PENV environment variable, and "$PUUID" is replaced with the [UUID for your Pantheon site](https://pantheon.io/docs/articles/sites/).

See the [Circle CI documentation](https://circleci.com/docs/getting-started) for more information on configuration.

## Repository Management

You may place your custom modules and themes in `drupal/sites/all/modules/custom` and `drupal/sites/all/themes/custom`, respectively, and commit them to the same repository that contains your composer.json file.

If you prefer, you may instead create a Composer project for your custom components, and add them to your composer.json file.  It is not necessary to make your code publicly accessible in order to do this; it is possible to create a local composer package definition that points to a private GitHub repository. See [Creating your very own Composer Package](https://knpuniversity.com/screencast/question-answer-day/create-composer-package) for details on different ways to create composer packages.

Additional modules may be added using Composer:
```
$ cd my-new-project-name
$ composer require drupal/devel '7.*'
```
This will put the specified module in `sites/all/modules/contrib`, and will also update your composer.json file with the information on the module that was just added.

