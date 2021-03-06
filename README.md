shin
====

Manage those dotfiles and shell snippets like a human, you dirty ape.

## OK, so what?

`shin` is a simple package manager geared towards wrangling all those dotfile fragments, shell functions, and other crap you've got stuck in `.profile`, `.bash_profile`, and their friends right now.  Instead of a giant file of death, you can now separate them into neat, versionable little packages.

## Installation

To install `shin`, simply run the `install.sh` file from the repo.  You can do so like this:

	wget https://raw.github.com/jm/shin/master/install.sh
	sh install.sh

Or if you're feeling risky:

	curl https://raw.github.com/jm/shin/master/install.sh | sh

The install will do three things:

* Create a directory in your home directory named `.shin`
* Clone a copy of `shin` to `.shin/system`
* Install a small little initializer snippet into the bottom of your `.profile`

Once that's finished up, you should be good to go.

## Usage

The commands are fairly straightforward:

### Installing packages from Git

To install a package into `shin`, it must be built to work with `shin` (see information on package format below; it's simple!).  Once you've got a Git repository you'd like to install from, you can install from it in a couple of ways using the `.install` command (aliased as `shin install`).  If it's a GitHub repository, you can simply give `shin` the `owner/repo` format like so:

	mymachine$ .install jm/silly

You can also give it a Git repo URL straight up if you want:

	mymachine$ .install git://github.com/jm/silly.git

If the package you want to install is hosted on Gist (you can have multiple files in the repo, remember?), you can give `shin` the repo URL or you can give it a Gist number:

	mymachine$ .install 12345

#### Updating via Git

Updating a package will go to its origin and pull the `master` branch (I may support other branches later or something, but it's an unlikely addition unless someone makes a compelling patch).  To update a package, run the `.update` command (aliased as `shin update`) followed by the package name:

	mymachine$ .update silly

### Installing packages via HTTP

You can also create ad hoc packages by installing scripts from a URL.  Simply feed the `.install` command or its alias an HTTP URL rather than a Git URL:

	mymachine$ .install http://myscripts.com/myscript.sh

That will create a `myscript` package from the contents of that URL.  If given a second argument (e.g., `.install http://myscripts.com/myscript.sh lololol`), `shin` will name the package as the second argument.

#### Updating via HTTP

To update an HTTP package, simply run `.update package-name`.  `shin` stores the URL for the script in the package, so it knows where to go and fetch a new version from.

### Uninstalling

To uninstall a package, simply run the `.uninstall` (aliased as `shin uninstall` also) command followed by the package name:

	mymachine$ .uninstall silly

### Listing packages

To get a list of all packages, run `.list` (or `shin list`).  One day I'll support in-list searching, but that's pretty far down the list.

### Setting your prompt

Some packages provide a special prompt you can activate.  Let's say you had a nice Git package that had a special prompt for managing bisects.  You might activate it like so:

	.ps1 awesome-git-package-name

You can also activate the special prompts with the `.prompt` or `shin ps1` commands.

### Initializing

If you choose to remove the shell initializer from your `.profile`, you can init the `shin` environment by running `shin init`.

### The bucket

`shin` also has the concept of a "bucket" package.  This package is basically just a holder for any random snippets or code you want to place in there.  To edit your bucket file directly in your default editor, simply run `shin bucket`.

## Anatomy of a package

A (non-ad-hoc) `shin` package is simply a Git repository with a few special files:

	* A `shinit.sh` file (required)
	* Your code (either in the `shinit.sh` or sourced from there)
	* A `.shin_description` file that is a short description of the package
	* An optional `ps1.sh` that will chnage a user's prompt (PS1)

That's all!  The `shinit.sh` file is where the magic happens.  If you need to source anything from there, simply use the `$package_path` variable the loader will expose to your script to source things in the package's path:

	source $package_path/this_file.sh

Otherwise, you can put everything in the `shinit.sh` file.  You should include a `.shin_description` file since one day I may build out an index for these things.  For an example of the smallest (useful) package, check out my `serve` package at https://github.com/jm/serve.

## Removing from your system

To remove `shin`, simply delete `~/.shin` and remove the lines added by `shin` to your `.profile`.

## Contributing and such

Feel free to file issues in the issue tracker here on GitHub; pull requests are, of course, accepted (especially if I've done something stupid in Bash; a shell programmer, I am not).

As for things to add, here's a quick list:

* Move description and dependency information into a `.shinfo` file instead of separate files
* Build out a ridiculously simple index service that simply has an API for pointing to repos
* Dependencies would be nice
* RVM Gemset-like functionality.  Much of the infrastructure is there (segmenting into sets, mapping functions added by a package, etc.); there's just a bit more groundwork to lay
