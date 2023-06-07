# maintain.toml - machine readable description of a software project's state

## Rationale

Open source projects require maintainers to add new features, fix bugs, write documentation, write tests and much more.
Being committed to an open source project requires time and effort.
But depending on life situations, a maintainer might not be able to provide the needed attention to a project.
Even healthy software projects might start to stagnate and eventually get abandonned.

Modern software typically makes use of many components and dependencies.
You probably know about [XKCD 2347](https://xkcd.com/2347/) (if not, then you are one of the [lucky 10000](https://xkcd.com/1053/)).
It is important to keep an eye on your dependencies when developing and deploying software, because software [can have confirmed security issues](https://www.cve.org/).
But apart from confirmed security issues, you probably also want to make sure that the projects you depend on are well-maintained,
so that bugs in your dependencies get fixed and don't affect your software.

The issue is: It can be hard to tell if a project is still alive and well.
Even if there is recent commit activity in the software repository,
the maintainers might only have very little time to process bugs and security issues.

It is possible to communicate issues with the project in a README like this,
but when you depend on hundreds of software projects, it is not feasible to
review those unstructured READMEs on a regular basis. Therefore it makes sense
to have a file format that can be automatically processed so that developers can
more effectively determine where dependencies need to be supported.

## The maintain.toml

The maintain.toml is a simple declarative [TOML](https://toml.io/en/) file to describe the needs and state of a project.
In its most basic form, there are only 3 required fields and the remaining entries have reasonable default values:

```toml
version = "0.0.1"

[time]
timestamp = 2023-06-07
expiry = 2024-06-07
```

The `version` field describes the version of the `maintain.toml` standard.
It follows [semantic versioning 2.0](https://semver.org/spec/v2.0.0.html).

The `timestamp` field describes when `maintain.toml` was last updated
using the native date format supported by TOML.
Naturally, you could determine the same information also from the file in the git log or the local file system entry,
but the `timestamp` field has a special relationship to the `expiry` field:
They can be at most 1 year apart.
The `expiry` is a future date on which the `maintain.toml` expires.
That is, on the 7th of June 2024, the project is officially unmaintained.
If no time zone was specified for the TOML time, the time zone is assumed to be UTC+0.
The `expiry` field acts as a [dead man's switch](https://en.wikipedia.org/wiki/Dead_man%27s_switch)
and therefore the `maintain.toml` file should be updated on a regular basis.

There are more fields that can be set. For example:

```toml
version = "0.0.1"

[time]
timestamp = 2023-06-07
expiry = 2024-06-07
reminder = 30
timekeepers = ["github:jbethune", "unix:hacker"]
```

The `reminder` field indicates how many days before the `expiry` you want to receive notifications about the upcoming expiry.
These notifications can be implemented by GitHub and other services.
GitHub would notice that there is an entry `github:jbethune` in the list of `timekeepers` and show a notification to me the next time I log on to GitHub.
At the same time, GitHub would ignore the `unix:hacker` entry,
but a tool  that I run on my local linux machine could pick the entry up and
show a local notification to the `hacker` user.

The default value for `reminder` is 30 days. But for it to have any effect, there need to be entries in the `timekeepers` array (which is empty by default).

See the [maintain.toml] file of this repository for more fields, their descriptions and default values.

## Contributing/Standardization

As pointed out in the entry `development.specification` in the [maintain.toml],
I am happy to take suggestions on the file format. Please open an issue to discuss changes.
