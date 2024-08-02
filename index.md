# The Rubin Build Engineering Team

```{abstract}
This document describes the Rubin Operations Build Engineering Team and its responsibilities.
```

## Introduction

During the Rubin construction phase the tasks associated with building the science pipelines software, enabling unit testing of that software, and distributing it, were the responsibility of the Science Quality and Reliability Engineering (SQuaRE) team before being given over to the Architecture team.
For operations the responsibilities were initially split between data engineering and the science pipelines team but they have now been unified into a distinct Build Engineering Team (BET) situated within the Data Abstraction division of Data Management.
The overall philosophy for builds and deployment and the development process can be found in {cite:p}`2018SPIE10707E..09J` and the [Rubin developer guide](https://developer.lsst.io).

## Responsibilities

The role of the Build Engineering Team is to facilitate releases of the LSST Science Pipelines software to the community and other parts of the Rubin organization.
A detailed overview of the build system can be found in {cite:p}`DMTN-218`.
This includes the support and evolutions of:

### Making Formal Releases

A core responsibility of is making formal releases of the [LSST Science Pipelines](https://pipelines.lsst.io) {cite:p}`PSTN-019`.
This includes making the release builds (including release candidates), possibly applying backports of tickets, and working with the pipelines team to create release notes.
This will include all the support software for tagging repositories and making the release binaries and containers.

### Supporting the core environment

Currently conda-forge is used to provide the base software environment on which the pipelines software is built.
Conda-forge has the advantage of providing an entire environment including compilers and compiled libraries.
This is a significant improvement over the previous system where we had to support specific compilers on different operating system versions, have people use package manager tools to install dependent core libraries, and then have to track specific versions of third-party libraries through the EUPS "tar and package" mechanism of git repos containing tar files.

The BET must ensure that the conda `rubin-env` and associated add ons (such as `rubin-env-developer`) continue to work and are kept up to date as new versions of software are released that might require pinning.
This also means that older `rubin-env` versions continue to need to function to support existing formal software releases that may have been created months or years ago.
A strategy must be developed for supporting the formal releases attached to LSST Data Releases since those specific software releases will have large significance to the community and may have to be supported with security patches long after the data release has been made.

There is a policy in place for adding packages to `rubin-env` via RFC.

### Supporting package builds

The individual science pipelines packages must be built and the tooling for that is a core BET responsibility.
Currently this means supporting EUPS (such as `setup` and `eupspkg`) and `sconsUtils`.
This means integrating features like pytest, linting, and code coverage into the builds.

For some packages this also means supporting PyPI uploads and standard python `pip` tooling.
One impediment to using PyPI for everything is the `setuptools` interaction with C++ libraries (our packages include C++ libraries that must be standalone as well as Python wrappers) and this is an area that is open to exploration.

There are continual discussions as to how the build tooling can be modernized, and proposals have included cmake, bazel, and meson.
Additionally, there have been multiple investigations into supporting conda build to allow us to make releases directly as conda packages.

### GitHub Actions

Per-package CI is performed using GitHub actions.
There are a number of template actions that are supported by BET.
Additionally, there are more complex actions used by packages that can obtain dependencies without having to depend on pipelines code.
These actions can evolve over time and be integrated into BET processes.

### Python Bindings

Python is the primary language for interacting with the science pipeline software.
In the early years SWIG was used to provide the bindings to the C++ interfaces.
Then Pybind11 was adopted.
The BET supports and evolves the core technologies and can propose alternatives such as nanobind.

### Stack Builds

Since the LSST Science Pipelines consist of separate git repositories with defined dependencies, software tooling is required to build a complete release and ensure that the packages are built in the correct order.
BET currently enables this with the `lsstsw` tool.

Additionally, once package source and binary releases have been made the `lsstinstall` tool is provided to allow external users to install the software.
This also requires the use of `eups distrib install`.

There is also a "shared stack" script supported by BET to enable a single shared development tree to be kept in sync and regularly updated with every weekly release.

### Continuous Integration

It must be possible for developers to easily test that their ticket branches have not broken the build.
BET currently supports a Jenkins system to enable this and policy requires that Jenkins must pass before a ticket branch can be merged.

There are additional time-based jobs that run to ensure that nothing has broken.

### Package Distribution

BET is responsible for making regular releases of the science pipelines software.
The software is released as docker containers and as EUPS source and binary packages.
The containers are used by the Rubin Science Platform and Telescope and Site.
There is no requirement that binary packages must be released using EUPS if a better alternative is found.

Currently there are daily releases made with the Wednesday night containers being treated as more significant "weekly" releases.
We currently support Alma Linux 9 on Intel and ARM architectures and macOS on Intel and ARM, although we will certainly drop Intel macOS some time soon.

## Evolution

The BET can investigate new technologies for building or deploying the software such as:

* Changing from Docker containers to a new technology as driven by stakeholders.
* Using a completely different CI/CD system.
* Having alternate distributions mechanisms that do not use EUPS (such as Conda).
* Replacing SCons as the package build system.

As we head into the era of long-lived formal data releases there is an expectation that BET would be involved in any changes to how we coordinate branches and release strategies in Git and how we build packages and implement policies directed by the Data Management Change Control Board (DM-CCB).

## Stakeholders

The Build Engineering Team (BET) is a small but critical team that sits in the middle of many stakeholders.
The Science Pipeline team relies on BET to ensure that not only releases are made but also to ensure that they can build and deploy the software themselves as well as run it during data release processing.

The DM-CCB relies on BET to report on release status and to implement any changes on release strategy and policy.

The Telescope and Site software team use the Science Pipeline container as the core of the CSC containers that they deploy to the summit.
Any changes to Python major versions must be approved and coordinated by T&S as well as with the Science Pipelines team.

The Rubin Science Platform deploys the pipeline docker containers.
These containers form the primary interface used by the science community.
The RSP community, including the Community Science Team, expects to have access to containers from weekly releases, formal validated releases, and recent dailies.

Finally, science users who are not using the containers need to be able to install the software.
The requirement is that the software be installable to outside users but this system can change over time.

## External Support

Some build activities happen outside of BET.
These include:

* Build and distribution of binaries over [CernVM-FS](https://cernvm.cern.ch/fs/) and the creation of Apptainer containers by our colleagues at [CC-IN2P3](https://cc.in2p3.fr/en/).
* Supporting of the "shared stack" at the USDF by the pipeline developers.

## Requirements

There are no Service Level Agreements between BET and other parts of the Rubin organization.
Furthermore, there are no formal requirements for build metrics or deployment mechanisms written in {cite:p}`LSE-61`.

Informal assumed requirements are:

* Developers shall have a CI system to check their code before they merge it.
  This system should provide feedback as quickly as possible to minimize developer overhead.
* Developers shall be able to build and test the software on their own computers without requiring remote access to development systems aside from source code repositories.
* Developers and RSP users shall be able to select containers corresponding to daily or weekly or formal release versions of the pipeline software.
* Formal releases will be supported for extended periods such that a mechanism must be in place to allow containers to be rebuilt from release branches of the science pipelines code along with security patches applied to the Conda environment.

The supported computer architectures are formally listed in the developer guide.

As part of commissioning it is becoming clear that the delay from making a patch to a pipelines package and creating a container is too long.
A requirement to be able to make a new (potentially untested) container in less than half an hour is becoming desirable.
Additionally, a related requirement is the ability to make a container from a ticket branch such that it can be tested in the RSP or at the summit before merging.


## References

```{bibliography}
```
