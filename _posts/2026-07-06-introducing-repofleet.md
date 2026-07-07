---
layout: post
title: "Introducing RepoFleet"
date: 2026-07-06 10:00:00 -0400
categories: [cli, git]
tags: [git, cli, repofleet, github]
excerpt: "RepoFleet is an issue-centered CLI that simplifies Git workflows across multiple repositories."
---
## Introducing RepoFleet: An Issue-Centered CLI for Multi-Repository Git Workflows

Working on a single repository is usually simple.

You create a branch, make your changes, push it, and open a pull request or merge request.

But modern software projects are often not always that simple. A single feature or bug fix may touch multiple repositories: a backend service, an admin panel, a shared library, deployment scripts, or a separate API project.

In that situation, the workflow becomes repetitive very quickly.

You may need to open several terminal tabs, switch between many directories, create the same issue branch in multiple repositories, fetch every repo one by one, check which repo has changes, and remember which branch belongs to which issue.

This is exactly the problem that RepoFleet tries to solve.

RepoFleet is an issue-centered CLI tool for managing Git workflows across multiple repositories.

Instead of thinking repo by repo, RepoFleet lets you think issue by issue.

You create one issue context, attach the related repositories to it, and then manage the Git workflow across all of them from one place.

The examples in this post are based on the current RepoFleet 0.6.0 workflow. This version makes the daily multi-repo flow smoother: `rf issue sync` is now focused on fetching remote refs, `rf issue status --go-to` can switch the selected repository to the issue branch automatically, `rf issue repo remove` handles checked-out issue branches more safely, and issue creation uses comma-separated repository selection such as `--repo service-a,service-b`.

## The Story Behind RepoFleet

The idea for RepoFleet came from a real problem I faced at work.

I was working at a company that had recently moved from a monorepo architecture to a multi-repository setup by splitting the codebase into separate repositories.

This change made the project structure cleaner in some ways, but it also introduced a new daily challenge for developers.

A single issue could now require changes across multiple repositories. For example, one task might involve a backend service, an admin application, a shared library, and deployment-related changes.

That meant I had to constantly switch between repositories, create matching branches, check status in different folders, fetch every repo separately, and remember which branch belonged to which issue.

After doing this repeatedly, I realized the workflow itself needed a tool.

That is how the idea for RepoFleet started.

I wanted a CLI that lets me manage work around the issue, not around individual repositories.

Instead of asking:

> Which repository am I working in?

RepoFleet starts from a better question:

> Which issue am I working on, and which repositories are part of it?

That small shift became the foundation of RepoFleet.

## Why RepoFleet?

When a task spans multiple repositories, the hard part is not usually Git itself.

The hard part is coordination.

For example, imagine you are working on issue `123`, and it requires changes in:

```text
service-a
service-b
admin-site
shared-library
```

Without a tool, you may do something like this manually:

```bash
cd service-a
git checkout -b fix/123-auth-fix

cd ../service-b
git checkout -b fix/123-auth-fix

cd ../admin-site
git checkout -b fix/123-auth-fix

cd ../shared-library
git checkout -b fix/123-auth-fix
```

Then later, you may need to check status, fetch remote refs, or switch between branches in every repository.

RepoFleet reduces that manual work by giving you a single issue context that knows which repositories are involved and which branches belong to that issue.

## The Main Idea: Issue-Centered Workflow

RepoFleet is built around one simple idea:

> A developer often works on an issue, not just on a repository.

A repository is still important, but the issue is the center of the workflow.

With RepoFleet, you can create an issue context like this:

```bash
rf issue create 123 --name authfix --description "fix token refresh" --kind bug --type fix
```

RepoFleet can then create the related branch across the selected repositories using your configured branch naming pattern.

For example, your branch pattern could be:

```text
{type}/{issue}-{name}
```

So the issue above could generate a branch like:

```text
fix/123-authfix
```

This keeps branch names consistent across repositories and makes it easier to understand what each branch belongs to.

## Command Overview

RepoFleet 0.6.0 uses this command structure:

