# The Rubin Build Engineering Team

```{abstract}
This document describes the Rubin Operations Build Engineering Team and its responsibilities.
```

## Introduction

During the Rubin construction phase the tasks associated with building the science pipelines software, enabling unit testing of that software, and distributing it, were the responsibility of the Science Quality and Reliability Engineering (SQuaRE) team before being given over to the Architecture team.
For operations the responsibilities were initially split between data engineering and the science pipelines team but they have now been unified into a distinct Build Engineering Team situated within the Data Abstraction division of Data Management.
The overall philosophy for builds and deployment and the development process can be found in {cite:p}`2018SPIE10707E..09J` and the [Rubin developer guide](https://developer.lsst.io).

## Responsibilities

The role of the Build Engineering Team is to facilitate releases of the LSST Science Pipelines software to the community and other parts of the Rubin organization.
This includes the support and evolutions of:

* Making formal science pipelines releases (working with the pipelines team on release notes and timing as well as the infrastructure for making the releases).
* The Conda python environment (e.g., the `rubin-env` meta package)
* The pipeline build system used by the developers (`sconsUtils`, EUPS, lint tooling, `lsstsw`, `lsstinstall`)
* The GitHub Actions and associated template packages for pipelines packages.
* The Python/C++ bindings such as Pybind11 or Nanobind.
* The CI/CD infrastructure (nightly, weekly, ticket branch releases, making docker containers, running AP verification jobs) on multiple architectures.

A detailed overview of the build system can be found in {cite:p}`DMTN-218`.

The BET can investigate new technologies for building or deploying the software such as:

* Changing from Docker containers to a new technology as driven by stakeholders.
* Using a completely different CI/CD system.
* Having alternate distributions mechanisms that do not use EUPS (such as Conda).
* Replacing SCons as the package build system.

As we head into the era of long-lived formal data releases there is an expectation that BET would be involved in any changes to how we coordinate branches and release strategies in Git.

## Stakeholders

The Build Engineering Team (BET) is a small but critical team that sits in the middle of many stakeholders.
The Science Pipeline team relies on BET to ensure that not only releases are made but also to ensure that they can build and deploy the software themselves as well as run it during data release processing.

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
* Developers and RSP users shall be able to select containers corresponding to daily or weekly or formal release versions of the pipeline software.
* Formal releases will be supported for extended periods such that a mechanism must be in place to allow containers to be rebuilt from release branches of the science pipelines code along with security patches applied to the Conda environment.

The supported computer architectures are formally listed in the developer guide.

As part of commissioning it is becoming clear that the delay from making a patch to a pipelines package and creating a container is too long.
A requirement to be able to make a new (potentially untested) container in less than half an hour is becoming desirable.
Additionally, a related requirement is the ability to make a container from a ticket branch such that it can be tested in the RSP or at the summit before merging.


## References

```{bibliography}
```
