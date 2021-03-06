Docker image for Python-based SBE/BDD tools
===


[![Circle CI](https://circleci.com/gh/William-Yeh/docker-behave.svg?style=shield)](https://circleci.com/gh/William-Yeh/docker-behave) [![Build Status](https://travis-ci.org/William-Yeh/docker-behave.svg?branch=master)](https://travis-ci.org/William-Yeh/docker-behave)


## Summary

Repository name in Docker Hub: **[williamyeh/behave](https://hub.docker.com/r/williamyeh/behave/)**

This repository contains Dockerized Python tools for SBE/BDD, published to the public [Docker Hub](https://hub.docker.com/) via **automated build** mechanism.


## Configuration

This docker image contains the following software stack:

- Base image: Debian Stretch + [Python 3](https://hub.docker.com/_/python/).

- [Behave](https://pypi.python.org/pypi/behave): behavior-driven development, Python style.

- [Selenium WebDriver](http://www.seleniumhq.org/projects/webdriver/): a suite of tools to automate web browsers across many platforms.

- [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/): a standalone server which implements WebDriver's wire protocol for Chromium.

- Selenium wrapper for Python:
  - [Elementium](https://github.com/actmd/elementium): jQuery-style syntactic sugar for Selenium.
  - [Capybara](https://elliterate.github.io/capybara.py/): a port of [Capybara from Ruby](https://en.wikipedia.org/wiki/Capybara_(software)).

- Other useful Python testing libraries for convenience:
  - [Mockito](http://mockito-python.readthedocs.io/en/latest/): a spying framework ported from Java.
  - [PyShould](https://github.com/drslump/pyshould): "Should"-style asserts based on [PyHamcrest](https://github.com/hamcrest/PyHamcrest).




## Usage


**Given** a `features` sub-directory as follows:

```
.
└── features
    ├── environment.py
    ├── requirements.txt
    ├── xxx.feature
    ├── yyy.feature
    └── steps
        ├── xxx_step.py
        └── yyy_step.py
```

**When** I invoke the program as follows:

```
$ docker run -it --rm -v "$(pwd):/behave:ro"  williamyeh/behave
```

**Then** I can see the output as follows:

```
Feature: ...
[...]
1 feature passed, 0 failed, 0 skipped
7 scenarios passed, 0 failed, 0 skipped
21 steps passed, 0 failed, 0 skipped, 0 undefined
Took 0m11.532s
```


Note: `requirements.txt` in `features` directory.


## Run with browser in headless mode

This is the default mode.


## Run with desktop's browser

To run with the desktop's browser, please follow the steps.

```
+---------------------------------------------------------+
|                                                         |
|                                                 host    |
|   +----------------+                                    |
|   |     Docker     |                                    |
|   |                |                                    |
|   |                |                                    |
|   |                |                                    |
|   |                |                                    |
|   |    container   |                        Chrome      |
|   |        |       |      ChromeDriver      browser     |
|   +----------------+       |        |          |        |
|            |               |        |          |        |
|            +---------------+        +----------+        |
|                    /wd/hub:9515                         |
|                                                         |
+---------------------------------------------------------+
```


### Requirements

1. Install [Chrome](https://www.google.com.tw/chrome/browser/desktop/).

2. Install [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/). Mac users can install it as follows:

   ```
   $ brew install chromedriver
   ```


### Configuration

Configure `features/environment.py` as follows:

```python

# connect from Docker container to host
REMOTE_CHROME_ADDR = "http://docker.for.mac.localhost:9515/wd/hub"

@capybara.register_driver("selenium_remote_chrome")
def init_selenium_chrome_driver(app):
    from capybara.selenium.driver import Driver
    return Driver(app, browser="remote",
                  command_executor=REMOTE_CHROME_ADDR,
                  desired_capabilities=DesiredCapabilities.CHROME)


def before_all(context):
    capybara.current_driver = "selenium_remote_chrome"
```


### Run

1. Start the ChromeDriver with base URL path `/wd/hub` and default port `9515`:

   ```
   $ chromedriver --url-base=/wd/hub
   ```

2. Now you're ready to run `williamyeh:behave`!


## TODO

1. Detailed document.
2. Firefox?
3. Flow with Jenkins.



## Tutorial

*Behave*

 - [Behave tutorial](https://pythonhosted.org/behave/tutorial.html)


*Selenium*

- https://intoli.com/blog/running-selenium-with-headless-chrome/


*Elementium*





*Capybara*




## License

Author: William Yeh <william.pjyeh@gmail.com>

Licensed under the Apache License V2.0. See the [LICENSE file](LICENSE) for details.
