# commit-formatter-example readme

## Setup
To use the funcionality from the custom git hook we defined we need to run `git config core.hooksPath .githooks` after we clone the repo

Note: this only works on git version 2.9 and above, so please check your `git --version` before running this command

##

If the setup is successful you'll see that in this repository commit messages are analyzed through commit-msg hook found in ./hooks (usualy this would be .git/hooks)

Valid commits follow a simple rule of `<type>:<description>` where type can be any of the following: `feature`, `fix`, `refactor`, `wip` and the complete commit message can't be more than 50 characters long.

You can read more about git hooks in [the official docs](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)

## How was this done?

To achieve this I used 3 Reg-ex-s *(is this even the propper way to write reg ex es?)* in order to get finer error reporting once an error gets recognized in a commit message. 

### These Regular expresisions test

if the commit message has the propper type
```
^(feature|fix|refactor|wip): .*$
```

if the commit message has the propper length
```
^.{1,50}$
```
if the commit message contains a colon

```
*:*
```

## Check the code
Full code found from .git/hooks/commit-msg is below

```
#!/bin/sh

temp_commit_file=$1
commit_msg=$(cat "$temp_commit_file")

commit_type_pattern="^(feature|fix|refactor|wip): .*$"
commit_length_pattern="^.{1,50}$"

# Checks the commit message length
if ! [[ $commit_msg =~ $commit_length_pattern ]]; then
    echo "Error: Commit message is too long and it exceeds 50 characters." >&2
    exit 1

# Checks for a colon in the commit message
elif [[ $commit_msg != *:* ]]; then
    echo "Error: Commit message must contain a colon \":\" to separate commit_type and description." >&2
	echo "Correct commit message format should follow <type>: <description> format"
    exit 1

# Checks the commit type
elif ! [[ $commit_msg =~ $commit_type_pattern ]]; then
    echo "Error: Invalid commit type." >&2
	echo "Valid commit_types include: feature, fix, refactor, wip)"
	echo "Correct commit message format should follow <type>: <description> format"
    exit 1
fi
fi
```