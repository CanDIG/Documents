# Contributing to CanDIG Repositories

:balloon: Thank you/Merci for helping CanDIG! We really appreciate it.

When contributing to this repository, please first discuss the change you wish to 
make via [issue] with the owners of this repository before making a change.


## TODO
- Add issue template in Github.
- Add [MAINTAINERS.md](MAINTAINERS.md)
- Add [GOVERNANCE.md](GOVERNANCE.md)
- Add code of conduct violator reporting email to EMAIL-COC.

## Getting Help

If you have a question about this project, please open a Github [issue] in this repo.

## Code of Conduct

This project and everyone participating in it is governed by the CanDIG [Code of Conduct].
By participating, you are expected to uphold this code. Please report unacceptable behavior
to EMAIL-COC.

## Certificate of Origin

Add a line to every git commit message:

```
Signed-off-by: Jane Smith <jane.smith@example.com>
```

If you set your `user.name` and `user.email` git configs, you can sign your
commit automatically with `git commit -s`.

Linux community uses a similar approach with a Developer Certificate of Origin (DCO).
This document was created by the Linux Kernel community and is a
simple statement that you, as a contributor, have the legal right to make the
contribution.

## Submitting a Pull Request

If you have a bugfix or an improvement, you can use the following steps to submit
that change.

1. Submit an [issue][issue] describing your proposed change.
2. Fork this repository to your own Github account where you will develop and test your code changes.
3. Ensure any install or build dependencies are removed before the end of the layer when doing a 
   build.
4. Update the [README.md](README.md) with details of changes.
5. Increase the version numbers in any documentation files and the README.md to the new version that this
   Pull Request would represent. The versioning scheme we use is [SemVer](http://semver.org/).
6. Submit a pull request against this repo's `develop` branch.
7. Your branch may be merged once all configured checks pass, including:
    - The branch has passed tests in CI.
    - A review from appropriate maintainers.
8. You may merge the Pull Request in once you have the sign-off of two other developers, or if you 
   do not have permission to do that, you may request the second reviewer to merge it for you.

## Commit Notes

Commit messages should be in the following format wherever possible.

```
Subject:

Problem:

Solution:

Validation:

Fixes: #[GitHub issue ID]
```

### Subject

- one line, <= 50 characters
- describe what is done; not the result
- use the active voice
- capitalize first word and proper nouns
- do not end in a period — this is a title/subject
- reference the GitHub issue by number

#### Example
 
```
BAD: added code so server now listens on port 443
GOOD: Introduce support for listening on port 443 (#420)
```

### Problem

Explain the context and why you're making that change.  What is the problem
you're trying to solve? In some cases there is not a problem and this can be
thought of as being the motivation for your change.

### Solution

Describe the modifications you've made.

### Validation

- Describe the testing you have done to validate your changes.
- Describe performace-related changes, if applicable.

Inspired by:

- PurpleBooth's: https://gist.github.com/PurpleBooth/b24679402957c63ec426
- Linkerd's: https://github.com/linkerd/linkerd2-proxy/blob/master/CONTRIBUTING.md


[issue]: https://github.com/CanDIG/candig-server/issues/new
[Code of Conduct]: CODE-OF-CONDUCT.md
