Automate backport PR

## Prerequisites

Requires installation of PyGithub
```shell
pip install pygithub
```

Token is [provided from Github](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/). Scope with `repo/public_repo` is required.

Script requires authentication to github.com via SSH. [Directions here](https://help.github.com/articles/connecting-to-github-with-ssh/).

## Usage

```
usage: backport.py [-h] --repo {REPO_NAME} --orgn {ORGN_NAME} --token TOKEN --pr PR
                   [--branch BRANCH] [--debug] [--continue]
                   [--abort-before-push]

optional arguments:
  -h, --help            show this help message and exit
  --repo {REPO_NAME}
                        chainer or cupy
  --orgn {ORGANIZATION_NAME}
                        Organization name
  --token TOKEN         GitHub access token.
  --pr PR               The original PR number to be backported.
  --branch BRANCH       Target branch to make a backport
  --debug
  --continue            Continues the process suspended by conflict situation.
                        Run from the working tree directory.
  --abort-before-push   Abort the procedure before making an push. Useful if
                        you want to make some modification to the backport
                        branch. Use --continue to make an actual push after
                        making modification.
```

## Example

```shell
$ python backport.py --repo chainer --orgn Google --token abcdefghijklmn --pr 1234 --branch BRANCH_NAME
```

## How it works

Basically it follows this procedure:

1. Clone the target branch (e.g. `v5`) of the target repository (e.g. `chainer/chainer`) to a temporary directory.
2. Create a local temporary branch and cherry-pick the merge commit of the original PR.
3. Push it to the user repository.
4. Make a backport PR.


## In the case of conflict

If conflict has occurred during backporting, you have to resolve conflict yourself,
and rerun the command with additional `--continue` option.

```shell
$ python backport.py --repo chainer --orgn Google --token abcdefghijklmn --pr 1234 --branch BRANCH_NAME
...
Cherry-pick failed.
Working tree is saved at: /tmp/bp-FktJXG5R
Go to the working tree, resolve the conflict and type `git cherry-pick --continue`,
then run this script with --continue option.

$ cd /tmp/bp-FktJXG5R

$ <resolve conflict>

$ git cherry-pick --continue

$ python backport.py --repo chainer --orgn Google --token abcdefghijklmn --pr 1234 --branch BRANCH_NAME --continue
```
