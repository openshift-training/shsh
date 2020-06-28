# shsh

A package manager for shell scripts and functions.

Basher allows you to quickly install shell packages directly from github (or
other sites). Instead of looking for specific install instructions for each
package and messing with your path, shsh will create a central location for
all packages and manage their binaries for you.

Even though it is called shsh, it also works with zsh and fish.

[![Build Status](https://travis-ci.org/shshpm/shsh.svg?branch=master)](https://travis-ci.org/shshpm/shsh)

## Installation

1. Checkout shsh on `~/.shsh`

    ~~~ sh
    $ git clone https://github.com/shshpm/shsh.git ~/.shsh
    ~~~

2. Initialize shsh in your shell initialization

    ~~~ sh
    export PATH="$HOME/.shsh/bin:$PATH"'
    eval "$(shsh init -)"
    ~~~

    **Fish**: Use the following commands instead:

    ~~~ sh
    if test -d ~/.shsh
      set shsh ~/.shsh/bin
    end
    set -gx PATH $shsh $PATH
    status --is-interactive; and . (shsh init -|psub)
    ~~~

## Updating

Go to the directory where you cloned shsh and pull the latest changes:

~~~ sh
$ cd ~/.shsh
$ git pull
~~~

## Usage

### Installing packages from Github

~~~ sh
$ shsh install sstephenson/bats
~~~

This will install bats from https://github.com/sstephenson/bats and add `bin/bats` to the PATH.

### Installing packages from other sites

~~~ sh
$ shsh install bitbucket.org/user/repo_name
~~~

This will install `repo_name` from https://bitbucket.org/user/repo_name

### Using ssh instead of https

If you want to do local development on installed packages and you have ssh
access to the site, use `--ssh` to override the protocol:

~~~ sh
$ shsh install --ssh juanibiapina/gg
~~~

### Installing a local package

If you develop a package locally and want to try it through shsh,
use the `link` command:

~~~ sh
$ shsh link directory my_namespace/my_package
~~~

The `link` command will install the dependencies of the local package.
You can prevent that with the `--no-deps` option:

~~~ sh
$ shsh link --no-deps directory my_namespace/my_package
~~~

### Sourcing files from a package into current shell

Basher provides an `include` function that allows sourcing files into the
current shell. After installing a package, you can run:

```
include username/repo lib/file.sh
```

This will source a file `lib/file.sh` under the package `username/repo`.

### Command summary

- `shsh commands` - List commands
- `shsh help <command>` - Display help for a command
- `shsh uninstall <package>` - Uninstall a package
- `shsh list` - List installed packages
- `shsh outdated` - List packages which are not in the latest version
- `shsh upgrade <package>` - Upgrade a package to the latest version

### Configuration options

To change the behavior of shsh, you can set the following variables either
globally or before each command:

- If `$XDG_DATA_HOME` is set, `$SHSH_ROOT` will be set as `$XDG_DATA_HOME/shsh` (commonly that will be at `~/.local/share/shsh`). It is used to store cellar for the cloned packages. If `$XDG_DATA_HOME` is not set, `$SHSH_ROOT` will be default as `$HOME/.shsh`.
- `SHSH_FULL_CLONE=true` - Clones the full repo history instead of only the last commit (useful for package development)
- `SHSH_PREFIX` - set the installation and package checkout prefix (default is `$SHSH_ROOT/cellar`).  Setting this to `/usr/local`, for example, will install binaries to `/usr/local/bin`, manpages to `/usr/local/man`, completions to `/usr/local/completions`, and clone packages to `/usr/local/packages`.  This allows you to manage "global packages", distinct from individual user packages.

## Packages

Packages are simply repos (username/repo). You may also specify a site
(site/username/repo).

Any files inside a bin directory are added to the path. If there is no bin
directory, any executable files in the package root are added to the path.

Any manpages (files ended in `\.[0-9]`) inside a `man` directory are added
to the manpath.

Optionally, a repo might contain a `package.sh` file which specifies binaries,
dependencies and completions in the following format:

~~~ sh
BINS=folder/file1:folder/file2.sh
DEPS=user1/repo1:user2/repo2
BASH_COMPLETIONS=completions/package
ZSH_COMPLETIONS=completions/_package
~~~

BINS specified in this fashion have higher precedence then the inference rules
above.
