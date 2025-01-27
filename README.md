# Oyster Package Generator

[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)

Setting up a Unity package manager project with cloud builds, automated version numbers, and documentation can take days. Oyster Package Generator creates all of this for you by answering a few simple questions about your project. Save hundred of hours deploying and maintaining your Unity package by spending 15 minutes to setup and run Oyster.

![Oyster Package Generator CLI](docs/cli-example.png)

Features

* Cloud deploy your Unity Package automatically to NPM
* Automatically generates the standard Unity [package structure](https://docs.unity3d.com/Manual/cus-layout.html) for you
* Version numbers are automatically created from your commits
* Auto-deploying nightly builds as you make commits
* Change logs are generated from commits
* Allows you to work inside a Unity project without cloning nested repos in `Assets`
* Usable across platforms thanks to Node.js (Mac, Windows, Linux, ect.)

**Support**

Join the [Discord Community](https://discord.gg/8QHFfzn) if you have questions or need help.

# Getting Started

In order to use Oyster Package Generator you'll need the following.

* [Git](https://git-scm.com/) installed
* [Node.js](https://nodejs.org/en/) version [here](.nvmrc) installed
* [GitHub](https://github.com/) account (uses GitHub specific deployment features)
* [NPM](https://www.npmjs.com/) account (used to publish packages)

## Quick Start

If you just want a project generated run the following in a Unity repo.

```bash
npx oyster-package-generator init
```

If you want a step-by-step guide follow along below.

1. Create a new Unity project and navigate to the root
2. Setup Git (skip if already setup)
    1. Run `git init` to prep everything for Git
    2. Run `git remote add origin YOUR_REPO`. Replace `YOUR_REPO` with the proper repo URL (such as git@github.com:ashblue/oyster-package-generator.git). This needs to be done before oyster runs. Reason being it hard writes some Git addresses into your project
3. Generate the Oyster package
    1. Run `npx oyster-package-generator init` and answer the prompts. Wait for the install script to finish. If you notice a bug on Windows 10 [see here](#windows-10-troubleshooting)
    2. Create your first commit with `npm run commit`. Choose "chore" and write "My first commit" for the body text
    3. Run `git push` to deploy the `master` branch (follow on-screen instructions if prompted)
    4. Use `git checkout -b develop` and then run `git push` to deploy it
4. [Setup cloud deploys](#setting-up-cloud-builds) (optional)
5. Set your default GitHub branch to `develop` instead of `master` in your repo's settings. While not required, this will make pull requests and maintaining your repo easier

Once setup, all commits to the `master` branch will generate a new release. All commits to the `develop` branch will generate an unversioned nightly build.

## Running the CLI

To generate your project you'll need to setup a GitHub repo if you haven't in a Unity project. Make sure you set the `origin` remote as the origin is used to auto populate some of the files.

Run the following command and answer the question prompts.

```bash
npx oyster-package-generator init
```

Please note if you plan on using Oyster a lot (or on [Windows 10](#windows-10-troubleshooting)) you should globally install it. `npx` can be quite slow since it doesn't cache.

```bash
npm install -g oyster-package-generator

# Run the program
oyster init
```

You're done. If you want to [setup cloud builds manually](#setting-up-cloud-builds) you'll need to do one extra thing.

### Making commits to your project

All commits should be made with the following command. Your project will use [Commitizen](https://github.com/commitizen/cz-cli) and enforce syntax via [Commitlint](https://commitlint.js.org).

```bash
npm run commit
```

### Git Flow

You should install and use the [Git Flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) branching strategy when working with Oyster generated packages. 

Why do I need GitFlow you might ask? Commits to Oyster's `develop` branch automatically create nightly builds. Commits to `master` automatically generate package releases. Therefore it's a good idea to work out of Git Flow's feature branches and use the branching strategy.

In short **you must** have a `develop` and `master` branch for cloud builds to work properly.

### Windows 10 Troubleshooting

If you're on Windows 10 you may run into the username space bug in the filepath (example `C:/first last/ect/...`). This is a [known issue](https://github.com/zkat/npx/issues/146) with `npx`. You might just want to install the package globally with the following instead of trying to fix it.

```bash
npm install -g oyster-package-generator

# Run the program
oyster init
```

You may also want to consider installing Node.js in the root of your C drive to decrease any problems you might encounter. For example a new `C:/node` folder during Node's setup.

### Upgrading Older Projects

This package comes with a convenient feature to upgrade an older Oyster project to the latest version. Please consider the following before running the command.

* Make sure all changes are checked in, this will delete files
* After the process runs, check your Git diffs to make sure nothing was lost
* If you're upgrading from Travis CI to GitHub Actions you'll need to provide a new `NPM_TOKEN` in your repo's [secrets](https://docs.github.com/en/actions/reference/encrypted-secrets)
* If you do not have a `.oyster.json` file (older projects), generate one with the [generate-config](#generating-a-config) command

When ready, use this command to trigger the upgrade.

```bash
npx oyster-package-generator upgrade
```

#### Generating a config

If you're on Oyster v1.X or v2.0, you'll need to generate a config file before running the `upgrade` command. Configs are new as of version `v2.1.0`. Generate a config file by running the following.

```bash
npx oyster-package-generator generate-config
```

### GitHub Protected Branches

Due to a known bug with GitHub Action commits, it's not recommended to add special requirements to a `master` protected branch. You can still protect `master`, but special requirements will result in crashing the Semantic Release bot that auto deploys releases.

### Licensing

Oyster Package Generator automatically includes an MIT license in the project. You can easily change this by deleting/changing the `package.json` license key and the `LICENSE.md` file if you desire.

## Roadmap

To view the latest upcoming features you can check the roadmap here.

https://trello.com/b/Z9P0XMl6/oyster-package-generator

## Setting up cloud builds

To get builds automatically deploying you'll need an NPM token. To get one we'll have to [generate an authentication key](https://docs.npmjs.com/creating-and-viewing-authentication-tokens) and choose the "Automation" token type. You must have an [npm](https://www.npmjs.com) account to generate a token. 

Add the key to your repo [secrets](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository) as `NPM_TOKEN`.

## Development Environment

To run this project locally you'll need to clone this repo and run the following in your project root. [NVM](https://github.com/nvm-sh/nvm) is recommended to sync your local Node.js version. Or you can install the [.nvmrc](.nvmrc) file version directly from the [Node.js](https://nodejs.org/) site. Please note the version of Node.js you use is important.

```bash
# If using NVM
nvm use

npm install
npm run build
```

After the processes are complete, you'll need to setup the oyster command locally to test it. This is important since you probably want to execute the command in various Unity projects. We can easily do this by running the following.

```bash
npm link
```

When the link is complete you can run `oyster` in the terminal. Which will execute the last build created from `npm run build`.

If you ever want to remove the global `oyster` command just run the following in the project root. This will remove the command entirely and uninstall it.

```bash
npm unlink
```

### Auto Builds

While developing, you may want to automatically rebuild the app when changes are detected. To do so simply run the following command that will automatically listen for changes.

```bash
npm run dev
```

### Pull Requests / Contributing

Please see the [Contributing Guidelines](CONTRIBUTING.md) document for more info.

