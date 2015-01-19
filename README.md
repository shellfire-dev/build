# `build`: functions module for [shellfire]

This module provides a small framework for making it straightforward to [build], [fatten] and [swaddle] [shellfire] applications. Unlike traditional build tools, it provides ***full*** access to a rich runtime library - [shellfire] and all its modules. A generic build script is supplied (`build`) that, together with an user-defined file `build.shellfire`, can be used to rapidly create build scripts. This file is idential to normal [shellfire] application logic - reference other modules, call functions, create functions, do whatever you want. The included functions can also be used standalone.

An example user is [shellfire].

Code may change as usage expands.

## Compatibility

* Tag [`release_2015.0117.1750-1`](https://github.com/shellfire-dev/build/releases/tag/release_2015.0117.1750-1) is compatible with [shellfire] release [`release_2015.0117.1750-1`](https://github.com/shellfire-dev/shellfire/releases/tag/release_2015.0117.1750-1).

## Overview

### Quick Tutorial

Do the following from the top-level folder of your git repository:-

```bash

# Add this as a submodule (Importing)
mkdir -m 0755 -p lib/shellfire
cd lib/shellfire
git submodule add "https://github.com/shellfire-dev/build.git"
git submodule update --init
cd -

# Symlink the build script
ln -s lib/shellfire/build/build

# Create some build rules
cat >build.shellfire <<-EOF
build()
{
	# Create a folder called 'output'
	build_prepareOutput
	
	core_message NOTICE "Hello World"
}
EOF

# Run the build!
./build

# Build scripts are regular shellfire applications
./build --help
```

Of course, you could add functions (just prefix them so they don't collide with the `build` namespace - `_program_` is appropriate), even add additional [shellfire] modules - anything a [shellfire] application can do, build scripts can do. And if you come up with something good, please consider pushing a pull request.

### Build with [swaddle] Tutorial

The [build] module contains a wrapper around [fatten] and [swaddle]. Setting it up requires us to:-

* Completed the [Quick Tutorial](#quick-tutorial)
* Import [fatten]
* Import [swaddle]
* Adjust the build rules
* Add swaddling
* Add a `README.md` and `COPYRIGHT` file, if not already present.


#### Import [fatten]

There are three ways to import [fatten]:-

* By downloading a [executable release](https://github.com/shellfire-dev/fatten/releases), and adding it as an executable at `tools/fatten/fatten`
* By relying on your package manager
* By importing it as a submodule

Since releases of fatten have [shellfire]'s automatic dependency installation disabled, it is preferable for this tutorial to use the submodule. This ensures the build system installs what it needs on first run. Let's add it:-

```bash
mkdir -m 0755 -p tools
cd tools
git submodule add --branch master "https://github.com/shellfire-dev/fatten.git"
git submodule init
cd -
```

This tracks [fatten]'s master branch. To fix to a particular release of [fatten], such as `release_2015.0116.1415-1`:-

```bash
# Assuming the steps above have been taken
cd tools/fatten
git checkout release_2015.0116.1415-1
cd -
```

#### Import [swaddle]

Similarly to [fatten], for [swaddle], there are three ways to import it. Again, we'll add it as a submdoule:-

```bash
cd tools
git submodule add --branch master "https://github.com/raphaelcohn/swaddle.git"
git submodule init
cd -
```

This tracks [swaddle]'s master branch. To fix to a particular release of [swaddle], such as `release_2015.0117.1737-2`:-

```bash
# Assuming the steps above have been taken
cd tools/swaddle
git checkout release_2015.0117.1737-2
cd -
```

#### Adjust the build rules

Do the following from the top-level folder of your git repository:-


```bash
# Set these. For the overdrive tutorial, they'd be 'overdrive' and 'overdrive'
mygithubuser=MY_GITHUB_USER_OR_ORGANIZATION
myproject=MY_PROJECT_NAME
myprogram=MY_PROGRAM_TO_FATTEN

# Overwrite your build rules
cat >build.shellfire <<-EOF
build()
{
	build_prepareOutput
	
	build_fattenAndSwaddle '${myproject}' "\$build_relativePath" '${myprogram}'
}
EOF
```







These steps assume you have a `README.md` and `COPYRIGHT` file (in [Machine-readable Debian format](https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/)):-













These steps assume you have a `README.md` and `COPYRIGHT` file (in [Machine-readable Debian format](https://www.debian.org/doc/packaging-manuals/copyright-format/1.0/)):-
## Overview


```bash
# Ignore output folder created by `build_prepareOutput()`
printf '\n%s\n' "output" >>.gitignore

# Create some swaddling for your project
mkdir -m 0755 -p swaddling/"$myproject"/{tar,deb,skeleton/all}
cat >swaddling/swaddling.conf <<-EOF
    configure swaddle host_base_url 'https://${mygithubuser}.github.io/${myproject}/download'
    configure swaddle bugs_url 'https://github.com/$mygithubuser/${myproject}/issues'
    configure swaddle maintainer_name 'Raphael Cohn'
    configure swaddle maintainer_comment 'Package Signing Key'
    configure swaddle maintainer_email 'raphael.cohn@stormmq.com'
    configure swaddle sign no
    configure swaddle vendor ${mygithubuser}
EOF
cat >swaddling/"$myproject"/package.conf <<-EOF
    configure swaddle_package description \
    "Overdrive is a tutorial application
    The first line is used as a summary."
EOF
cat >swaddling/"$myproject"/deb/deb.conf <<-EOF
    configure swaddle_deb depends dash
    configure swaddle_deb compression gzip
EOF
cat >swaddling/"$myproject"/tar/tar.conf <<-EOF
    configure swaddle_tar compressions gzip
    configure swaddle_tar compressions xz
EOF

## Make sure we don't check stuff in
printf '\n%s\n' 'body' >swaddling/overdrive/.gitignore
## 

# Use the help to see what build does
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

You may need to change the url `https://github.com/shellfire-dev/build.git` above if using a fork.

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
