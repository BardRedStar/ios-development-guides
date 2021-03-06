## Tools we use
---

> [Home page](/README.md)

## Git
Git-flow with naming conventions and commit style.

[Take a look how we work with git](Tools/work-with-git.md)

Default [.gitignore](../.gitignore)

## Cocoapods
1. Pods directory should be added to <code>.gitignore</code> file.
2. Files <code>Podfile</code> and <code>Podfile.lock</code> should be added to Git index.

## SwiftLint
Use [SwiftLint tool](https://github.com/realm/SwiftLint). Take a look at our latest [.swiftlint.yml](../Development/.swiftlint.yml) as a basic template for a project.

The fastest way to get a copy of the config:
```bash
curl -O "https://raw.githubusercontent.com/Saritasa/ios-development-guides/master/Development/.swiftlint.yml"
```

## swiftformat

Swiftformat can be configured on a project in several ways. I believe the simplest way is to use
a `format.sh` script in the project directory (replace `<project directory>` with actual
directory):

```bash
#!/usr/bin/env bash

swiftformat \
    --config .swiftformat \
    <project directory>/
```

Don't forget to set permissions on this file by running `chmod 754 format.sh`.

We use the following formatting options (in `.swiftformat` file):
```bash
# Initial list of options was provided by running swiftformat with `--inferoptions`

--binarygrouping none
--closingparen same-line
--commas inline
--comments ignore
--decimalgrouping none
--hexgrouping none
--ifdef no-indent
--octalgrouping none
--patternlet hoist
--ranges no-space
--stripunusedargs closure-only
--wraparguments after-first
--wrapcollections before-first
--swiftversion 4.2
```

You should change `--swiftversion` accordingly.

After this setup you can run `./format.sh` periodically to format all files in a project.

### Run swiftformat on each build (recommended)

Change `format.sh` to the following (replace you paths to the tool and config accordingly):

```sh
#!/usr/bin/env bash

if ./Pods/SwiftFormat/CommandLineTool/swiftformat --version >/dev/null; then
    git diff --diff-filter=d --name-only | grep -e '\(.*\).swift$' | while read line; do
        ./Pods/SwiftFormat/CommandLineTool/swiftformat  --config .swiftformat "../${line}";
    done
fi
```

This script only formats swift files that have changed in git.

Since xcode doesn't support normal integration with command line tools
(such as formatters) you should be aware of the following issues:

- always save a file before build, otherwise you may see a saving error "The document could not be
saved. The file has been changed by another application." when you try to save a file next time.
- after auto formatting is run on some files, the undo history for those files will be wiped out.

### Run swiftformat using git hooks (deprecated)

You can run `swiftformat` on each commit and only on files that you have changed by using
pre-commit hook. In git hooks are always local so you need to convey other developers that you use
them. To share hooks among the team of developers you can create `git-hooks` directory in the root
of a repository and add all hooks there. Then you can point git to use this directory by running the
following command: `git config core.hooksPath git-hooks/`

For `swiftformat` you can use the following `pre-commit` hook (change path to the config
accordingly and set permissions on the file `chmod 754 pre-commit`):

```sh
#!/usr/bin/env bash

if which swiftformat >/dev/null; then
    git diff --diff-filter=d --staged --name-only | grep -e '\(.*\).swift$' | while read line; do
        swiftformat --config Application/.swiftformat "${line}";
        git add "$line";
    done
else
    echo "warning: SwiftFormat not installed, download from https://github.com/nicklockwood/SwiftFormat"
fi
```

> Note: if a file will be formatted it will be staged for the commit. This breaks a flow when you
> want to make several commits with changes. So you can remove `git add "$line";` after running
> swiftformat. This also allows to verify formatting before commiting to the repo.

Also Check out [Swift guidelines](CodeStyle.md#swift)

## Other tools that might be useful

- https://eject.herokuapp.com - converts xibs into code
- https://www.charlesproxy.com - debug http requests
- https://app.quicktype.io/ - fast JSON to Codable conversion

> [Home page](/README.md)
