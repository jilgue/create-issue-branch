# Create Issue Branch

![Logo](static/logo.png)

[![BCH compliance](https://bettercodehub.com/edge/badge/robvanderleek/create-issue-branch?branch=master)](https://bettercodehub.com/)
[![Build Status](https://github.com/robvanderleek/create-issue-branch/workflows/CICD/badge.svg)](https://github.com/robvanderleek/create-issue-branch/actions)
[![Build Status](https://github.com/robvanderleek/create-issue-branch/workflows/Create%20Release/badge.svg)](https://github.com/robvanderleek/create-issue-branch/actions)
[![Dependabot](https://badgen.net/badge/Dependabot/enabled/green?icon=dependabot)](https://dependabot.com/)
[![Sentry](https://img.shields.io/badge/sentry-enabled-green)](https://sentry.io)

A GitHub App/Action that automates the creation of issue branches (either automatically after assigning an issue or after commenting on an issue with a ChatOps command: `/create-issue-branch` or `/cib`).

Built in response to this feature request issue: https://github.com/isaacs/github/issues/1125

# Installation

There are two options to run this app as part of your development workflow:

1. [Install](https://github.com/apps/create-issue-branch) it as an *app* for your organization/account/repository
2. Run it as an *action* in your GitHub action YAML configuration

Option 1 is easiest if you're developing on GitHub.com, option 2 gives you full control how and when the app runs in your development workflow.

## Option 1. Install the GitHub App

You can install the app for your organization/account/repository from [*this page*](https://github.com/apps/create-issue-branch)

## Option 2. Configure GitHub Action

Add this to your workflow YAML configuration:

```yaml
on:
    issues:
        types: [assigned]
    issue_comment:
        types: [created]

jobs:
    create_issue_branch_job:
        runs-on: ubuntu-latest
        steps:
        - name: Create Issue Branch
          uses: robvanderleek/create-issue-branch@master
          env:
            GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

# Usage

This app can support your development workflow in two ways (modes): auto and chatops.

In "auto" mode the typical development workflow is:

 1. An issue is created, for example: Issue 15: Fix nasty bug!

 *some time may pass*

 2. The issue is assigned
 3. When the issue is assigned this app will create a new issue branch
    (for the example issue this branch will be called `issue-15-Fix_nasty_bug`)

In "chatops" mode the typical development workflow is:

 1. An issue is created, for example: Issue 15: Fix nasty bug!

 *some time may pass*

 2. A developer that wants to work on this issue gives the ChatOps command `/cib` as a comment on the issue
 3. This app will create a new issue branch
    (for the example issue this branch will be called `issue-15-Fix_nasty_bug`)
    By default the app notifies creation is completed with a comment on the issue.

# Configuration

This app does not require a configuration. However, if you want to override 
the default behaviour you can do so by placing a YAML file in your repository 
at the location: `.github/issue-branch.yml` with the overrides.

If the app has a problem with your configuration YAML (e.g.: invalid content) it will create an issue 
with the title "Error in Create Issue Branch app configuration" in the repo. Subsequent runs with an 
invalid configuration will not create new issues, only one stays open. 

## Organization/User wide configuration

Organization/user wide configuration prevents a configuration in every individual repo and is supported by putting the YAML file `.github/issue-branch.yml`
in a repository called `.github`. So, if your organization/username is `acme`, the full path becomes:
`https://github.com/acme/.github/blob/master/.github/issue-branch.yml`. 

## Mode: auto or chatops

The default mode is "auto", meaning a new issue branch is created after an issue is assigned.

You can change the mode to "chatops", meaning a new issue branch is created after commenting on an issue with `/create-issue-branch` or `/cib`, by puuting the following line in your `issue-branch.yml`:

```yaml
mode: chatops
```

## Silent or chatty

By default in mode "Auto" this app is silent. By default in mode "ChatOps" the app comments on the issue after creating a branch.

You can change this default behaviour by putting the following line in your `issue-branch.yml`:

```yaml
silent: true # or false
```

## Branch names

Branch names are generated from the issue, there are 3 flavours:

 1. `tiny` => an `i` followed by the issue number, for example: `i15`
 2. `short` => the word `issue` followed by the issue number, for example:
    `issue-15`
 3. `full` => the word issue followed by the issue number followed by the
    issue title, for example: `issue-15-Fix_nasty_bug`
    
The default is `full`, other types can be configured in the YAML like this:

```yaml
branchName: tiny
```

or

```yaml
branchName: short
```

## Source branch based on issue label

You can override the source branch (by default the "default branch" of the
repository is used) based on the issue label.

For example, if you want branches for issues with the `enhancement` label to
have the `dev` branch as a source and branches for issues with the `bug`
label to have the `staging` branch as a source, add this to your configuration
YAML:

```yaml
branches:
  - label: enhancement
    name: dev
  - label: bug
    name: staging
```

When issues have multiple labels the branch of the first match (based on the 
order in the configuration YAML will be used).

If a configured branch does not exist in the repository the "default branch"
is used.

## Branch name prefix based on issue label

Branch names can be prefixed based on the label of an issue.

For example, if you want branches for issues with the `enhancement` label to
have the `feature/` prefix and branches for issues with the `bug` label to 
have the `bugfix/` prefix, add this to your configuration YAML:

```yaml
branches:
  - label: enhancement
    prefix: feature/
  - label: bug
    prefix: bugfix/
```

You can use `${...}` placeholders in the prefix to substitute fields from the
GitHub issue assignment JSON object. For example, if you want the GitHub login name of the user that created
the issue in the branch prefix, add this to your configuration YAML:

```yaml
branches:
  - label: enhancement
    prefix: feature/${issue.user.login}/
```

Check 
[test/fixtures/issues.assigned.json](test/fixtures/issues.assigned.json) for
all possible placeholder names.

## Matching labels with wildcards

Wildcard characters '?' (matches any single character) and '*' (matches any sequence of characters, 
including the empty sequence) can be used in the label field.

For example, to set the default/fallback prefix `issues/` for issues that do not have the `enhancement`  or `bug`
label, use this configuration:

```yaml
branches:
  - label: enhancement
    prefix: feature/
  - label: bug
    prefix: bugfix/
  - label: '*'
    prefix: issues/
```

*Remember to put quotes around a single asterisk ('\*') in YAML*

# Feedback, suggestions and bug reports

Please create an issue here: https://github.com/robvanderleek/create-issue-branch/issues

# Contributing

If you have suggestions for how create-issue-branch could be improved, or want to report a bug, open an issue! We'd love all and any contributions.

For more, check out the [Contributing Guide](CONTRIBUTING.md).

# License

[ISC](LICENSE) © 2019 Rob van der Leek <robvanderleek@gmail.com> (https://twitter.com/robvanderleek)
