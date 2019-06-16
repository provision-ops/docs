# Provision Ops Planning

This document is being prepared by @jonpugh. I will write some things in the first person until questions are answered.

## Current Status

The 4.x branch of [Provision CLI](https://github.com/provision-ops/provision) was an attempt at rewriting Aegir concepts in Symfony Console. I don't think we should continue on `4.x`. I think we *should* tag a `4.0.0` of the Provision CLI, then immediately mark the branch as unsupported. 

However, after almost 2 years, it's become clear that following the old model is a huge blocker to future development.

## Path Forward?

We have two options, I would like feedback from the community:

### `5.x`

Advantages: 

- Clearly indicates a continuation of "Aegir" system. 
- Clearly demonstrates that the system has been in production use for some time.

Disadvantages: 

- Much legacy code, tools, and other things in the repository woud need cleanup/removal. 
- Implies a tie to legacy Aegir model. 
- Implies we will have an upgrade path. 
- Being associated with Aegir shapes expectations, good and bad.

### `1.x`

Advantages: 

- Brand new codebase for a modern Symfony CLI project.
- CLI is relatively simple: mostly includes other components. 
- Clean slate allows for much more rapid development. 
- Remove conflict with "provision" namespace on Drupal.org. "provision" was never a CLI anyway.
- Version `7.x-3.x` doesn't make sense.
- Potentially gain maintainers from being a fresh start.

Disadvantages: 

- Lose project history.
- Potentially lose Aegir maintainers and users.

I'm strongly leaning towards `1.x`.

## Provision Ops Ecosystem

The next generation of Provision Ops aims to be a suite of simple, decoupled tools that make server management and site development easier for software in any language.

### Design Goals

The design goals of the Provision Ops tools are aligned with [Unix Philosophy](https://en.wikipedia.org/wiki/Unix_philosophy#Origin) (Emphasis Added):

1. Make each program do one thing well. To do a new job, *build afresh rather than complicate old programs* by adding new "features".
2. Expect the output of every program to become the input to another, as yet unknown, program. *Don't clutter output with extraneous information*. Avoid stringently columnar or binary input formats. Don't insist on interactive input.
3. Design and build software, even operating systems, to be tried early, ideally within weeks. *Don't hesitate to throw away the clumsy parts and rebuild them*.
4. Use tools in preference to unskilled help to lighten a programming task, even if you have to *detour to build the tools and expect to throw some of them out* after you've finished using them.

By following the Unix Philosophy, we can reach the broadest number of users.

Expanding on that, Provision Ops tools should follow these principles as well:

1. Each component should be useful in it's own right, have multiple ways to be used, and designed in such a way that it can be used with other tools or installed in other hosting or CI environments.
2. Components should be designed in such a way that it wouldn't be impossible to reimplement in other languages. For example, we will soon have a `yaml-tests` command written in bash.
3. Components should follow a similar user experience design, whether they provide a visual interface or a shell interface.

## ProvisionOps Components

### Power Process

- Runs any shell command in a more user friendly and standardized way.
- Provides Symfony Process-compatible class.
- Clearly deliniates between tool output and command output.
- Flexible outputting options for inline output, file output, remote logging, etc. (@TODO)
- Saves metadata and logs for every command to a simple YML format. (@TODO)

### Yaml Tests

- Provides a single command to run a suite of tests, as defined in a tests.yml file.
- Designed to work for local development and in CI.
- Output is designed for optimal readability. 
- Each test can post commit status back to GitHub API to report pass or failure. Great for CI.
- Errors are posted to GitHub on the commit itself as a comment.

### Future

Yaml Tests and Power Process were the inception of the new ProvisionOps tools.

The Yaml Tests tool proved so useful, it was leveraged for not just testing but production deployment for cms.va.gov only 24 days after it was written. See this public PR: https://github.com/department-of-veterans-affairs/va.gov-cms/pull/348

Tentative plan is:

- Create a "Yaml Jobs" or similarly named package that provides the basis for the individual "tests".
- Create a "Yaml Deploy" package that provides jobs for deployment and integrates with Deployment APIs like GitHub's.
- Refactor the "Yaml Tests" package to provide the test-specific command and the commit-status integration, and leverage the "Yaml Jobs" package.
