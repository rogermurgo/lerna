# `@lerna/filter-options`

> Options for lerna sub-commands that need filtering

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Options

### `--scope <glob>`

Include only packages with names matching the given glob.

```sh
$ lerna exec --scope my-component -- ls -la
$ lerna run --scope toolbar-* test
$ lerna run --scope package-1 --scope *-2 lint
```

**Note:** For certain globs, it may be necessary to quote the option argument to avoid premature shell expansion.

### `--ignore <glob>`

Exclude packages with names matching the given glob.

```sh
$ lerna exec --ignore package-{1,2,5}  -- ls -la
$ lerna run --ignore package-1  test
$ lerna run --ignore package-@(1|2) --ignore package-3 lint
```

More examples of filtering can be found [here](https://github.com/lerna/lerna/blob/c0a750e0f482c16dda2f922f235861283efbe94d/commands/list/__tests__/list-command.test.js#L305-L356).

### `--no-private`

Exclude private packages. They are included by default.

### `--since [ref]`

Only include packages that have been updated since the specified `ref`. If no ref is passed, it defaults to the most-recent tag.

```sh
# List the contents of packages that have changed since the latest tag
$ lerna exec --since -- ls -la

# Run the tests for all packages that have changed since `master`
$ lerna run test --since master

# List all packages that have changed since `some-branch`
$ lerna ls --since some-branch
```

_This can be particularly useful when used in CI, if you can obtain the target branch a PR will be going into, because you can use that as the `ref` to the `--since` option. This works well for PRs going into master as well as feature branches._

### `--include-filtered-dependents`

Include all transitive dependents when running a command regardless of `--scope`, `--ignore`, or `--since`.

### `--include-filtered-dependencies`

Include all transitive dependencies when running a command regardless of `--scope`, `--ignore`, or `--since`.

Used in combination with any command that accepts `--scope` (`bootstrap`, `clean`, `ls`, `run`, `exec`).
Ensures that all dependencies (and dev dependencies) of any scoped packages (either through `--scope` or `--ignore`) are operated on as well.

> Note: This will override the `--scope` and `--ignore` flags.
>
> > i.e. A package matched by the `--ignore` flag will still be bootstrapped if it is depended on by another package that is being bootstrapped.

This is useful for situations where you want to "set up" a single package that relies on other packages being set up.

```sh
$ lerna bootstrap --scope my-component --include-filtered-dependencies
# my-component and all of its dependencies will be bootstrapped
```

```sh
$ lerna bootstrap --scope "package-*" --ignore "package-util-*" --include-filtered-dependencies
# all packages matching "package-util-*" will be ignored unless they are
# depended upon by a package whose name matches "package-*"
```
# `@lerna/validation-error`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/cli`

> Lerna's CLI

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/project`

> Lerna project configuration

## Configuration Resolution

Lerna's file-based configuration is located in `lerna.json` or the `lerna` property of `package.json`.
Wherever this configuration is found is considered the "root" of the lerna-managed multi-package repository.
A minimum-viable configuration only needs a `version` property; the following examples are equivalent:

```json
{
  "version": "1.2.3"
}
```

```json
{
  "name": "my-monorepo",
  "version": "0.0.0-root",
  "private": true,
  "lerna": {
    "version": "1.2.3"
  }
}
```

Any other properties on this configuration object will be used as defaults for CLI options of _all_ lerna subcommands. That is to say, CLI options _always_ override values found in configuration files (a standard practice for CLI applications).

### Command-Specific Configuration

To focus configuration on a particular subcommand, use the `command` subtree. Each subproperty of `command` corresponds to a lerna subcommand (`publish`, `create`, `run`, `exec`, etc).

```json
{
  "version": "1.2.3",
  "command": {
    "publish": {
      "loglevel": "verbose"
    }
  },
  "loglevel": "success"
}
```

In the example above, `lerna publish` will act as if `--loglevel verbose` was passed.
All other subcommands will receive the equivalent of `--loglevel success` (much much quieter).
# `@lerna/global-options`

> Global options applicable to _every_ lerna sub-command

## Options

### `--concurrency`

How many threads to use when Lerna parallelizes the tasks (defaults to count of logical CPU cores)

```sh
$ lerna publish --concurrency 1
```

### `--loglevel <silent|error|warn|success|info|verbose|silly>`

What level of logs to report. On failure, all logs are written to lerna-debug.log in the current working directory.

Any logs of a higher level than the setting are shown. The default is "info".

### `--max-buffer <bytes>`

Set a max buffer length for each underlying process call. Useful for example
when someone wants to import a repo with a larger amount of commits while
running `lerna import`. In that case the built-in buffer length might not
be sufficient.

### `--no-progress`

Disable progress bars. This is always the case in a CI environment.

### `--no-sort`

By default, all tasks execute on packages in topologically sorted order as to respect the dependency relationships of the packages in question. Cycles are broken on a best-effort basis in a way not guaranteed to be consistent across Lerna invocations.

Topological sorting can cause concurrency bottlenecks if there are a small number of packages with many dependents or if some packages take a disproportionately long time to execute. The `--no-sort` option disables sorting, instead executing tasks in an arbitrary order with maximum concurrency.

This option can also help if you run multiple "watch" commands. Since `lerna run` will execute commands in topologically sorted order, it can end up waiting for a command before moving on. This will block execution when you run "watch" commands, since they typically never end. An example of a "watch" command is [running `babel` with the `--watch` CLI flag](https://babeljs.io/docs/usage/cli/#babel-compile-files).

### `--reject-cycles`

Fail immediately if a cycle is found (in `bootstrap`, `exec`, `publish` or `run`).

```sh
$ lerna bootstrap --reject-cycles
```
# `@lerna/prompt`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
> A tool for managing JavaScript projects with multiple packages.

<h2 align="center">
  <img alt="Lerna" src="https://cloud.githubusercontent.com/assets/952783/15271604/6da94f96-1a06-11e6-8b04-dc3171f79a90.png" width="480">
</h2>

<p align="center">
  <a href="https://www.npmjs.com/package/lerna"><img alt="NPM Status" src="https://img.shields.io/npm/v/lerna.svg?style=flat"></a>
  <a href="https://travis-ci.org/lerna/lerna"><img alt="Travis Status" src="https://img.shields.io/travis/lerna/lerna/master.svg?style=flat&label=travis"></a>
</p>

## Usage

Check out our documentation [here](https://github.com/lerna/lerna#readme).

```sh
npm i -D lerna
```
# `@lerna/otplease`

> Prompt for OTP when wrapped Promise fails

## Usage

This is an internal lerna library, you probably shouldn't use it directly.# `@lerna/package-graph`

> Lerna's internal representation of a package graph

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/conventional-commits`

> Lerna's internal interface to conventional-changelog and friends

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/command`

> Lerna's internal base class for commands

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/package`

> Lerna's internal representation of a package

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/child-process`

> Lerna's internal child_process wrapper

## Usage

You probably shouldn't.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/log-packed`

> Log the result of npm pack --json

Extracted from the [npm source](https://github.com/npm/cli/blob/4f801d8a476f7ca52b0f182bf4e17a80db12b4e2/lib/pack.js#L175-L212).

## Usage

```js
const execa = require("execa");
const logPacked = require("@lerna/log-packed");

execa("npm", ["pack", "--json"]).then(result => {
  const tarballs = JSON.parse(result.stdout);
  tarballs.forEach(logPacked);
});
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/npm-dist-tag`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/query-graph`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/npm-publish`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/pack-directory`

> Pack a directory into an npm package tarball

This package owes an immense debt of gratitude to [Kat Marchán](https://github.com/zkat), [Rebecca Turner](https://github.com/iarna), and all past contributors to npm for making such awesome software that I basically copied.

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/listable`

> Shared logic for listing package information

This is an internal package for [Lerna](https://github.com/lerna/lerna/#readme), YMMV.

## Usage

### `listable.format()`

```js
const listable = require("@lerna/listable");

const { text, count } = listable.format(packages, options);
```

### `listable.options()`

```js
const listable = require("@lerna/listable");

exports.builder = yargs => {
  listable.options(yargs);
};
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/collect-updates`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/pulse-till-done`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.
This is a direct extraction of an [internal npm utility](https://github.com/npm/cli/blob/f6ebf5e/lib/utils/pulse-till-done.js).

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/symlink-binary`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/timer`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/gitlab-client`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/describe-ref`

> Parse [git describe][] output for lerna-related tags

## Usage

```js
const describe = require("@lerna/describe-ref");

(async () => {
  const { lastTagName, lastVersion, refCount, sha, isDirty } = await describe();
})();

// values listed here are their defaults
const options = {
  cwd: process.cwd(),
  // pass a glob to match tag name, e.g. "v*.*.*"
  match: undefined,
  // if true, omit --first-parent option
  includeMergedTags: false,
};

const { lastTagName, lastVersion, refCount, sha, isDirty } = describe.sync(options);

const result = describe.parse("v1.0.0-5-gdeadbeef");
// { lastTagName, lastVersion, refCount, sha, isDirty }
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

[git describe]: https://git-scm.com/docs/git-describe
# `@lerna/output`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/get-npm-exec-opts`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/create-symlink`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/run-topologically`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/run-parallel-batches`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/npm-conf`

> Vendored npm-conf with updates

## Usage

See [npm-conf](https://github.com/kevva/npm-conf#readme)

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/npm-run-script`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/filter-packages`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/rimraf-dir`

> Run rimraf on a directory in a subprocess to hack around slowness

## Usage

```js
const rimrafDir = require("@lerna/rimraf-dir");

rimrafDir("/path/to/directory").then(removedDir => {
  console.log("removed", removedDir);
});
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/check-working-tree`

> Check git working tree status and error appropriately

## Usage

```js
const checkWorkingTree = require("@lerna/check-working-tree");

// values listed here are their defaults
const options = {
  cwd: process.cwd(),
};

(async () => {
  try {
    await checkWorkingTree(options);
  } catch (err) {
    console.error(err.message);
    // "Working tree has uncommitted changes, ..."
  }
})();
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/resolve-symlink`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/get-packed`

> Read contents of package tarball created by npm pack

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/run-lifecycle`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/map-to-registry`

> Produce registry uri and auth of package name from npm config

## Usage

This is an extraction of an internal npm [utility](https://github.com/npm/npm/blob/f644018/lib/utils/map-to-registry.js). Caveat emptor.

```js
"use strict";

const mapToRegistry = require("@lerna/map-to-registry");
const npmConf = require("@lerna/npm-conf");

const config = npmConf();
const { uri, auth } = mapToRegistry("my-package", config);
```

`uri` and `auth` are suitable for arguments to [npm-registry-client](https://www.npmjs.com/package/npm-registry-client) instance method parameters.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/npm-install`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/github-client`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/write-log-file`

> What lerna uses to dump logs

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/collect-uncommitted`

> Check git working tree status and collect uncommitted changes for display

## Usage

```js
const collectUncommitted = require("@lerna/collect-uncommitted");

// values listed here are their defaults
const options = {
  cwd: process.cwd(),
  log: require("npmlog"),
};

(async () => {
  try {
    const results = await collectUncommitted(options);
    console.log(`Uncommitted changes on CWD ${options.cwd}: ${results.join("\n")}`);
  } catch (err) {
    console.error(err.message);
  }
})();
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/has-npm-version`

> Test if the current version of npm satisfies a given semver range

## Usage

```js
const hasNpmVersion = require("@lerna/has-npm-version");

// `npm --version` === 6.3.0
hasNpmVersion(">=6"); // => true

// `npm --version` === 5.6.0
hasNpmVersion(">=6"); // => false

// makePredicate() caches the call to `npm --version`
// useful if you're calling it repeatedly in a loop
const predicate = hasNpmVersion.makePredicate();

// `npm --version` === 6.3.0
hasNpmVersion(">=5"); // => true
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/batch-packages`

> An internal Lerna tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/symlink-dependencies`

> Lerna's internal symlinking tool

## Usage

You probably shouldn't, at least directly.

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
# `@lerna/prerelease-id-from-version`

> Get the prerelease ID from a version string

## Usage

```js
const prereleaseIdFromVersion = require("@lerna/prerelease-id-from-version");

prereleaseIdFromVersion(1.0.0-alpha.0); // => "alpha"

prereleaseIdFromVersion(1.0.0); // => undefined

prereleaseIdFromVersion(); // => undefined
```

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.
<p align="center">
  <img alt="Lerna" src="https://cloud.githubusercontent.com/assets/952783/15271604/6da94f96-1a06-11e6-8b04-dc3171f79a90.png" width="480">
</p>

<p align="center">
  A tool for managing JavaScript projects with multiple packages.
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/lerna"><img alt="NPM Status" src="https://img.shields.io/npm/v/lerna.svg?style=flat"></a>
  <a href="https://travis-ci.org/lerna/lerna"><img alt="Travis Status" src="https://img.shields.io/travis/lerna/lerna/master.svg?style=flat&label=travis"></a>
</p>

- [About](#about)
- [Getting Started](#getting-started)
- [How It Works](#how-it-works)
- [Troubleshooting](#troubleshooting)
- Commands
  - [`lerna publish`](./commands/publish#readme)
  - [`lerna version`](./commands/version#readme)
  - [`lerna bootstrap`](./commands/bootstrap#readme)
  - [`lerna list`](./commands/list#readme)
  - [`lerna changed`](./commands/changed#readme)
  - [`lerna diff`](./commands/diff#readme)
  - [`lerna exec`](./commands/exec#readme)
  - [`lerna run`](./commands/run#readme)
  - [`lerna init`](./commands/init#readme)
  - [`lerna add`](./commands/add#readme)
  - [`lerna clean`](./commands/clean#readme)
  - [`lerna import`](./commands/import#readme)
  - [`lerna link`](./commands/link#readme)
  - [`lerna create`](./commands/create#readme)
- [Concepts](#concepts)
- [Lerna.json](#lernajson)
- [Global Flags](./core/global-options)
- [Filter Flags](./core/filter-options)

## About

Splitting up large codebases into separate independently versioned packages
is extremely useful for code sharing. However, making changes across many
repositories is _messy_ and difficult to track, and testing across repositories
gets complicated really fast.

To solve these (and many other) problems, some projects will organize their
codebases into multi-package repositories (sometimes called [monorepos](https://github.com/babel/babel/blob/master/doc/design/monorepo.md)). Projects like [Babel](https://github.com/babel/babel/tree/master/packages), [React](https://github.com/facebook/react/tree/master/packages), [Angular](https://github.com/angular/angular/tree/master/modules),
[Ember](https://github.com/emberjs/ember.js/tree/master/packages), [Meteor](https://github.com/meteor/meteor/tree/devel/packages), [Jest](https://github.com/facebook/jest/tree/master/packages), and many others develop all of their packages within a
single repository.

**Lerna is a tool that optimizes the workflow around managing multi-package
repositories with git and npm.**

Lerna can also reduce the time and space requirements for numerous
copies of packages in development and build environments - normally a
downside of dividing a project into many separate NPM package. See the
[hoist documentation](doc/hoist.md) for details.

### What does a Lerna repo look like?

There's actually very little to it. You have a file structure that looks like this:

```
my-lerna-repo/
  package.json
  packages/
    package-1/
      package.json
    package-2/
      package.json
```

### What can Lerna do?

The two primary commands in Lerna are `lerna bootstrap` and `lerna publish`.

`bootstrap` will link dependencies in the repo together.
`publish` will help publish any updated packages.

### What can't Lerna do?

Lerna is not a deployment tool for serverless monorepos. Hoisting might be incompatible with traditional serverless monorepo deployment techniques.

## Getting Started

> The instructions below are for Lerna 3.x.
> We recommend using it instead of 2.x for a new Lerna project.

Let's start by installing Lerna as a dev dependency of your project with [npm](https://www.npmjs.com/).

```sh
$ mkdir lerna-repo && cd $_
$ npx lerna init
```

This will create a `lerna.json` configuration file as well as a `packages` folder, so your folder should now look like this:

```
lerna-repo/
  packages/
  package.json
  lerna.json
```

## How It Works

Lerna allows you to manage your project using one of two modes: Fixed or Independent.

### Fixed/Locked mode (default)

Fixed mode Lerna projects operate on a single version line. The version is kept in the `lerna.json` file at the root of your project under the `version` key. When you run `lerna publish`, if a module has been updated since the last time a release was made, it will be updated to the new version you're releasing. This means that you only publish a new version of a package when you need to.

This is the mode that [Babel](https://github.com/babel/babel) is currently using. Use this if you want to automatically tie all package versions together. One issue with this approach is that a major change in any package will result in all packages having a new major version.

### Independent mode

`lerna init --independent`

Independent mode Lerna projects allows maintainers to increment package versions independently of each other. Each time you publish, you will get a prompt for each package that has changed to specify if it's a patch, minor, major or custom change.

Independent mode allows you to more specifically update versions for each package and makes sense for a group of components. Combining this mode with something like [semantic-release](https://github.com/semantic-release/semantic-release) would make it less painful. (There is work on this already at [atlassian/lerna-semantic-release](https://github.com/atlassian/lerna-semantic-release)).

> Set the `version` key in `lerna.json` to `independent` to run in independent mode.

## Troubleshooting

If you encounter any issues while using Lerna please check out our [Troubleshooting](doc/troubleshooting.md)
document where you might find the answer to your problem.

## Frequently asked questions

See [FAQ.md](FAQ.md).

## Concepts

Lerna will log to a `lerna-debug.log` file (same as `npm-debug.log`) when it encounters an error running a command.

Lerna also has support for [scoped packages](https://docs.npmjs.com/misc/scope).

Run `lerna --help` to see all available commands and options.

### lerna.json

```json
{
  "version": "1.1.3",
  "npmClient": "npm",
  "command": {
    "publish": {
      "ignoreChanges": ["ignored-file", "*.md"],
      "message": "chore(release): publish"
    },
    "bootstrap": {
      "ignore": "component-*",
      "npmClientArgs": ["--no-package-lock"]
    }
  },
  "packages": ["packages/*"]
}
```

- `version`: the current version of the repository.
- `npmClient`: an option to specify a specific client to run commands with (this can also be specified on a per command basis). Change to `"yarn"` to run all commands with yarn. Defaults to "npm".
- `command.publish.ignoreChanges`: an array of globs that won't be included in `lerna changed/publish`. Use this to prevent publishing a new version unnecessarily for changes, such as fixing a `README.md` typo.
- `command.publish.message`: a custom commit message when performing version updates for publication. See [@lerna/version](commands/version#--message-msg) for more details. 
- `command.bootstrap.ignore`: an array of globs that won't be bootstrapped when running the `lerna bootstrap` command.
- `command.bootstrap.npmClientArgs`: array of strings that will be passed as arguments directly to `npm install` during the `lerna bootstrap` command.
- `command.bootstrap.scope`: an array of globs that restricts which packages will be bootstrapped when running the `lerna bootstrap` command.
- `packages`: Array of globs to use as package locations.

The packages config in `lerna.json` is a list of globs that match directories containing a `package.json`, which is how lerna recognizes "leaf" packages (vs the "root" `package.json`, which is intended to manage the dev dependencies and scripts for the entire repo).

By default, lerna initializes the packages list as `["packages/*"]`, but you can also use another directory such as `["modules/*"]`, or `["package1", "package2"]`. The globs defined are relative to the directory that `lerna.json` lives in, which is usually the repository root. The only restriction is that you can't directly nest package locations, but this is a restriction shared by "normal" npm packages as well.

For example, `["packages/*", "src/**"]` matches this tree:

```
packages/
├── foo-pkg
│   └── package.json
├── bar-pkg
│   └── package.json
├── baz-pkg
│   └── package.json
└── qux-pkg
    └── package.json
src/
├── admin
│   ├── my-app
│   │   └── package.json
│   ├── stuff
│   │   └── package.json
│   └── things
│       └── package.json
├── profile
│   └── more-things
│       └── package.json
├── property
│   ├── more-stuff
│   │   └── package.json
│   └── other-things
│       └── package.json
└── upload
    └── other-stuff
        └── package.json
```

Locating leaf packages under `packages/*` is considered a "best-practice", but is not a requirement for using Lerna.

#### Legacy Fields

Some `lerna.json` fields are no longer in use. Those of note include:
* `lerna`: originally used to indicate the current version of Lerna. [Made obsolete](https://github.com/lerna/lerna/pull/1122) and [removed](https://github.com/lerna/lerna/pull/1225) in v3

### Common `devDependencies`

Most `devDependencies` can be pulled up to the root of a Lerna repo with `lerna link convert`

The above command will automatically hoist things and use relative `file:` specifiers.

Hoisting has a few benefits:
- All packages use the same version of a given dependency
- Can keep dependencies at the root up-to-date with an automated tool such as [GreenKeeper](https://greenkeeper.io/)
- Dependency installation time is reduced
- Less storage is needed

Note that `devDependencies` providing "binary" executables that are used by
npm scripts still need to be installed directly in each package where they're
used.

For example the `nsp` dependency is necessary in this case for `lerna run nsp`
(and `npm run nsp` within the package's directory) to work correctly:

```json
{
  "scripts": {
    "nsp": "nsp"
  },
  "devDependencies": {
    "nsp": "^2.3.3"
  }
}
```

### Git Hosted Dependencies

Lerna allows target versions of local dependent packages to be written as a [git remote url](https://docs.npmjs.com/cli/install) with a `committish` (e.g., `#v1.0.0` or `#semver:^1.0.0`) instead of the normal numeric version range.
This allows packages to be distributed via git repositories when packages must be private and a [private npm registry is not desired](https://www.dotconferences.com/2016/05/fabien-potencier-monolithic-repositories-vs-many-repositories).

Please note that lerna does _not_ perform the actual splitting of git history into the separate read-only repositories. This is the responsibility of the user. (See [this comment](https://github.com/lerna/lerna/pull/1033#issuecomment-335894690) for implementation details)

```
// packages/pkg-1/package.json
{
  name: "pkg-1",
  version: "1.0.0",
  dependencies: {
    "pkg-2": "github:example-user/pkg-2#v1.0.0"
  }
}

// packages/pkg-2/package.json
{
  name: "pkg-2",
  version: "1.0.0"
}
```

In the example above,

- `lerna bootstrap` will properly symlink `pkg-2` into `pkg-1`.
- `lerna publish` will update the committish (`#v1.0.0`) in `pkg-1` when `pkg-2` changes.

### README Badge

Using Lerna? Add a README badge to show it off: [![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)

```
[![lerna](https://img.shields.io/badge/maintained%20with-lerna-cc00ff.svg)](https://lerna.js.org/)
```

### Wizard

If you prefer some guidance for cli (in case you're about to start using lerna or introducing it to a new team), you might like [lerna-wizard](https://github.com/szarouski/lerna-wizard). It will lead you through a series of well-defined steps:

![lerna-wizard demo image](https://raw.githubusercontent.com/szarouski/lerna-wizard/2e269fb5a3af7100397a1f874cea3fa78089486e/demo.png)
# `@lerna/init`

> Create a new Lerna repo or upgrade an existing repo to the current version of Lerna

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna init
```

Create a new Lerna repo or upgrade an existing repo to the current version of Lerna.

> Lerna assumes the repo has already been initialized with `git init`.

When run, this command will:

1. Add `lerna` as a [`devDependency`](https://docs.npmjs.com/files/package.json#devdependencies) in `package.json` if it doesn't already exist.
2. Create a `lerna.json` config file to store the `version` number.

Example output on a new git repo:

```sh
$ lerna init
lerna info version v2.0.0
lerna info Updating package.json
lerna info Creating lerna.json
lerna success Initialized Lerna files
```

## Options

### `--independent`

```sh
$ lerna init --independent
```

This flag tells Lerna to use independent versioning mode.

### `--exact`

```sh
$ lerna init --exact
```

By default, `lerna init` will use a caret range when adding or updating
the local version of `lerna`, just like `npm install --save-dev lerna`.

To retain the `lerna` 1.x behavior of "exact" comparison, pass this flag.
It will configure `lerna.json` to enforce exact match for all subsequent executions.

```json
{
  "command": {
    "init": {
      "exact": true
    }
  },
  "version": "0.0.0"
}
```
# `@lerna/bootstrap`

> Link local packages together and install remaining package dependencies

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna bootstrap
```

Bootstrap the packages in the current Lerna repo.
Installs all of their dependencies and links any cross-dependencies.

When run, this command will:

1. `npm install` all external dependencies of each package.
2. Symlink together all Lerna `packages` that are dependencies of each other.
3. `npm run prepublish` in all bootstrapped packages (unless `--ignore-prepublish` is passed).
4. `npm run prepare` in all bootstrapped packages.

`lerna bootstrap` respects the `--ignore`, `--scope` and `--include-filtered-dependencies` flags (see [Filter Flags](https://www.npmjs.com/package/@lerna/filter-options)).

Pass extra arguments to npm client by placing them after `--`:

```sh
$ lerna bootstrap -- --production --no-optional
```

May also be configured in `lerna.json`:

```js
{
  ...
  "npmClient": "yarn",
  "npmClientArgs": ["--production", "--no-optional"]
}
```

### --hoist [glob]

Install external dependencies matching `glob` at the repo root so they're
available to all packages. Any binaries from these dependencies will be
linked into dependent package `node_modules/.bin/` directories so they're
available for npm scripts. If the option is present but no `glob` is given
the default is `**` (hoist everything). This option only affects the
`bootstrap` command.

```sh
$ lerna bootstrap --hoist
```

For background on `--hoist`, see the [hoist documentation](https://github.com/lerna/lerna/blob/master/doc/hoist.md).

Note: If packages depend on different _versions_ of an external dependency,
the most commonly used version will be hoisted, and a warning will be emitted.

### --strict

When used in conjunction with hoist will throw an error and stop bootstrapping after emitting the version warnings. Has no effect if you aren't hoisting, or if there are no version warnings.

```sh
$ lerna bootstrap --hoist --strict
```

### --nohoist [glob]

Do _not_ install external dependencies matching `glob` at the repo root. This
can be used to opt out of hoisting for certain dependencies.

```sh
$ lerna bootstrap --hoist --nohoist=babel-*
```

### --ignore

```sh
$ lerna bootstrap --ignore component-*
```

The `--ignore` flag, when used with the `bootstrap` command, can also be set in `lerna.json` under the `command.bootstrap.ignore` key. The command-line flag will take precedence over this option.

**Example**

```json
{
  "version": "0.0.0",
  "command": {
    "bootstrap": {
      "ignore": "component-*"
    }
  }
}
```

> Hint: The glob is matched against the package name defined in `package.json`,
> not the directory name the package lives in.

## Options

### `--ignore-prepublish`

Skip prepublish lifecycle scripts run by default in bootstrapped packages.
Note, this lifecycle is [deprecated](https://docs.npmjs.com/misc/scripts#deprecation-note),
and will likely be removed in the next major version of Lerna.

```sh
$ lerna bootstrap --ignore-prepublish
```

### `--ignore-scripts`

Skip any lifecycle scripts normally run (`prepare`, etc) in bootstrapped packages.

```sh
$ lerna bootstrap --ignore-scripts
```

### `--registry <url>`

When run with this flag, forwarded npm commands will use the specified registry for your package(s).

This is useful if you do not want to explicitly set up your registry
configuration in all of your package.json files individually when e.g. using
private registries.

### `--npm-client <client>`

Must be an executable that knows how to install npm package dependencies.
The default `--npm-client` is `npm`.

```sh
$ lerna bootstrap --npm-client=yarn
```

May also be configured in `lerna.json`:

```js
{
  ...
  "npmClient": "yarn"
}
```

### `--use-workspaces`

Enables integration with [Yarn Workspaces](https://github.com/yarnpkg/rfcs/blob/master/implemented/0000-workspaces-install-phase-1.md) (available since yarn@0.27+).
The values in the array are the commands in which Lerna will delegate operation to Yarn (currently only bootstrapping).
If `--use-workspaces` is true then `packages` will be overridden by the value from `package.json/workspaces.`
May also be configured in `lerna.json`:

```js
{
  ...
  "npmClient": "yarn",
  "useWorkspaces": true
}
```

The root-level package.json must also include a `workspaces` array:

```json
{
  "private": true,
  "devDependencies": {
    "lerna": "^2.2.0"
  },
  "workspaces": ["packages/*"]
}
```

This list is broadly similar to lerna's `packages` config (a list of globs matching directories with a package.json),
except it does not support recursive globs (`"**"`, a.k.a. "globstars").

### `--no-ci`

When using the default `--npm-client`, `lerna bootstrap` will call [`npm ci`](https://docs.npmjs.com/cli/ci) instead of `npm install` in CI environments.
To disable this behavior, pass `--no-ci`:

```sh
$ lerna bootstrap --no-ci
```

To _force_ it during a local install (where it is not automatically enabled), pass `--ci`:

```sh
$ lerna bootstrap --ci
```

This can be useful for "clean" re-installs, or initial installations after fresh cloning.

### `--force-local`

```sh
$ lerna bootstrap --force-local
```

When passed, this flag causes the `bootstrap` command to always symlink local dependencies regardless of matching version range.

## How It Works

Let's use `babel` as an example.

- `babel-generator` and `source-map` (among others) are dependencies of `babel-core`.
- `babel-core`'s [`package.json`](https://github.com/babel/babel/blob/13c961d29d76ccd38b1fc61333a874072e9a8d6a/packages/babel-core/package.json#L28-L47) lists both these packages as keys in `dependencies`, as shown below.

```js
// babel-core package.json
{
  "name": "babel-core",
  ...
  "dependencies": {
    ...
    "babel-generator": "^6.9.0",
    ...
    "source-map": "^0.5.0"
  }
}
```

- Lerna checks if each dependency is also part of the Lerna repo.
  - In this example, `babel-generator` can be an internal dependency, while `source-map` is always an external dependency.
  - The version of `babel-generator` in the `package.json` of `babel-core` is satisfied by `packages/babel-generator`, passing for an internal dependency.
  - `source-map` is `npm install`ed (or `yarn`ed) like normal.
- `packages/babel-core/node_modules/babel-generator` symlinks to `packages/babel-generator`
- This allows nested directory imports

## Notes

- When a dependency version in a package is not satisfied by a package of the same name in the repo, it will be `npm install`ed (or `yarn`ed) like normal.
- Dist-tags, like `latest`, do not satisfy [semver](https://semver.npmjs.com/) ranges.
- Circular dependencies result in circular symlinks which _may_ impact your editor/IDE.

[Webstorm](https://www.jetbrains.com/webstorm/) locks up when circular symlinks are present. To prevent this, add `node_modules` to the list of ignored files and folders in `Preferences | Editor | File Types | Ignored files and folders`.
# `@lerna/diff`

> Diff all packages or a single package since the last release

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna diff [package]

$ lerna diff
# diff a specific package
$ lerna diff package-name
```

Diff all packages or a single package since the last release.

> Similar to `lerna changed`. This command runs `git diff`.
# `@lerna/clean`

> Remove the node_modules directory from all packages

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna clean
```

Remove the `node_modules` directory from all packages.

`lerna clean` respects the `--ignore`, `--scope`, and `--yes` flags (see [Filter Flags](https://www.npmjs.com/package/@lerna/filter-options)).

> `lerna clean` does not remove modules from the root `node_modules` directory, even if you have the `--hoist` option enabled.
# `@lerna/publish`

> Publish packages in the current project

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
lerna publish              # publish packages that have changed since the last release
lerna publish from-git     # explicitly publish packages tagged in the current commit
lerna publish from-package # explicitly publish packages where the latest version is not present in the registry
```

When run, this command does one of the following things:

- Publish packages updated since the last release (calling [`lerna version`](https://github.com/lerna/lerna/tree/master/commands/version#readme) behind the scenes).
  - This is the legacy behavior of lerna 2.x
- Publish packages tagged in the current commit (`from-git`).
- Publish packages in the latest commit where the version is not present in the registry (`from-package`).
- Publish an unversioned "canary" release of packages (and their dependents) updated in the previous commit.

> Lerna will never publish packages which are marked as private (`"private": true` in the `package.json`).

Check out [Per-Package Configuration](#per-package-configuration) for more details about publishing scoped packages, custom registries, and custom dist-tags.

## Positionals

### bump `from-git`

In addition to the semver keywords supported by [`lerna version`](https://github.com/lerna/lerna/tree/master/commands/version#positionals),
`lerna publish` also supports the `from-git` keyword.
This will identify packages tagged by `lerna version` and publish them to npm.
This is useful in CI scenarios where you wish to manually increment versions,
but have the package contents themselves consistently published by an automated process.

### bump `from-package`

Similar to the `from-git` keyword except the list of packages to publish is determined by inspecting each `package.json`
and determining if any package version is not present in the registry. Any versions not present in the registry will
be published.
This is useful when a previous `lerna publish` failed to publish all packages to the registry.

## Options

`lerna publish` supports all of the options provided by [`lerna version`](https://github.com/lerna/lerna/tree/master/commands/version#options) in addition to the following:

- [`--canary`](#--canary)
- [`--contents <dir>`](#--contents-dir)
- [`--dist-tag <tag>`](#--dist-tag-tag)
- [`--git-head <sha>`](#--git-head-sha)
- [`--graph-type <all|dependencies>`](#--graph-type-alldependencies)
- [`--no-git-reset`](#--no-git-reset)
- [`--no-verify-access`](#--no-verify-access)
- [`--otp`](#--otp)
- [`--preid`](#--preid)
- [`--pre-dist-tag <tag>`](#--pre-dist-tag-tag)
- [`--registry <url>`](#--registry-url)
- [`--temp-tag`](#--temp-tag)
- [`--ignore-scripts`](#--ignore-scripts)
- [`--ignore-prepublish`](#--ignore-prepublish)
- [`--yes`](#--yes)
- [`--tag-version-prefix`](#--tag-version-prefix)

### `--canary`

```sh
lerna publish --canary
# 1.0.0 => 1.0.1-alpha.0+${SHA} of packages changed since the previous commit
# a subsequent canary publish will yield 1.0.1-alpha.1+${SHA}, etc

lerna publish --canary --preid beta
# 1.0.0 => 1.0.1-beta.0+${SHA}

# The following are equivalent:
lerna publish --canary minor
lerna publish --canary preminor
# 1.0.0 => 1.1.0-alpha.0+${SHA}
```

When run with this flag, `lerna publish` publishes packages in a more granular way (per commit).
Before publishing to npm, it creates the new `version` tag by taking the current `version`, bumping it to the next _minor_ version, adding the provided meta suffix (defaults to `alpha`) and appending the current git sha (ex: `1.0.0` becomes `1.1.0-alpha.0+81e3b443`).

If you have publish canary releases from multiple active development branches in CI,
it is recommended to customize the [`--preid`](#--preid) and [`--dist-tag <tag>`](#--dist-tag-tag) on a per-branch basis to avoid clashing versions.

> The intended use case for this flag is a per commit level release or nightly release.

### `--contents <dir>`

Subdirectory to publish. Must apply to ALL packages, and MUST contain a package.json file.
Package lifecycles will still be run in the original leaf directory.
You should probably use one of those lifecycles (`prepare`, `prepublishOnly`, or `prepack`) to _create_ the subdirectory and whatnot.

If you're into unnecessarily complicated publishing, this will give you joy.

```sh
lerna publish --contents dist
# publish the "dist" subfolder of every Lerna-managed leaf package
```

**NOTE:** You should wait until the `postpublish` lifecycle phase (root or leaf) to clean up this generated subdirectory,
as the generated package.json is used during package upload (_after_ `postpack`).

### `--dist-tag <tag>`

```sh
lerna publish --dist-tag next
```

When run with this flag, `lerna publish` will publish to npm with the given npm [dist-tag](https://docs.npmjs.com/cli/dist-tag) (defaults to `latest`).

This option can be used to publish a [`prerelease`](http://carrot.is/coding/npm_prerelease) or `beta` version under a non-`latest` dist-tag, helping consumers avoid automatically upgrading to prerelease-quality code.

> Note: the `latest` tag is the one that is used when a user runs `npm install my-package`.
> To install a different tag, a user can run `npm install my-package@prerelease`.

### `--git-head <sha>`

Explicit SHA to set as [`gitHead`](https://git.io/fh7np) on manifests when packing tarballs, only allowed with [`from-package`](#bump-from-package) positional.

For example, when publishing from AWS CodeBuild (where `git` is not available),
you could use this option to pass the appropriate [environment variable](https://docs.aws.amazon.com/codebuild/latest/userguide/build-env-ref-env-vars.html) to use for this package metadata:

```sh
lerna publish from-package --git-head ${CODEBUILD_RESOLVED_SOURCE_VERSION}
```

Under all other circumstances, this value is derived from a local `git` command.

### `--graph-type <all|dependencies>`

Set which kind of dependencies to use when building a package graph. The default value is `dependencies`, whereby only packages listed in the `dependencies` section of a package's `package.json` are included. Pass `all` to include both `dependencies` _and_ `devDependencies` when constructing the package graph and determining topological order.

When using traditional peer + dev dependency pairs, this option should be configured to `all` so the peers are always published before their dependents.

```sh
lerna publish --graph-type all
```

Configured via `lerna.json`:

```json
{
  "command": {
    "publish": {
      "graphType": "all"
    }
  }
}
```

### `--no-git-reset`

By default, `lerna publish` ensures any changes to the working tree have been reset.

To avoid this, pass `--no-git-reset`. This can be especially useful when used as part of a CI pipeline in conjunction with the `--canary` flag. For instance, the `package.json` version numbers which have been bumped may need to be used in subsequent CI pipeline steps (such as Docker builds).

```sh
lerna publish --no-git-reset
```

### `--no-verify-access`

By default, `lerna` will verify the logged-in npm user's access to the packages about to be published. Passing this flag will disable that check.

If you are using a third-party registry that does not support `npm access ls-packages`, you will need to pass this flag (or set `command.publish.verifyAccess` to `false` in lerna.json).

> Please use with caution

### `--otp`

When publishing packages that require two-factor authentication, you can specify a [one-time password](https://docs.npmjs.com/about-two-factor-authentication) using `--otp`:

```sh
lerna publish --otp 123456
```

> Please keep in mind that one-time passwords expire within 30 seconds of their generation. If it expires during publish operations, a prompt will request a refreshed value before continuing.

### `--preid`

Unlike the `lerna version` option of the same name, this option only applies to [`--canary`](#--canary) version calculation.

```sh
lerna publish --canary
# uses the next semantic prerelease version, e.g.
# 1.0.0 => 1.0.1-alpha.0

lerna publish --canary --preid next
# uses the next semantic prerelease version with a specific prerelease identifier, e.g.
# 1.0.0 => 1.0.1-next.0
```

When run with this flag, `lerna publish --canary` will increment `premajor`, `preminor`, `prepatch`, or `prerelease` semver
bumps using the specified [prerelease identifier](http://semver.org/#spec-item-9).

### `--pre-dist-tag <tag>`

```sh
lerna publish --pre-dist-tag next
```

Works the same as [`--dist-tag`](#--dist-tag-tag), except only applies to packages being released with a prerelease version.

### `--registry <url>`

When run with this flag, forwarded npm commands will use the specified registry for your package(s).

This is useful if you do not want to explicitly set up your registry
configuration in all of your package.json files individually when e.g. using
private registries.

### `--temp-tag`

When passed, this flag will alter the default publish process by first publishing
all changed packages to a temporary dist-tag (`lerna-temp`) and then moving the
new version(s) to the dist-tag configured by [`--dist-tag`](#--dist-tag-tag) (default `latest`).

This is not generally necessary, as Lerna will publish packages in topological
order (all dependencies before dependents) by default.

### `--ignore-scripts`

When passed, this flag will disable running [lifecycle scripts](#lifecycle-events) during `lerna publish`.

### `--ignore-prepublish`

When passed, this flag will disable [`prepublish`](#lifecycle-events) script being executed.

### `--yes`

```sh
lerna publish --canary --yes
# skips `Are you sure you want to publish the above changes?`
```

When run with this flag, `lerna publish` will skip all confirmation prompts.
Useful in [Continuous integration (CI)](https://en.wikipedia.org/wiki/Continuous_integration) to automatically answer the publish confirmation prompt.

### `---tag-version-prefix`

This option allows to provide custom prefix instead of the default one: `v`.

Keep in mind that currently you have to supply it twice: for `version` command and for `publish` command:

```bash
# locally
lerna version --tag-version-prefix=''
# on ci
lerna publish from-git --tag-version-prefix=''
```

## Deprecated Options

### `--skip-npm`

Call [`lerna version`](https://github.com/lerna/lerna/tree/master/commands/version#readme) directly, instead.

## Per-Package Configuration

A leaf package can be configured with special [`publishConfig`](https://docs.npmjs.com/files/package.json#publishconfig) that in _certain_ circumstances changes the behavior of `lerna publish`.

### `publishConfig.access`

To publish packages with a scope (e.g., `@mycompany/rocks`), you must set [`access`](https://docs.npmjs.com/misc/config#access):

```json
  "publishConfig": {
    "access": "public"
  }
```

- If this field is set for a package _without_ a scope, it **will** fail.
- If you _want_ your scoped package to remain private (i.e., `"restricted"`), there is no need to set this value.

  Note that this is **not** the same as setting `"private": true` in a leaf package; if the `private` field is set, that package will _never_ be published under any circumstances.

### `publishConfig.registry`

You can customize the registry on a per-package basis by setting [`registry`](https://docs.npmjs.com/misc/config#registry):

```json
  "publishConfig": {
    "registry": "http://my-awesome-registry.com/"
  }
```

- Passing [`--registry`](#--registry-url) applies globally, and in some cases isn't what you want.

### `publishConfig.tag`

You can customize the dist-tag on a per-package basis by setting [`tag`](https://docs.npmjs.com/misc/config#tag):

```json
  "publishConfig": {
    "tag": "flippin-sweet"
  }
```

- Passing [`--dist-tag`](#--dist-tag-tag) will _overwrite_ this value.
- This value is _always_ ignored when [`--canary`](#--canary) is passed.

### `publishConfig.directory`

This _non-standard_ field allows you to customize the published subdirectory just like [`--contents`](#--contents-dir), but on a per-package basis. All other caveats of `--contents` still apply.

```json
  "publishConfig": {
    "directory": "dist"
  }
```

## LifeCycle Events

Lerna will run [npm lifecycle scripts](https://docs.npmjs.com/misc/scripts#description) during `lerna publish` in the following order:

### Pre Publish

- In root package:

  - `prepublish`
  - `prepare`
  - `prepublishOnly`
  - `prepack`

- In each subpackage:
  - `prepublish`
  - `prepare`
  - `prepublishOnly`
  - `prepack`

### Packing each subpackage

- In each subpackage:
  - `postpack`

### After all subpackages packed

- In root package:
  - `postpack`

### Publishing each subpackage

- In each subpackage:
  - `publish`
  - `postpublish`

### After all subpackages published

- In root package:
  - `publish`
  - `postpublish`
# `@lerna/add`

> Add a dependency to matched packages

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna add <package>[@version] [--dev] [--exact]
```

Add local or remote `package` as dependency to packages in the current Lerna repo. Note that only a single package can be added at a time compared to `yarn add` or `npm install`.

When run, this command will:

1. Add `package` to each applicable package. Applicable are packages that are not `package` and are in scope
2. Bootstrap packages with changes to their manifest file (`package.json`)

If no `version` specifier is provided, it defaults to the `latest` dist-tag, just like `npm install`.

## Options

`lerna add` respects the `--ignore`, `--scope` and `--include-filtered-dependencies` flags (see [Filter Flags](https://www.npmjs.com/package/@lerna/filter-options)).

### `--dev`

Add the new package to `devDependencies` instead of `dependencies`.

### --exact

```sh
$ lerna add --exact
```

Add the new package with an exact version (e.g., `1.0.1`) rather than the default `^` semver range (e.g., `^1.0.1`).

### `--registry <url>`

Use a custom registry to install the targeted package.

### `--no-bootstrap`

Skip the chained `lerna bootstrap`.

## Examples

```sh
# Adds the module-1 package to the packages in the 'prefix-' prefixed folders
lerna add module-1 packages/prefix-*

# Install module-1 to module-2
lerna add module-1 --scope=module-2

# Install module-1 to module-2 in devDependencies
lerna add module-1 --scope=module-2 --dev

# Install module-1 in all modules except module-1
lerna add module-1

# Install babel-core in all modules
lerna add babel-core
```
# `@lerna/link`

> Symlink together all packages that are dependencies of each other

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna link
```

Symlink together all Lerna `packages` that are dependencies of each other in the current Lerna repo.

## Options

### `--force-local`

```sh
$ lerna link --force-local
```

When passed, this flag causes the `link` command to always symlink local dependencies regardless of matching version range.
# `@lerna/version`

> Bump version of packages changed since the last release

## Usage

```sh
lerna version 1.0.1 # explicit
lerna version patch # semver keyword
lerna version       # select from prompt(s)
```

When run, this command does the following:

1. Identifies packages that have been updated since the previous tagged release.
2. Prompts for a new version.
3. Modifies package metadata to reflect new release.
4. Commits those changes and tags the commit.
5. Pushes to the git remote.

## Positionals

### semver `bump`

```sh
lerna version [major | minor | patch | premajor | preminor | prepatch | prerelease]
# uses the next semantic version(s) value and this skips `Select a new version for...` prompt
```

When this positional parameter is passed, `lerna version` will skip the version selection prompt and [increment](https://github.com/npm/node-semver#functions) the version by that keyword.
You must still use the `--yes` flag to avoid all prompts.

## Prerelease

If you have any packages with a prerelease version number (e.g. `2.0.0-beta.3`) and you run `lerna version` with and a non-prerelease bump (`major`, `minor`, or `patch`), it will publish those previously pre-released packages _as well as_ the packages that have changed since the last release.

For projects using conventional commits, use the following flags for prerelease management:
**[`--conventional-prerelease`](#--conventional-prerelease):** release current changes as prerelease versions.
**[`--conventional-graduate`](#--conventional-graduate):** graduate prerelease versioned packages to stable versions.

Running `lerna version --conventional-commits` without the above flags will release current changes as prerelease only if the version is already in prerelease.

## Options

- [`--allow-branch`](#--allow-branch-glob)
- [`--amend`](#--amend)
- [`--conventional-commits`](#--conventional-commits)
- [`--conventional-graduate`](#--conventional-graduate)
- [`--conventional-prerelease`](#--conventional-prerelease)
- [`--changelog-preset`](#--changelog-preset)
- [`--exact`](#--exact)
- [`--force-publish`](#--force-publish)
- [`--git-remote`](#--git-remote-name)
- [`--create-release`](#--create-release-type)
- [`--ignore-changes`](#--ignore-changes)
- [`--include-merged-tags`](#--include-merged-tags)
- [`--message`](#--message-msg)
- [`--no-changelog`](#--no-changelog)
- [`--no-commit-hooks`](#--no-commit-hooks)
- [`--no-git-tag-version`](#--no-git-tag-version)
- [`--no-push`](#--no-push)
- [`--preid`](#--preid)
- [`--sign-git-commit`](#--sign-git-commit)
- [`--sign-git-tag`](#--sign-git-tag)
- [`--yes`](#--yes)
- [`--tag-version-prefix`](#--tag-version-prefix)

### `--allow-branch <glob>`

A whitelist of globs that match git branches where `lerna version` is enabled.
It is easiest (and recommended) to configure in `lerna.json`, but it is possible to pass as a CLI option as well.

```json
{
  "command": {
    "version": {
      "allowBranch": "master"
    }
  }
}
```

With the configuration above, the `lerna version` will fail when run from any branch other than `master`.
It is considered a best-practice to limit `lerna version` to the primary branch alone.

```json
{
  "command": {
    "version": {
      "allowBranch": ["master", "feature/*"]
    }
  }
}
```

With the preceding configuration, `lerna version` will be allowed in any branch prefixed with `feature/`.
Please be aware that generating git tags in feature branches is fraught with potential errors as the branches are merged into the primary branch. If the tags are "detached" from their original context (perhaps through a squash merge or a conflicted merge commit), future `lerna version` executions will have difficulty determining the correct "diff since last release."

It is always possible to override this "durable" config on the command-line.
Please use with caution.

```sh
lerna version --allow-branch hotfix/oops-fix-the-thing
```

### `--amend`

```sh
lerna version --amend
# commit message is retained, and `git push` is skipped.
```

When run with this flag, `lerna version` will perform all changes on the current commit, instead of adding a new one.
This is useful during [Continuous integration (CI)](https://en.wikipedia.org/wiki/Continuous_integration) to reduce the number of commits in the project's history.

In order to prevent unintended overwrites, this command will skip `git push` (i.e., it implies `--no-push`).

### `--conventional-commits`

```sh
lerna version --conventional-commits
```

When run with this flag, `lerna version` will use the [Conventional Commits Specification](https://conventionalcommits.org/) to [determine the version bump](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-recommended-bump) and [generate CHANGELOG.md files](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-cli).

Passing [`--no-changelog`](#--no-changelog) will disable the generation (or updating) of `CHANGELOG.md` files.

### `--conventional-graduate`

```sh
lerna version --conventional-commits --conventional-graduate=package-2,package-4

# force all prerelease packages to be graduated
lerna version --conventional-commits --conventional-graduate
```
When run with this flag, `lerna version` will graduate the specified packages (comma-separated) or all packages using `*`. This command works regardless of whether the current HEAD has been released, similar to `--force-publish`, except that any non-prerelease packages are ignored. If changes are present for packages that are not specified (if specifying packages), or for packages that are not in prerelease, those packages will be versioned as they normally would using `--conventional-commits`.

"Graduating" a package means bumping to the non-prerelease variant of a prerelease version, eg. `package-1@1.0.0-alpha.0 => package-1@1.0.0`.

> NOTE: when specifying packages, dependents of specified packages will be released, but will not be graduated.

### `--conventional-prerelease`

```sh
lerna version --conventional-commits --conventional-prerelease=package-2,package-4

# force all prerelease packages to be graduated
lerna version --conventional-commits --conventional-graduate
```

When run with this flag, `lerna version` will release with prerelease versions the specified packages (comma-separated) or all packages using `*`. Releases all unreleased changes as pre(patch/minor/major/release) by prefixing the version recommendation from `conventional-commits` with `pre`, eg. if present changes include a feature commit, the recommended bump will be `minor`, so this flag will result in a `preminor` release. If changes are present for packages that are not specified (if specifying packages), or for packages that are already in prerelease, those packages will be versioned as they normally would using `--conventional-commits`.

### `--changelog-preset`

```sh
lerna version --conventional-commits --changelog-preset angular-bitbucket
```

By default, the changelog preset is set to [`angular`](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-angular#angular-convention).
In some cases you might want to change either use a another preset or a custom one.

Presets are names of built-in or installable configuration for conventional changelog.
Presets may be passed as the full name of the package, or the auto-expanded suffix
(e.g., `angular` is expanded to `conventional-changelog-angular`).

### `--exact`

```sh
lerna version --exact
```

When run with this flag, `lerna version` will specify updated dependencies in updated packages exactly (with no punctuation), instead of as semver compatible (with a `^`).

For more information, see the package.json [dependencies](https://docs.npmjs.com/files/package.json#dependencies) documentation.

### `--force-publish`

```sh
lerna version --force-publish=package-2,package-4

# force all packages to be versioned
lerna version --force-publish
```

When run with this flag, `lerna version` will force publish the specified packages (comma-separated) or all packages using `*`.

> This will skip the `lerna changed` check for changed packages and forces a package that didn't have a `git diff` change to be updated.

### `--git-remote <name>`

```sh
lerna version --git-remote upstream
```

When run with this flag, `lerna version` will push the git changes to the specified remote instead of `origin`.

### `--create-release <type>`

```sh
lerna version --conventional-commits --create-release github
lerna version --conventional-commits --create-release gitlab
```

When run with this flag, `lerna version` will create an official GitHub or GitLab release based on the changed packages. Requires `--conventional-commits` to be passed so that changelogs can be generated.

To authenticate with GitHub, the following environment variables can be defined.

- `GH_TOKEN` (required) - Your GitHub authentication token (under Settings > Developer settings > Personal access tokens).
- `GHE_API_URL` - When using GitHub Enterprise, an absolute URL to the API.
- `GHE_VERSION` - When using GitHub Enterprise, the currently installed GHE version. [Supports the following versions](https://github.com/octokit/plugin-enterprise-rest.js).

To authenticate with GitLab, the following environment variables can be defined.

- `GL_TOKEN` (required) - Your GitLab authentication token (under User Settings > Access Tokens).
- `GL_API_URL` - An absolute URL to the API, including the version. (Default: https://gitlab.com/api/v4)

> NOTE: When using this option, you cannot pass [`--no-changelog`](#--no-changelog).

### `--ignore-changes`

Ignore changes in files matched by glob(s) when detecting changed packages.

```sh
lerna version --ignore-changes '**/*.md' '**/__tests__/**'
```

This option is best specified as root `lerna.json` configuration, both to avoid premature shell evaluation of the globs and to share the config with `lerna diff` and `lerna changed`:

```json
{
  "ignoreChanges": ["**/__fixtures__/**", "**/__tests__/**", "**/*.md"]
}
```

Pass `--no-ignore-changes` to disable any existing durable configuration.

> In the following cases, a package will always be published, regardless of this option:
>
> 1. The latest release of the package is a `prerelease` version (i.e. `1.0.0-alpha`, `1.0.0–0.3.7`, etc.).
> 2. One or more linked dependencies of the package have changed.

### `--include-merged-tags`

```sh
lerna version --include-merged-tags
```

When run with this flag, `lerna version` will also consider tags of merged branches during package change detection.

### `--message <msg>`

This option is aliased to `-m` for parity with `git commit`.

```sh
lerna version -m "chore(release): publish %s"
# commit message = "chore(release): publish v1.0.0"

lerna version -m "chore(release): publish %v"
# commit message = "chore(release): publish 1.0.0"

# When versioning packages independently, no placeholders are replaced
lerna version -m "chore(release): publish"
# commit message = "chore(release): publish
#
# - package-1@3.0.1
# - package-2@1.5.4"
```

When run with this flag, `lerna version` will use the provided message when committing the version updates
for publication. Useful for integrating lerna into projects that expect commit messages to adhere
to certain guidelines, such as projects which use [commitizen](https://github.com/commitizen/cz-cli) and/or [semantic-release](https://github.com/semantic-release/semantic-release).

If the message contains `%s`, it will be replaced with the new global version version number prefixed with a "v".
If the message contains `%v`, it will be replaced with the new global version version number without the leading "v".
Note that this placeholder interpolation only applies when using the default "fixed" versioning mode, as there is no "global" version to interpolate when versioning independently.

This can be configured in lerna.json, as well:

```json
{
  "command": {
    "version": {
      "message": "chore(release): publish %s"
    }
  }
}
```

### `--no-changelog`

```sh
lerna version --conventional-commits --no-changelog
```

When using `conventional-commits`, do not generate any `CHANGELOG.md` files.

> NOTE: When using this option, you cannot pass [`--create-release`](#--create-release-type).

### `--no-commit-hooks`

By default, `lerna version` will allow git commit hooks to run when committing version changes.
Pass `--no-commit-hooks` to disable this behavior.

This option is analogous to the `npm version` option [`--commit-hooks`](https://docs.npmjs.com/misc/config#commit-hooks), just inverted.

### `--no-git-tag-version`

By default, `lerna version` will commit changes to package.json files and tag the release.
Pass `--no-git-tag-version` to disable the behavior.

This option is analogous to the `npm version` option [`--git-tag-version`](https://docs.npmjs.com/misc/config#git-tag-version), just inverted.

### `--no-push`

By default, `lerna version` will push the committed and tagged changes to the configured [git remote](#--git-remote-name).
Pass `--no-push` to disable this behavior.

### `--preid`

```sh
lerna version prerelease
# uses the next semantic prerelease version, e.g.
# 1.0.0 => 1.0.1-alpha.0

lerna version prepatch --preid next
# uses the next semantic prerelease version with a specific prerelease identifier, e.g.
# 1.0.0 => 1.0.1-next.0
```

When run with this flag, `lerna version` will increment `premajor`, `preminor`, `prepatch`, or `prerelease` semver
bumps using the specified [prerelease identifier](http://semver.org/#spec-item-9).

### `--sign-git-commit`

This option is analogous to the `npm version` [option](https://docs.npmjs.com/misc/config#sign-git-commit) of the same name.

### `--sign-git-tag`

This option is analogous to the `npm version` [option](https://docs.npmjs.com/misc/config#sign-git-tag) of the same name.

### `--yes`

```sh
lerna version --yes
# skips `Are you sure you want to publish these packages?`
```

When run with this flag, `lerna version` will skip all confirmation prompts.
Useful in [Continuous integration (CI)](https://en.wikipedia.org/wiki/Continuous_integration) to automatically answer the publish confirmation prompt.

### `--tag-version-prefix`

This option allows to provide custom prefix instead of the default one: `v`.

Keep in mind that currently you have to supply it twice: for `version` command and for `publish` command:

```bash
# locally
lerna version --tag-version-prefix=''
# on ci
lerna publish from-git --tag-version-prefix='' 
```

## Deprecated Options

### `--cd-version`

Pass the semver keyword to the [`bump`](#bump) positional instead.

### `--repo-version`

Pass an explicit version number to the [`bump`](#bump) positional instead.

### `--skip-git`

Use [`--no-git-tag-version`](#--no-git-tag-version) and [`--no-push`](#--no-push) instead.

> NOTE: This option **does not** restrict _all_ git commands from being executed. `git` is still required by `lerna version`.

## Tips

### Generating Initial Changelogs

If you start using the [`--conventional-commits`](#--conventional-commits) option _after_ the monorepo has been active for awhile, you can still generate changelogs for previous releases using [`conventional-changelog-cli`](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-cli#readme) and [`lerna exec`](https://github.com/lerna/lerna/tree/master/commands/exec#readme):

```bash
# Lerna does not actually use conventional-changelog-cli, so you need to install it temporarily
npm i -D conventional-changelog-cli
# Documentation: `npx conventional-changelog --help`

# fixed versioning (default)
# run in root, then leaves
npx conventional-changelog --preset angular --release-count 0 --outfile ./CHANGELOG.md --verbose
npx lerna exec --concurrency 1 --stream -- 'conventional-changelog --preset angular --release-count 0 --commit-path $PWD --pkg $PWD/package.json --outfile $PWD/CHANGELOG.md --verbose'

# independent versioning
# (no root changelog)
npx lerna exec --concurrency 1 --stream -- 'conventional-changelog --preset angular --release-count 0 --commit-path $PWD --pkg $PWD/package.json --outfile $PWD/CHANGELOG.md --verbose --lerna-package $LERNA_PACKAGE_NAME'
```

If you use a custom [`--changelog-preset`](#--changelog-preset), you should change `--preset` value accordingly in the example above.
# `@lerna/list`

> List local packages

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

The `list` subcommand is aliased to several convenient shorthands (similar to [`npm ls`](https://docs.npmjs.com/cli/ls)):

- `lerna ls`: Identical to `lerna list`, which is itself analogous to the `ls` command
- `lerna ll`: Equivalent to `lerna ls -l`, showing [long](#--long) output
- `lerna la`: Equivalent to `lerna ls -la`, showing [all](#--all) packages (including private ones)

```sh
$ lerna ls
package-1
package-2
```

You might notice extra logging from `lerna` when running these commands in your shell.
Rest assured they will not infect your piped incantations,
as all logs are emitted to `stderr`, not `stdout`.

In any case, you can always pass `--loglevel silent` to create pristine chains of magical shell wizardry.

## Options

- [`--json`](#--json)
- [`--ndjson`](#--ndjson)
- [`-a`, `--all`](#--all)
- [`-l`, `--long`](#--long)
- [`-p`, `--parseable`](#--parseable)
- [`--toposort`](#--toposort)
- [`--graph`](#--graph)

`lerna ls` also respects all available [Filter Flags](https://www.npmjs.com/package/@lerna/filter-options).

### `--json`

Show information as a JSON array.

```sh
$ lerna ls --json
[
  {
    "name": "package-1",
    "version": "1.0.0",
    "private": false,
    "location": "/path/to/packages/pkg-1"
  },
  {
    "name": "package-2",
    "version": "1.0.0",
    "private": false,
    "location": "/path/to/packages/pkg-2"
  }
]
```

**Tip:** Pipe to the [`json`](http://trentm.com/json/) utility to pick out individual properties:

```sh
$ lerna ls --json --all | json -a -c 'this.private === true' name
package-3
```

### `--ndjson`

Show information as [newline-delimited JSON](http://ndjson.org).

```sh
$ lerna ls --ndjson
{"name":"package-1","version":"1.0.0","private":false,"location":"/path/to/packages/pkg-1"}
{"name":"package-2","version":"1.0.0","private":false,"location":"/path/to/packages/pkg-2"}
```

### `--all`

Alias: `-a`

Show private packages that are hidden by default.

```sh
$ lerna ls --all
package-1
package-2
package-3 (private)
```

### `--long`

Alias: `-l`

Show extended information.

```sh
$ lerna ls --long
package-1 v1.0.1 packages/pkg-1
package-2 v1.0.2 packages/pkg-2

$ lerna ls -la
package-1 v1.0.1 packages/pkg-1
package-2 v1.0.2 packages/pkg-2
package-3 v1.0.3 packages/pkg-3 (private)
```

### `--parseable`

Alias: `-p`

Show parseable output instead of columnified view.

By default, each line of the output is an absolute path to a package.

In `--long` output, each line is a `:`-separated list: `<fullpath>:<name>:<version>[:flags..]`

```sh
$ lerna ls --parseable
/path/to/packages/pkg-1
/path/to/packages/pkg-2

$ lerna ls -pl
/path/to/packages/pkg-1:package-1:1.0.1
/path/to/packages/pkg-2:package-2:1.0.2

$ lerna ls -pla
/path/to/packages/pkg-1:package-1:1.0.1
/path/to/packages/pkg-2:package-2:1.0.2
/path/to/packages/pkg-3:package-3:1.0.3:PRIVATE
```

### `--toposort`

Sort packages in topological order (dependencies before dependents) instead of lexical by directory.

```sh
$ json dependencies <packages/pkg-1/package.json
{
  "pkg-2": "file:../pkg-2"
}

$ lerna ls --toposort
package-2
package-1
```

### `--graph`

Show dependency graph as a JSON-formatted [adjacency list](https://en.wikipedia.org/wiki/Adjacency_list).

```sh
$ lerna ls --graph
{
  "pkg-1": [
    "pkg-2"
  ],
  "pkg-2": []
}

$ lerna ls --graph --all
{
  "pkg-1": [
    "pkg-2"
  ],
  "pkg-2": [
    "pkg-3"
  ],
  "pkg-3": [
    "pkg-2"
  ]
}
```
# `@lerna/exec`

> Execute an arbitrary command in each package

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna exec -- <command> [..args] # runs the command in all packages
$ lerna exec -- rm -rf ./node_modules
$ lerna exec -- protractor conf.js
```

Run an arbitrary command in each package.
A double-dash (`--`) is necessary to pass dashed flags to the spawned command, but is not necessary when all the arguments are positional.

The name of the current package is available through the environment variable `LERNA_PACKAGE_NAME`:

```sh
$ lerna exec -- npm view \$LERNA_PACKAGE_NAME
```

You may also run a script located in the root dir, in a complicated dir structure through the environment variable `LERNA_ROOT_PATH`:

```sh
$ lerna exec -- node \$LERNA_ROOT_PATH/scripts/some-script.js
```

## Options

`lerna exec` respects the `--concurrency`, `--scope`, and `--ignore` flags (see [Filter Flags](https://www.npmjs.com/package/@lerna/filter-options)).

```sh
$ lerna exec --scope my-component -- ls -la
```

> The commands are spawned in parallel, using the concurrency given (except with `--parallel`).
> The output is piped through, so not deterministic.
> If you want to run the command in one package after another, use it like this:

```sh
$ lerna exec --concurrency 1 -- ls -la
```

### `--stream`

Stream output from child processes immediately, prefixed with the originating
package name. This allows output from different packages to be interleaved.

```sh
$ lerna exec --stream -- babel src -d lib
```

### `--parallel`

Similar to `--stream`, but completely disregards concurrency and topological sorting, running a given command or script immediately in all matching packages with prefixed streaming output. This is the preferred flag for long-running processes such as `babel src -d lib -w` run over many packages.

```sh
$ lerna exec --parallel -- babel src -d lib -w
```

> **Note:** It is advised to constrain the scope of this command when using
> the `--parallel` flag, as spawning dozens of subprocesses may be
> harmful to your shell's equanimity (or maximum file descriptor limit,
> for example). YMMV

### `--no-bail`

```sh
# Run a command, ignoring non-zero (error) exit codes
$ lerna exec --no-bail <command>
```

By default, `lerna exec` will exit with an error if _any_ execution returns a non-zero exit code.
Pass `--no-bail` to disable this behavior, executing in _all_ packages regardless of exit code.

### `--no-prefix`

Disable package name prefixing when output is streaming (`--stream` _or_ `--parallel`).
This option can be useful when piping results to other processes, such as editor plugins.
# `@lerna/run`

> Run an npm script in each package that contains that script

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna run <script> -- [..args] # runs npm run my-script in all packages that have it
$ lerna run test
$ lerna run build

# watch all packages and transpile on change, streaming prefixed output
$ lerna run --parallel watch
```

Run an [npm script](https://docs.npmjs.com/misc/scripts) in each package that contains that script. A double-dash (`--`) is necessary to pass dashed arguments to the script execution.

## Options

`lerna run` respects the `--concurrency`, `--scope`, and `--ignore` flags (see [Filter Flags](https://www.npmjs.com/package/@lerna/filter-options)).

```sh
$ lerna run --scope my-component test
```

### `--npm-client <client>`

Must be an executable that knows how to run npm lifecycle scripts.
The default `--npm-client` is `npm`.

```sh
$ lerna run build --npm-client=yarn
```

May also be configured in `lerna.json`:

```json
{
  "command": {
    "run": {
      "npmClient": "yarn"
    }
  }
}
```

### `--stream`

Stream output from child processes immediately, prefixed with the originating
package name. This allows output from different packages to be interleaved.

```sh
$ lerna run watch --stream
```

### `--parallel`

Similar to `--stream`, but completely disregards concurrency and topological sorting, running a given command or script immediately in all matching packages with prefixed streaming output. This is the preferred flag for long-running processes such as `npm run watch` run over many packages.

```sh
$ lerna run watch --parallel
```

> **Note:** It is advised to constrain the scope of this command when using
> the `--parallel` flag, as spawning dozens of subprocesses may be
> harmful to your shell's equanimity (or maximum file descriptor limit,
> for example). YMMV

### `--no-bail`

```sh
# Run an npm script in all packages that contain it, ignoring non-zero (error) exit codes
$ lerna run --no-bail test
```

By default, `lerna run` will exit with an error if _any_ script run returns a non-zero exit code.
Pass `--no-bail` to disable this behavior, running the script in _all_ packages that contain it regardless of exit code.

### `--no-prefix`

Disable package name prefixing when output is streaming (`--stream` _or_ `--parallel`).
This option can be useful when piping results to other processes, such as editor plugins.
# `@lerna/changed`

> List local packages that have changed since the last tagged release

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

The output of `lerna changed` is a list of packages that would be the subjects of the next `lerna version` or `lerna publish` execution.

```sh
$ lerna changed
package-1
package-2
```

**Note:** `lerna.json` configuration for `lerna publish` _and_ `lerna version` also affects
`lerna changed`, e.g. `command.publish.ignoreChanges`.

## Options

`lerna changed` supports all of the flags supported by [`lerna ls`](https://github.com/lerna/lerna/tree/master/commands/list#options):

- [`--json`](https://github.com/lerna/lerna/tree/master/commands/list#--json)
- [`--ndjson`](https://github.com/lerna/lerna/tree/master/commands/list#--ndjson)
- [`-a`, `--all`](https://github.com/lerna/lerna/tree/master/commands/list#--all)
- [`-l`, `--long`](https://github.com/lerna/lerna/tree/master/commands/list#--long)
- [`-p`, `--parseable`](https://github.com/lerna/lerna/tree/master/commands/list#--parseable)
- [`--toposort`](https://github.com/lerna/lerna/tree/master/commands/list#--toposort)
- [`--graph`](https://github.com/lerna/lerna/tree/master/commands/list#--graph)

Unlike `lerna ls`, however, `lerna changed` **does not** support [filter options](https://www.npmjs.com/package/@lerna/filter-options), as filtering is not supported by `lerna version` or `lerna publish`.

`lerna changed` also supports all the flags supported by [`lerna version`](https://github.com/lerna/lerna/tree/master/commands/version#options).

Relevant ones are maybe:

- [`--ignore-changes`](https://github.com/lerna/lerna/tree/master/commands/version#--ignore-changes).
- [`--include-merged-tags`](https://github.com/lerna/lerna/tree/master/commands/version#--include-merged-tags).
# `@lerna/import`

> Import a package into the monorepo with commit history

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```sh
$ lerna import <path-to-external-repository>
```

Import the package at `<path-to-external-repository>`, with commit history,
into `packages/<directory-name>`. Original commit authors, dates and messages
are preserved. Commits are applied to the current branch.

This is useful for gathering pre-existing standalone packages into a Lerna
repo. Each commit is modified to make changes relative to the package
directory. So, for example, the commit that added `package.json` will
instead add `packages/<directory-name>/package.json`.

_Note_: If you're importing an external repository on a new lerna repository, then do remember to have at least one commit.

```bash
# Getting started with Lerna
$ git init lerna-repo && cd lerna-repo
$ npx lerna init
$ npm install

# Adding a commit
$ git add .
$ git commit -m "Initial lerna commit" # Without a commit, import command would fail

# Importing other repository
$ npx lerna import <path-to-external-repository>
```

## Options

### `--flatten`

When importing repositories with merge commits with conflicts, the import command will fail trying to apply all commits. The user can use this flag to ask for import of "flat" history, i.e. with each merge commit as a single change the merge introduced.

```
$ lerna import ~/Product --flatten
```

### `--dest`

When importing repositories, you can specify the destination directory by the directory listed in lerna.json.

```
$ lerna import ~/Product --dest=utilities
```

### `--preserve-commit`

Each git commit has an **author** and a **committer** (with a separate date for each). Usually they're the same person (and date), but since `lerna import` re-creates each commit from the external repository, the **committer** becomes the current git user (and date). This is *technically* correct, but may be undesireable, for example, on Github, which displays both the **author** and **committer** if they're different people, leading to potentially confusing history/blames on imported commits.

Enabling this option preserves the original **committer** (and commit date) to avoid such issues.

```
$ lerna import ~/Product --preserve-commit
```
# `@lerna/create`

> Create a new lerna-managed package

Install [lerna](https://www.npmjs.com/package/lerna) for access to the `lerna` CLI.

## Usage

```
lerna create <name> [loc]

Create a new lerna-managed package

Positionals:
  name  The package name (including scope), which must be locally unique _and_
        publicly available                                   [string] [required]
  loc   A custom package location, defaulting to the first configured package
        location                                                        [string]

Command Options:
  --access        When using a scope, set publishConfig.access value
                             [choices: "public", "restricted"] [default: public]
  --bin           Package has an executable. Customize with --bin
                  <executableName>                             [default: <name>]
  --description   Package description                                   [string]
  --dependencies  A list of package dependencies                         [array]
  --es-module     Initialize a transpiled ES Module
  --homepage      The package homepage, defaulting to a subpath of the root
                  pkg.homepage                                          [string]
  --keywords      A list of package keywords                             [array]
  --license       The desired package license (SPDX identifier)   [default: ISC]
  --private       Make the new package private, never published
  --registry      Configure the package's publishConfig.registry        [string]
  --tag           Configure the package's publishConfig.tag             [string]
  --yes           Skip all prompts, accepting default values
```
