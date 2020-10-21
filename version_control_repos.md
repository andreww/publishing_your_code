# Using version control to work with others

Assume at this point that software is under version control and this
is being used for development. Further, assume that repository existis 
in GitHub (other hosts are available, but I'm just starting with this one).
Aim here is to make use of git and GitHub to ease further development.

Probably best to got for a model where current version of software lives
on a specific branch in a GitHub repository (this should probably be called
"main", not "master" - TODO: work out how to change this). Modifications
(fixes or new features) are made to branches which could live in the main
developers repository or (probably best for others) in a forked repository.
Modifications merged via pull requests. With this in mind, there are several 
things to consider:

## Reviewing pull requests

It is probably best to not randomly accept pull requests but to look at the
changes first. Key skill here is code review (TODO: describe this in a seperate
page). Also need to consider things like power dynamics in teams of developers
and research groups.

Sometimes it does not make sense to merge a pull request, even if the code is
perfect and a new feature is added. Maybe the new feature is just not needed
or the additional maintenance overhead is not worth it. Speaks to the need for
contributor guidance and clear aims (and limitations) on the software. (TODO:
describe this somewhere).

## Automated checks

It is possible (and desirable) to automatically check that changes do not introduce
errors. This can be done in GitHub using webhooks or actions. More generally these
tools can be used to enable continuous integration (CI), a development practice where
changes are merged and put to use regularly. For this to work well the process from
writing code to putting it to use needs to be as automatic as possible and this process
needs to minimise the possibility to introducing errors. Here we will use CI tooling to
automate some of the checks you may want to do before merging changes.

### Linting with GitHub actions and the super-linter

GitHub actions allow tasks to run (on GitHub's cloud computing resources) in response to
changes being made to GitHub repositories. We will use this to run a linter on files that
are proposed to be changed within a pull request. Results of the linting process are presented
via the pull request interface and any problems can be fixed before the pull request is merged.
As a linter we'll use the ]super-linter](https://github.com/github/super-linter), which combines
linters for many different programming languages.

Setting this up is quite easy. On a new branch create a new file in
`.github/workflows/linter.yml` containing the following:

```
---
###########################
###########################
## Linter GitHub Actions ##
###########################
###########################
name: Lint Code Base

#
# Documentation:
# https://help.github.com/en/articles/workflow-syntax-for-github-actions
#

#############################
# Start the job on all push #
#############################
on:
  push:
    branches-ignore: [master]
    # Remove the line above to run when pushing to master
  pull_request:
    branches: [master]

###############
# Set the Job #
###############
jobs:
  build:
    # Name the Job
    name: Lint Code Base
    # Set the agent to run on
    runs-on: ubuntu-latest

    ##################
    # Load all steps #
    ##################
    steps:
      ##########################
      # Checkout the code base #
      ##########################
      - name: Checkout Code
        uses: actions/checkout@v2
        with:
          # Full git history is needed to get a proper list of changed files within `super-linter`
          fetch-depth: 0

      ################################
      # Run Linter against code base #
      ################################
      - name: Lint Code Base
        uses: github/super-linter@v3
        env:
          VALIDATE_ALL_CODEBASE: false
          DEFAULT_BRANCH: master
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

push the branch and create a pull request. That pull request should lint itself, and once merged all
future pull requests should be linted.

### Automated tests

## Issues

Probably need some discussion of these.

 
