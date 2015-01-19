# `build`: functions module for [shellfire]

This module provides a small framework for making it straightforward to [build], [fatten] and [swaddle] [shellfire] applications. Unlike traditional build tools, it provides ***full*** access to a rich runtime library - [shellfire] and all its modules. A generic build script is supplied (`build`) that, together with an user-defined file `build.shellfire`, can be used to rapidly create build scripts. This file is idential to normal [shellfire] application logic - reference other modules, call functions, create functions, do whatever you want. The included functions can also be used standalone.

An example user is [shellfire].

Code may change as usage expands.

## Compatibility

* Tag [`release_2015.0117.1750-1`](https://github.com/shellfire-dev/build/releases/tag/release_2015.0117.1750-1) is compatible with [shellfire] release [`release_2015.0117.1750-1`](https://github.com/shellfire-dev/shellfire/releases/tag/release_2015.0117.1750-1).

## Overview

Do the following from the top-level folder of your git repository:-

```bash

# Add this as a submodule (Importing)
mkdir -p lib/shellfire
cd lib/shellfire
git submodule add "https://github.com/shellfire-dev/build.git"
git submodule update --init
cd -

# Symlink the build script
ln -s lib/shellfire/build/build

# Create build rules
cat >build.shellfire <<-EOF
build()
{
	build_travis_ci_updateGitSubmodulesRecursively
	build_travis_ci_ensureGnupgKeyringExists
	build_prepareOutput
	
	build_fattenAndSwaddle 'myproject' "$build_relativePath" paths.d-helper
}
EOF

# Ignore output folder created by `build_prepareOutput()`
printf '\n%s\n' "output" >>.gitignore

# Now build your application with `./build`
./build

# Use the help to see what else you can do
./build --help
```

Would it be useful to supply a script to do these steps for you? (eg one that is available as a curl one-liner)? Please let us know.

## Global Variables

The global array variable `build_nonOptions` contains all non-options (ie things after switches / `--`). The [shellfire] build uses this variable to decide which components to build.

## Notes

* Please note that the symlink `build.shellfire.functions` _intentionally_ appears broken.


## Importing

To import this module, add a git submodule to your repository. From the root of your git repository in the terminal, type:-
```bash
mkdir -p lib/shellfire
cd lib/shellfire
git submodule add "https://github.com/shellfire-dev/build.git"
cd -
git submodule init --update
```

You may need to change the url `https://github.com/shellfire-dev/build.git"` above if using a fork.

You will also need to add paths - include the module [paths.d].


## Namespace `build`

### To use in code

If calling from another [shellfire] module, add to your shell code the line
```bash
core_usesIn build
```
in the global scope (ie outside of any functions). A good convention is to put it above any function that depends on functions in this module. If using it directly in a program, put this line _inside_ the `_program()` function:-

```bash
_program()
{
	core_usesIn build
	…
}
```

### Functions

***
#### `build_prepareOutput()`
Takes no parameters.

Creates an output folder at `build_outputPath`, which, by default, is `$(pwd)/output` but can be overridden by `build --output-path`.

***
#### `build_fattenAndSwaddle()`
|Parameter|Value|Optional|
|---------|-----|--------|
|`swaddleName`|Name of the swaddle to use.|_No_|
|`repositoryPath`|Path to repository.|_No_|
|`…`|One or more [shellfire] applications (files) to fatten|_Yes_, but please supply at least one to be useful!|

This function [fatten]s one or more [shellfire] applications at `repositoryPath` before [swaddle]ing them with a swaddle `swaddleName` at `repositoryPath/swaddling/swaddleName`. Output is written to `build_outputPath/fattened` and `build_outputPath/swaddled`. Since [swaddle]ing also tags and publishes to GitHub releases (but not, as yet, pages), this also does a release.

Before calling this function, you should prepare any other output in the `body` folders of the [swaddle].

Additionally, a Debian format COPYRIGHT file is expected at `repositoryPath`.

Commonly, `"$build_relativePath"` is passed for `repositoryPath`.

***


## Namespace `build_travis_ci`

Please note, Travis CI doesn't play nicely with [swaddle] at this time. [swaddle] uses GPG and has its own concepts of release, etc, which, in our view, are far more useful than the Travis CI approach. Opinions will differ, but we should think of Continuous Deployment as the new normal, and script for it, rather than separately. Build artifacts should become a thing of the past, of no more importance than temp files.

### To use in code

If calling from another [shellfire] module, add to your shell code the line
```bash
core_usesIn build/travis ci
```
in the global scope (ie outside of any functions). A good convention is to put it above any function that depends on functions in this module. If using it directly in a program, put this line _inside_ the `_program()` function:-

```bash
_program()
{
	core_usesIn build/travis ci
	…
}
```

### Functions

#### `build_travis_ci_isExecuting()`
Takes no arguments.

Returns a zero value if Travis CI is executing, or non-zero if not.

***
#### `build_travis_ci_do()`
|Parameter|Value|Optional|
|---------|-----|--------|
|`…`|A command line consisting of a function name and arguments to pass to it.|_No_|

Executes the passed command line if Travis CI is executing.

***
#### `build_travis_ci_updateGitSubmodulesRecursively()`
Takes no arguments.

Attempts to update git submodules recursively in Travis CI environment. Highly experimental, subject to change.

***

#### `build_travis_ci_ensureGnupgKeyringExists()`
Takes no arguments.

Attempts to ensure GPG keyring folder exists. Highly experimental, subject to change.

***


[swaddle]: https://github.com/raphaelcohn/swaddle "Swaddle homepage"
[shellfire]: https://github.com/shellfire-dev "shellfire homepage"
[fatten]: https://github.com/shellfire-dev "fatten homepage"
[build]: https://github.com/shellfire-dev/core "shellfire build module homepage"
[core]: https://github.com/shellfire-dev/core "shellfire core module homepage"
[paths.d]: https://github.com/shellfire-dev/paths.d "paths.d shellfire module homepage"
