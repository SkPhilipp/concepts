# Concepts

This repository contains a list of ideas I would like to see realized, but haven't had the time to implement myself.

### Conflict Warning

An IDE integration which warns through a visual indicator when you're modifying a file that that file's changes are likely to cause a conflict. Not only
with the base branch but especially with other branches that are in active development.

### Plain Docker over Earthly

Earthly is a tool with a syntax similar to Dockerfiles. Its capabilities of caching and parallelization are implemented in Docker through multi-stage
Dockerfiles.

A reference implementation of this exists for release-engineers/y at https://github.com/release-engineers/y/blob/feat/multi-stage/Dockerfile.

### Pull-based Content

A common practice for repositories is a push-based approach where the repository owner decides when a release is created. This requires a repository owner to
perform manual labor to either manually perform this or to automate this process.

An alternative approach would be pull-based; where a repository consumer can choose a construct a release from any point in the project's history.
Following a standardized layout, tooling could be built around this to make it easier to consume.

| Artifacts    | Content Source              ||--------------|-----------------------------|| Packages     | Dockerfile target           || Docker Image | Dockerfile target (default) || Change log   | Git Metadata                || Releases     | Git Metadata                |### Git Repository Management Interface

A library to more easily work with GitHub repositories and Git. This should be able to easily parse and construct GitHub URLs in various formats. Moreover, it
should be able to perform local Git operations as well as operations using the remote repository management system.

For example:
- Parsing from a GitHub organization and repository name combination.
- Creating a new branch locally.
- Creating a new branch on the remote repository without owning a copy of the repository locally.

### Bulk Patching

A tool to apply a patch to multiple repositories within an organization, in an easily repeatable and idempotent manner.

Preferably with a user interface where users can apply patches to their own repositories either directly or through a pull request.

### Git Development to Master

A workflow which automatically pushes changes that have passed CI to the master branch. This would keep that branch entirely green.

### Markdown Links

A tool which runs [markdown-link-check](https://github.com/tcort/markdown-link-check) or a similar solution to check for dead links in a repository.
Ideally, for links pointing to repositories it should be able to locate how files were moved and update the link accordingly. So that it would not
just create a PR or warn that links are broken, but also fix them.

### Codeowners

For larger organizations with loads of repositories containing external contributions it can be difficult to keep track of who is responsible for
what. Ideally I'd have a tool which ensures that each repository at least contains a CODEOWNERS file with a default entry covering the repository.
Such a tool might be able to view the collaboration graph of a repository and make a better guess as to who should be the default owner than someone
who is trying to guess from the outside.

Obviously this should also fix broken CODEOWNERS files :^) (Happens in GitHub when a GitHub Team gets renamed or disbanded.)

### GitHub Actions Pinning

GitHub Workflows and GitHub Actions can refer to other GitHub (Reusable) Workflows or GitHub Actions by Git reference, which can be a branch, tag or a
commit. Usually what GitHub appears to recommend is you refer to a floating tag (e.g. `uses: actions/checkout@v2`) and with semantic versioning you
would get all non-breaking changes for free, and then you can call it a day. For GitHub Actions by GitHub themselves or internal actions that's
probably fine but for external actions you might want to pin to a specific commit if you can't trust the author to have the same level of security for
their GitHub account as GitHub themselves or your organization does; they might some day have malicious code pushed to their repository.

As such, I would like a tool which can;

- Have configured which GitHub organizations you trust (i.e. your own, and GitHub).
- Pin untrusted GitHub Workflows and GitHub to a commit. (In a way that Renovate can work with it)

### Git Tag Policy

I like consistency across the board, and you the reader probably do too; For example, when you're working with a GitHub Action using semantic
versioning as its tagging scheme, you'd expect that when the latest version (`v1.2.3`) gets released, that the floating tag `v1` points to it.
Developers usually have implicit assumptions about how Git tags are used, especially for projects claiming to use semantic versioning. When these
assumptions are broken it can lead to confusing issues but more importantly when these implicit rules are followed you can then build powerful
automation on top of it. I've listed some of my assumptions;

- the latest of `(major).(minor).(patch)` exists as `(major).(minor)` for each major & minor version.
- the latest of `(major).(minor)` exists as `(major)` for each major version.
- when a `(major)` exists, a corresponding `(major).0` exists
- when a `(major).(minor)` exists, a corresponding `(major).(minor).0` exists
- when a `(major).(minor).(patch)` exists, all patch versions `0` through `patch` exist
- when a `(major).(minor).0` exists, all minor versions `0` through `minor` exist
- when a `(major).0.0` exists that all major versions `0` through `major` exist

When fully automated, those rules _usually_ tend to be true. What I'd like to see is a tool which can check for repositories and tags that violate
these rules, and then either fix the tags or let the maintainers know.

