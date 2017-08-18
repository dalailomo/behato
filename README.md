# Behato

Quick behavioural testing tool using [behat](http://behat.org/en/latest/) and [vvo/selenium-standalone](https://github.com/vvo/selenium-standalone).

## Requirements

- PHP >= 5.6
- NodeJS >= 7.10

## Installation

First require the dependencies: 

```bash
$ composer require dalailomo/behato
```

Then, install the selenium server:

```bash
$ bin/installSeleniumStandalone
```

Create a config file called `behat.yml` in the root of your project with the following:

```yaml
# behat.yml
default:
  suites:
    default:
      contexts:
        - BaseContext
        - Devinci\DevinciExtension\Context\DebugContext:
            asset_dump_path: '<THE PATH YOU WANT TO DUMP ASSETS>'
        - Devinci\DevinciExtension\Context\JavascriptContext:
            maximum_wait: 30
  extensions:
    Behat\MinkExtension:
      base_url:  '<THE BASE URL YOU WANT>'
      browser_name: chrome
      sessions:
        default:
          #goutte: ~
          selenium2: ~
```

Then run `bin/behat --init`

## Testing example

### Set the parameters in the config file
- Change the `asset_dump_path` key to `%paths.base%/asset` (make sure you create a directory called `asset` on the root path of your project).
- Change the `base_url` key to `http://wikipedia.org`

### Create a feature file

Now, create a file `features/search.feature` on `features` folder (this has been created by running `bin/behat --init`).

```yaml
Feature: Search
  In order to see a word definition
  As a website user
  I need to be able to search for a word

  Scenario: Searching for a page that does exist
    Given I am on "/wiki/Main_Page"
    When I fill in "search" with "Behavior Driven Development"
    And I press "searchButton"
    Then I should see "agile software development"

  Scenario: Searching for a page that does NOT exist
    Given I am on "/wiki/Main_Page"
    When I fill in "search" with "Glory Driven Development"
    And I press "searchButton"
    Then I should see "Search results"
```

Now, go to the file `features/bootstrap/BaseContext.php` and change the contents by the following:

```php
<?php

use Behat\MinkExtension\Context\MinkContext;

/**
 * Defines application features from the specific context.
 */
class BaseContext extends MinkContext
{
    /**
     * Initializes context.
     *
     * Every scenario gets its own context instance.
     * You can also pass arbitrary arguments to the
     * context constructor through behat.yml.
     */
    public function __construct()
    {
    }
}
```

Now, in a separate console, run the server:

```bash
$ bin/runServer
```

And run the test!

```bash
$ bin/behat
```

> If a step fails, you will see a screenshot and a HTML dump of the failed step inside the `asset/` folder. Pretty useful huh?
