---
title: Npm-and-Yarn-Introduction
date: 2020-11-19 19:00
description: First week to learn how to use Npm and Yarn
categories:
 - 前端
---

## HighLight

- [Awesome npm](https://github.com/sindresorhus/awesome-npm)

## The Basics of npm

**About npm**

什么是 npm:
- the website
- the Command Line Interface (CLI)
- the registry

### Overview

版本管理: [nvm](https://github.com/nvm-sh/nvm)

**常用 npm CLI**

- npm init

  set up a new or existing npm package.
- npm install (aliases: npm i, npm add) 

  install a package, and any packages that it depends on.
- npm update (aliases: up, upgrade) 
  
  update all packages listed to the latest version
- npm uninstall (aliases: remove, rm, r, un, unlink) 

  uninstall a package, completely removing everything npm installed on its behalf.
- npm publish

  publish a package to the registry so that it can be installed by name.

- npm run-script (alias: npm run)

  run an arbitrary command from a package's "scripts" object.

- npm start

  run an arbitrary command specified in the package's "start" property of it "scripts" object.

- npm build

  this is the plumbing command called by npm link and npm install.

**npm config**

- -v: --version
- -g: --global
- -S: --save
- -h, -?, --help, -H: --usage

**[淘宝npm镜像](https://developer.aliyun.com/mirror/NPM?from=tnpm)**

```shell
npm config set registry http://registry.npm.taobao.org
npm config get registry
```

**package.json**

- name

  The name is what your thing is called.
- version

  The name and version together form an identifier
- description

  This helps people discover your package, as it's listed in npm search.
- keywords

  It's an array of strings. This helps people discover your package as it's listed in npm search.

- homepage

  The url to the project homepage.

- bugs

  The url to your project's issue tracker and / or the email address to which issues should be reported. 

- license

  You should specify a license for your package so that people know how they are permitted to use it, and any restrictions you're placing on it.

- people fields: author, contributors

  The "author" is one person. "contributors" is an array of people. A "person" is an object with a "name" field and optionally "url" and "email"

- repository

  Specify the place where your code lives.

- scripts

  The "scripts" property is a dictionary containing script commands that are run at various times in the lifecycle of your package. The key is the lifecycle event, and the value is the command to run at that point.

- dependencies

  Packages required by your application in production.
- devDependencies

  Packages that are only needed for local development and testing.

**[Semantic Versioning](https://semver.org/)**

MAJOR.MINOR.PATCH

- MAJOR version when you make incompatible API changes,
- MINOR version when you add functionality in a backwards compatible manner, and
- PATCH version when you make backwards compatible bug fixes.

1.  `version` Must match `version` exactly
2.  `>version` Must be greater than `version`
3.  `>=version` etc
4.  `<version`
5.  `<=version`
6.  `~version` "Approximately equivalent to version"  See [semver](https://docs.npmjs.com/cli/v6/using-npm/semver)
7.  `^version` "Compatible with version"  See [semver](https://docs.npmjs.com/cli/v6/using-npm/semver)
8.  `1.2.x` 1.2.0, 1.2.1, etc., but not 1.3.0
9.  `http://...` 'URLs as Dependencies'
10.  `*` Matches any version
11.  `""` (just an empty string) Same as `*`
12.  `version1 - version2` Same as `>=version1 <=version2`.
13.  `range1 || range2` Passes if either range1 or range2 are satisfied.
14.  `git...` 'Git URLs as Dependencies'
15.  `user/repo` 'GitHub URLs'
16.  `tag` A specific version tagged and published as `tag`  See [`npm dist-tag`](https://docs.npmjs.com/cli/v6/commands/npm-dist-tag)
17.  `path/path/path` Local Paths

**Script**

The "scripts" property of of your package.json file supports a number of built-in scripts and their preset life cycle events as well as arbitrary scripts.

Hook Scripts: 

Place an executable file at `node_modules/.hooks/{eventname}`, and
it'll get run for all packages when they are going through that point
in the package lifecycle for any packages installed in that root.

## The Basics of Yarn

- Fast
- Reliable
- Secure
- Reproducible

### Overview

**Usage**

Starting a new project

```shell
yarn init
```

Adding a dependency

```shell
yarn add [package]
yarn add [package]@[version]
yarn add [package]@[tag]
```

Adding a dependency to different categories of dependencies
Add to `devDependencies`, `peerDependencies`, and `optionalDependencies` respectively:

```shell
yarn add [package] --dev
yarn add [package] --peer
yarn add [package] --optional
```

Upgrading a dependency

```shell
yarn upgrade [package]
yarn upgrade [package]@[version]
yarn upgrade [package]@[tag]
```

Removing a dependency

```shell
yarn remove [package]
```

Installing all the dependencies of project

```shell
yarn
# or
yarn install
```

Run a defined package script

```shell
yarn run [script] [<args>]
```

## Comparison

https://www.sitepoint.com/yarn-vs-npm/

## References

- [npm Official Website](https://www.npmjs.com/)
- [yarn Offical Website](https://yarnpkg.com/)
