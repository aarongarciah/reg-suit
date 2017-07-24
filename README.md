# reg-suit
[![CircleCI](https://circleci.com/gh/reg-viz/reg-suit.svg?style=svg)](https://circleci.com/gh/reg-viz/reg-suit)
[![npm version](https://badge.fury.io/js/reg-suit.svg)](https://badge.fury.io/js/reg-suit)

Tool for visual regression testing.

* *Easy to setup*
* *Easy to integrate your CI flow*
* *Easy to extend*

## Getting Started

```sh
$ npm install -g reg-suit
$ cd path-to-your-project
$ reg-suit init
# Answer a few questions...
```

```sh
$ reg-suit run
```

## Plugins
reg-suit has it's own plugin system. Plugins integrate your project with various services.

The following plugins are available:

- [reg-keygen-git-hash-plugin](https://github.com/reg-viz/reg-suit/tree/master/packages/reg-keygen-git-hash-plugin) - *key-generator plugin* - This plugin provides functions to identify "what commit hash should I compare to" walking git branch graph.
- [reg-publish-s3-plugin](https://github.com/reg-viz/reg-suit/tree/master/packages/reg-publish-s3-plugin) - *publisher plugin* - This plugin fetches the previous snapshot images from S3 bucket and these fetched snapshots are used as expected images in comparison. After the comparison, this plugin pushes the current(the actual) snapshot images and the report of the comparison result.
- [reg-notify-github-plugin](https://github.com/reg-viz/reg-suit/tree/master/packages/reg-notify-github-plugin) - *notifier plugin* - This plugin integrates reg-suit and it's [GitHub app](https://github.com/apps/reg). Installing these plugin and app, you can receive reg-suit result via GitHub commit status and PR comment.
- [reg-notify-slack-plugin](https://github.com/reg-viz/reg-suit/tree/master/packages/reg-notify-slack-plugin) - *notifier plugin* - This plugin notifies reg-suit result to your Slack channel using incoming Webhook.

## CLI Usage

```sh
reg-suit [options] <command>
```

### `run` command

Run visual testing, publish the current snapshot images, and send notifications.

### `init` command

Install and configure reg-suit and plugins into your project.

- `--use-yarn` : By the default cli installs packages using `npm`. If you prefer yarn pkg, turn this option on.

### `prepare` command

Configure the installed plugin(s). It's useful to configure reg-suit and plugins.

- `--plugin` : Specify plugin name(s) to be configured.

### Global options
*T.B.D*

If you want more details, please exec `reg-suit -h` or `reg-suit <command> -h`.

## Run with CI service
A working demonstration is [here](https://github.com/reg-viz/reg-simple-demo).

### *Workaround for Detached HEAD*

reg-suit(git-hash-plugin) needs the current branch name to identify the base-commit hash. However, under some CI services' environment(e.g. TravisCI, WerckerCI), the HEAD is detached. So you should attach it explicitly. 

For example:

```yml
# .travis.yml 

script:
  - git checkout $TRAVIS_BRANCH || git checkout -b $TRAVIS_BRANCH
  - reg-suit run
```

```yml
# wercker.yml

build:
  steps:
    - script:
      name: Attach HEAD
      code: |
        git checkout $WERCKER_GIT_BRANCH || git checkout -b $WERCKER_GIT_BRANCH
    - script:
      name: Run reg-suit
      code: |
        reg-suit run
```

## How it works

reg-suit calls installed plugins according to the following procedure:

* Determine the key of snapshot which will be used as the expected images via installed key-generator plugin. This step will be skipped if key-generator plugin is not set.
* Fetch the previous snapshot images as the expected snapshot via installed publisher plugin. This step will be skipped if publisher plugin is not set.
* Compare images using [reg-cli](https://github.com/reg-viz/reg-cli) and create report.
* Determine the key of the current snapshot via installed key-generator plugin. If key-Generator is not set, time stamp is used.
* Publish the current snapshot and report via installed publisher-plugin. This step will be skipped if publisher plugin is not set.
* Notify result via installed notifier-plugin.


## License
The MIT License (MIT)

Copyright 2017 Quramy

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.