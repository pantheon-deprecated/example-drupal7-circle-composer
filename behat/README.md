This folder contains behat tests that can be run in a number of different configurations.

- Local: Both Behat and the Drupal site under test are running on the local system.
- CI: Both Behat and the Drupal site under test are running on the CI server
- Pantheon: Behat is running either locally or on the CI server, and the Drupal site under test is running on Pantheon.

The tests for these configurations are stored in different feature directories:

- features: Basic tests that always run first in every environment, to ensure that the build is basically okay.
- common-features: These tests can run locally, or on Pantheon. When testing on the CI server, the common tests typically should be run on *either* the CI server *or* on the Pantheon server, but typically not in both locations.
- pantheon-features: These tests can only run on Pantheon, typically because they use some component that might not be available on CI or the local system, e.g. Redis or Solr.

In order to run tests on Pantheon, you must customize the behat-pantheon.yml file.

Mink Extension:

Modify the base_url to point at the Pantheon site and environment to test on.
```
  base_url: http://dev-example-drupal7-circle-composer.pantheon.io/
```
Drupal Extension:

Modify the alias to indicate the name of the Drush alias to use in tests. Note that the test scripts will download your aliases via Terminus, and place them in an appropriate location; you may use something other than 'pantheon-ci' for the alias group, if you wish. The site name and site environment in the alias should match the site used in the base_url.
```
  alias: '@pantheon-ci.example-drupal7-circle-composer.dev'
```