```text
rf
├── workspace
│   ├── switch [name]              Switch to a workspace, or create one if it does not exist
│   ├── remove <name>              Remove a workspace
│   └── config                     View or update workspace configuration
│                                  --branch-pattern  set branch naming pattern
├── repo
│   ├── add <path>                 Add a repository to the current workspace
│   │                              Forge is auto-detected from the remote URL
│   │                              Use --forge to override when needed
│   ├── remove <name>              Remove a repository from the current workspace
│   └── list                       List repositories in the current workspace
└── issue
    ├── create <id>                Create an issue context
    │                              ID must be an integer
    │                              --name         short internal name, max 8 chars, no spaces
    │                              --description  short description
    │                              --kind         bug | feature | task | story
    │                              --type         feat | fix | chore | docs | refactor | test
    │                              --repo         limit to specific repos, comma-separated
    │                              --skip-branch  save context without creating a git branch
    ├── switch [id|name]           Switch to an issue interactively, by ID, or by name
    │                              --archived     include archived issues in the list
    ├── repo
    │   ├── add <repo-name>        Add a repo to the current issue
    │   └── remove <repo-name>     Remove a repo from the current issue
    ├── sync                       Fetch all remotes for every repo in the current issue
    ├── status                     Show the status dashboard for the current issue
    │                              --go-to        open an interactive selector and cd into a repo
    ├── remove <id>                Remove an issue context
    └── archive <id>               Archive a completed issue context
```

## Workspaces

RepoFleet uses workspaces to group repositories together.

A workspace is a logical collection of repositories that belong to the same area of work.

For example, you might have a default workspace for your main project, or separate workspaces for different clients, products, or teams.

You can switch to a workspace with:

```bash
rf workspace switch default
```

A default workspace is created automatically on first run, so you can start simply and organize more later.

You can also create and switch to a new workspace by using a new name:

```bash
rf workspace switch backend
```

Workspace names should use letters, numbers, hyphens, or underscores.

Once you are inside a workspace, you can add repositories:

```bash
rf repo add ~/code/service-a
rf repo add ~/code/service-b
rf repo add ~/code/admin-site
```

Then you can verify the repositories in the workspace:

```bash
rf repo list
```

This gives RepoFleet the list of repositories it should manage together.

## Branch Naming Pattern

One useful feature in RepoFleet is the ability to configure a branch naming pattern.

For example:

```bash
rf workspace config --branch-pattern "{type}/{issue}-{name}"
```

RepoFleet supports useful tokens such as:

```text
{workspace}
{issue}
{name}
{description}
{kind}
{type}
```

This makes the branch naming flexible enough for different teams and different workflows.

For example, you could use:

```text
{type}/{issue}-{name}
```

Result:

```text
fix/123-authfix
```

Or:

```text
{workspace}/{type}/{issue}-{description}
```

Result:

```text
backend/fix/123-fix-token-refresh
```

You can also view the current workspace configuration with:

```bash
rf workspace config
```

The goal is not to force one branch naming style. The goal is to make the naming predictable and automatic.

## Creating an Issue Context

After adding repositories to a workspace, you can create an issue context:

```bash
rf issue create 123 --name authfix --description "fix token refresh" --kind bug --type fix
```

The issue ID must be an integer.

The `--name` value is optional, but when you use it, it should be short: max 8 characters and no spaces.

This creates the issue context and prepares the related branches.

You can also create an issue only for specific repositories:

```bash
rf issue create 789 --name apiwork --repo service-a,service-b
```

This is useful when not every repository in the workspace is part of the task.

If you already have branches and only want RepoFleet to track the current state, you can use:

```bash
rf issue create 456 --name dash --skip-branch
```

With `--skip-branch`, RepoFleet saves the current checked-out branch for each repository instead of creating new branches.

## Checking Status Across Repositories

One of the most useful commands is:

```bash
rf issue status
```

This gives you a dashboard for the current issue across all related repositories.

Instead of running `git status` in every folder manually, you can see the current state from one place.

RepoFleet shows useful information such as:

```text
Repo
Checkout
Commit
Age
HEAD±
```

This helps answer common questions quickly:

- Which repositories are part of the current issue?
- Which branch is checked out in each repository?
- Does this repo have local changes?
- Is the branch ahead or behind?
- How old is the latest commit?

For multi-repo work, this kind of overview saves time and reduces mistakes.

## Interactive Go-To Mode

RepoFleet also supports an interactive mode for jumping into a repository:

```bash
rf issue status --go-to
```

This opens an interactive selector.

After selecting a repository, RepoFleet can change your terminal directory into that repo.

In 0.6.0, this flow is smarter. If the selected repository is not currently on the issue branch, RepoFleet can switch it to the issue branch automatically. Repositories that need a branch switch are shown with a branch indicator in the selector.

On first run, RepoFleet sets up shell integration automatically. When prompted, run the command it shows you, such as:

```bash
source ~/.zshrc
```

After that, the `--go-to` flow feels more seamless because it can move your current shell into the selected repository.

## Switching Between Issues

Developers often work on more than one task.

Maybe you need to pause one issue, review another, and then return to the previous one.

RepoFleet supports issue switching:

```bash
rf issue switch
```

This lets you pick an issue interactively.

You can also switch directly by issue ID:

```bash
rf issue switch 123
```

Or by short internal name:

```bash
rf issue switch authfix
```

