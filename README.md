# Integration Testing for The Carpentries Workbench

Integration test status: [![Test template](https://github.com/zkamvar/workbench-integration-test/actions/workflows/main.yml/badge.svg)](https://github.com/zkamvar/workbench-integration-test/actions/workflows/main.yml)

This repository contains a matrix workflow that will download and test the
building of a select group of workbench repositories to ensure that they work.

At the moment, the build does not confirm anything beyond the fact that output
is created, but it could serve as a future framework for integration tests.


## Usage

The integration test for this repository will test the development versions of
The Workbench packages on specific repositories. This runs weekly or when needed.

### On GitHub

To use this repository, head over to ["Test Template" action in the "Actions" 
tab](https://github.com/carpentries/workbench-integration-test/actions/workflows/main.yml)
and then click the "Run Workflow" button to run the workflow on the current
versions of the workbench as they exist on the GitHub main branch. Check the
[options](#options) for more information on how to choose different versions of
the packages

### Via CLI

You can use the [GitHub CLI](https://cli.github.com) application to run this
workflow from your command line: 

```sh
gh workflow run main.yml \
--repo carpentries/workbench-integration-test \
-f sandpaper='carpentries/sandpaper' \
-f pegboard='carpentries/pegboard' \
-f varnish='carpentries/varnish' \
-f test-lesson='LibraryCarpentry/lc-git' \
-f reset=false
```

### Options

To reset the cached markdown files (e.g. rebuild everything from scratch,
you can use `reset: true`). This will be a checkbox in the GitHub interface.

You can choose a specific lesson to test by supplying it in the "additional 
lesson to test" box. This defaults to 
carpentries-incubator/managing-computational-projects

One feature of this test is that you can choose which versions of The Workbench
packages you would like to test with. If you want to test specific versions of
a package OR test specific branches or pull requests, you can specify them
using the [remotes syntax for
GitHub](https://remotes.r-lib.org/articles/dependencies.html#github):

```
# Default: main branch
carpentries/pegboard

# Select a specific pull requeset
carpentries/pegboard#132

# Select a specific branch
carpentries/pegboard@allow-overview-pages

# Select a tag
carpentries/pegboard@0.5.3

# Select the most recent release
carpentries/pegboard@*release

# Select a specific commit
carpentries/pegboard@c648bc5
```