### GitHub Release Policy

Same as above, but for GitHub Releases.

### 'action-push-or-pull'

Sometimes a CI/CD process might want to push changes to a repository. Examples include Maven Release plugins, GitOps automation, mutation testing
and so on. Running a git add commit and push is not that exciting, but it can be difficult to handle all the things that can possibly go wrong.
Anything can happen to a repository during your CI/CD, so I'd like an action to make this a bit easier;

This GitHub Action should be able to Git push local changes to a remote, with;

- Option to configure `git` with the user and email of the GitHub Token being used when not configured.
- Option to retry for some amount of times.
- Option to rebase when pushing fails.
- Option to submit changes through a pull request (on its own branch) when pushing does fail.
- Option to enable auto-merge or any created pull requests when available.

### ArgoCD Linter

A linter for ArgoCD Applications that checks for anything that could possibly go wrong before you apply an ArgoCD Application. There is a functioning
proof of concept at [release-engineers/lint-argocd-application](https://github.com/release-engineers/lint-argocd-application) that contains some
cool features, but ideally I would like to see even more;

- [x] YAML integrity checks
- [x] Helm integrity checks
- [ ] Kustomize integrity checks
- [x] Kubectl dry-run of the resources generated by Helm
- [ ] Kubectl dry-run of the resources generated by Kustomize
- [ ] Check for CRDs in resources generated (notably when combined with prune)
- [ ] Check for overlap between resources generated and existing resources

### GitHub Workflow Dependency Tree

[`mvn dependency:tree`](https://maven.apache.org/plugins/maven-dependency-plugin/tree-mojo.html) but for GitHub Workflows. Large GitHub Workflows
can be difficult to interpret, especially for cases with multiple workflows, reusable workflows, actions which refer to other actions and so on.
This should be able to generate a dependency tree for a GitHub Workflow, so you can easily see what a workflow depends on.

Notably I'd like this to display for each dependency;

- Where they are referenced from.
- What versions are currently in use.
- What versions are available. (i.e. latest major, latest minor for current major.)

### CLI Feedback Library

[`az feedback`](https://learn.microsoft.com/en-us/cli/azure/reference-index?view=azure-cli-latest#az-feedback) but as an easy to integrate library
for your own CLI tools. Ideally this would show on `--help`, and any `--help` should always link to where you can find the source code of a CLI tool.

### Linter to Sonar Plugin Wrapper

I want to see my GitHub Workflows and Bash scripts in SonarQube with the linters I like, however, proper SonarQube support for them is nowhere to be
found. Having to set up a different CI/CD pipeline or GitHub's super-linter for every non-Java type of repository is not ideal.

I'd like to see a tool which you could give a linter (or Docker image) along with a way to read that linter's results, and it would spit out a
SonarQube plugin for it. SonarQube plugins are Java-based and the enthusiasm for writing them is unfortunately not that high. Ideally this would
release a new version of the plugin whenever the underlying linter releases a new version.

### GitHub Action Wrapper

Same as above, but for GitHub Actions.

### Prettify for a Repository

My own repositories should look cool, right? I have like 50 of them, and I'd like to see a tool which can make my readme look flashy;

- Add Shields
- Add a logo (AI generated obviously)
- Add a table of contents
- Configure Renovate to keep it up to date
- Configure Dependabot to keep me up to date
- Ensure it has my favorite OSS license

### GitHub Workflow Testing Suite

Although [nektos/act](https://github.com/nektos/act) exists, it's not a full replacement for GitHub Actions. Reason being is that CI/CD processes
interact with every system imaginable and have tons of internal and external dependencies which can become so difficult to mock that past some point
it's more time efficient and more realistic to test workflows manually. I'd like to see a tool which can;

- Take a branched version of a GitHub Workflow, GitHub reusable Workflow, or GitHub Action.
- Merge it into your favorite dummy repository that most closely resembles real repositories.
- Run the workflow with actual GitHub events (i.e. make a real pull-request when that's the workflow trigger).
- Verify the workflow did what it was supposed to do.

### Minimal Library for Timed Events

I've seen many implementations of over-time events, but they're usually implemented in a manner that is not very flexible, not performant and not possible
to share over a network.

Although more related to a previous endeavour, EffortGames, I'd still like to see a library which can;

- Queue an event of a given type at a given time.
- Move an already queued event forward in time.
- Move an already queued event backward in time.
- Retrieve events of a given type at the current time.
- Retrieve events of a given type at the current and all future times.
- Tick forward by one time increment.
- Tick forward to a time increment.

Which should contain reference implementations for;

- Changes in a value over time.
- Toggles of values over time.

And how to serialize and deserialize these queued changes.