To include archived issues in the interactive list, use:

```bash
rf issue switch --archived
```

This makes it easier to move between active tasks without manually remembering every branch in every repository.

## Adding or Removing Repositories from an Issue

Sometimes an issue starts small, but later you realize another repository is also affected.

RepoFleet supports adding a repository to the current issue:

```bash
rf issue repo add service-c
```

If needed, RepoFleet creates or switches to the correct branch for that issue.

You can also remove a repository from the issue:

```bash
rf issue repo remove service-c
```

RepoFleet is careful with branch deletion. It does not delete protected branches like `main` or `master`, and it warns when there are uncommitted changes.

In 0.6.0, repository removal is safer when the issue branch is currently checked out. RepoFleet can switch to `main` or `master` first, then delete the issue branch only when it is clean and safe to do so.

## Syncing Work

Keeping multiple repositories up to date can be annoying.

RepoFleet provides:

```bash
rf issue sync
```

In 0.6.0, this command fetches all remotes for every repository in the current issue.

That means it keeps your remote refs up to date without automatically rebasing your work.

This is useful because fetching is safe and predictable. You can review the status first, then decide whether you want to rebase, merge, or make another Git change manually.

## Archiving or Removing Completed Issues

When the work is done, you can archive the issue context:

```bash
rf issue archive 123
```

This keeps your active issue list clean while still allowing completed work to be stored separately.

If you want to remove the issue context entirely, use:

```bash
rf issue remove 123
```

Removing an issue context does not delete Git branches.

## Installation

RepoFleet can be installed on macOS or Linux using Homebrew:

```bash
brew install mehranzand/tap/repofleet
```

On Windows, it can be installed with Scoop:

```bash
scoop bucket add mehranzand https://github.com/mehranzand/scoop-bucket
scoop install repofleet
```

You can also build it from source:

```bash
git clone https://github.com/mehranzand/repofleet
cd repofleet
go build -ldflags="-X main.version=dev" -o rf ./cmd/repofleet
```

RepoFleet requires Go 1.22+ when building from source.

## Updating RepoFleet

If you installed RepoFleet with Homebrew, update it with:

```bash
brew update
brew upgrade repofleet
```

If you installed it with Scoop, update it with:

```powershell
scoop update
scoop update repofleet
```

Then confirm the installed version. For this article, the commands are based on version 0.6.0:

```bash
rf -v
```

## Example Workflow

Here is a simple end-to-end example.

First, create or switch to a workspace:

```bash
rf workspace switch default
```

Add repositories:

```bash
rf repo add ~/code/service-a
rf repo add ~/code/service-b
rf repo add ~/code/admin-site
```

Configure the branch naming pattern:

```bash
rf workspace config --branch-pattern "{type}/{issue}-{name}"
```

Create an issue context:

```bash
rf issue create 123 --name authfix --description "fix token refresh" --kind bug --type fix
```

Or create it only for selected repositories:

```bash
rf issue create 123 --name authfix --description "fix token refresh" --kind bug --type fix --repo service-a,admin-site
```

Check status:

```bash
rf issue status
```

Jump into a repo interactively:

```bash
rf issue status --go-to
```

Fetch remote refs for all repositories in the current issue:

```bash
rf issue sync
```

Switch to another issue:

```bash
rf issue switch
```

Archive the issue when finished:

```bash
rf issue archive 123
```

## Who Is RepoFleet For?

RepoFleet is useful for developers who often work across multiple repositories.

It can help when:

- your feature touches more than one service
- your team uses microservices
- your frontend and backend live in separate repositories
- your issue requires changes in shared libraries
- you need consistent branch naming
- you want a simple dashboard for multi-repo work
- you switch between issues often
- you want to reduce repetitive Git commands

RepoFleet is not trying to replace Git.

It is a small layer on top of Git that helps organize multi-repository work around issues.

## Who Is RepoFleet Not For?

RepoFleet may not be necessary if you only work in one repository.

It may also be less useful if your team already works in a monorepo and most tasks stay inside that single repository.

The tool is mainly designed for developers who regularly deal with multiple repositories and want a cleaner way to manage that workflow.

## Final Thoughts

RepoFleet started from a practical problem: working across multiple repositories creates too much manual coordination.

The goal is to make that workflow simpler.

Create one issue context.

Connect the repositories.

Create consistent branches.

Check status from one place.

Jump into the right repo when needed.

Fetch all related repos safely.

Switch between issues easily.

Archive when done.

That is the core idea behind RepoFleet.

If you often work with multi-repository tasks, RepoFleet can help you keep your Git workflow cleaner, faster, and easier to manage.

[RepoFleet on GitHub](https://github.com/mehranzand/repofleet)
