# Contributing to satis-to-artifact-action

## Welcome!

We look forward to your contributions! Here are some examples how you can contribute:

* [Ask any questions you may have](https://github.com/mattgrul/satis-to-artifact-action/discussions/new/choose)
* [Report a bug](https://github.com/mattgrul/satis-to-artifact-action/issues/new?labels=bug&projects=&template=BUG_REPORT.yml&title=%5BBug%5D%3A+)
* [Propose a new feature](https://github.com/mattgrul/satis-to-artifact-action/issues/new?labels=enhancement&projects=&template=FEATURE_REQUEST.yml&title=%5BFeature%5D%3A+)
* [Send a pull request](https://github.com/mattgrul/satis-to-artifact-action/pulls)

## Contributor Code of Conduct

Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in
this project you agree to abide by its terms.

## Getting Started

To get started [Fork][fork] `satis-to-artifact-action` and then clone it using git:

```bash
git clone https://github.com/<your-username>/satis-to-artifact-action.git

cd satis-to-artifact-action
```

## Workflow to create Pull Requests

We recommend that you create a new branch for each bug or feature that you will work.

* [Fork](https://github.com/mattgrul/satis-to-artifact-action/fork) the `satis-to-artifact-action` project and clone it.
* Create a new branch `git checkout -b my-branch-name`
* Make your bug fix or feature addition.
* Make any updates to the `test-action` workflow if applicable.
* Ensure the `test-action` workflow passes. See [Running the test workflow](#running-the-test-suite).
* Send a pull request to the relevant branch.

### Choosing a branch

You will want to choose your branch based on the work you are doing:

* For a **feature**, your branch should be created from the `main` branch and Pull Requests submitted to `main`
* For a **bug** fix, your branch should be created from the appropriate latest stable release you are fixing. For example, a
  bug fix for `v1` should be created from `releases/v1` and a Pull Request submitted to the same matching branch.

### Running the test workflow

To test changes locally before pushing to GitHub `act` can be used. See the [act documentation](https://github.com/nektos/act) for more information.

An example command to run the `test-action` workflow locally via GitHub CLI using the `shivammathur/setup-php` image:

```bash
gh act -P ubuntu-latest=shivammathur/node:latest -s GITHUB_TOKEN="$(gh auth token)" --artifact-server-path /tmp/artifacts
```
