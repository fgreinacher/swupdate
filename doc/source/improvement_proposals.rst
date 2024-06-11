.. SPDX-FileCopyrightText: 2013-2021 Stefano Babic <stefano.babic@swupdate.org>
.. SPDX-License-Identifier: GPL-2.0-only

=============================
Proposals to improve SWUpdate
=============================

Please take into account that most of the items here are *proposals*.
I get some ideas talking with customers, some ideas are my own thoughts.
There is no plan when these features will be implemented - this depends
if there will be contribution to the project in terms of patches or
financial contributions to develop a feature.

Thanks again to all companies that have supported my work up now and to
everybody who has contributed to the project, let me bring SWUpdate
to the current status !

Main goal
=========

First goal is to reach a quite big audience, making
SWUpdate suitable for a large number of products.
This will help to build a community around the project
itself.

Core features
=============

Support for OpenWRT
-------------------

OpenWRT is used on many routers and has its own way for updating that is not power-cut safe.

Software-Software compatibility
-------------------------------

SWUpdate has from the early stage a hardware to software compatibility check. In case
software is split in several components (like OS and application), it is desirable to have
a sort of software compatibility check. For example, SWUpdate verifies if a component
(like an application) is compatible with a runningOS and reject the update in case of
mismatch.

Support files bigger than 4GB
-----------------------------

SWUpdate currently uses CPIO to pack updates in the 'newc' and 'crc' formats.
These formats limit single files to 4GB - 1byte in size, which could become a
problem as update size grows.

Parser
======

SWUpdate supports two parsers : libconfig and JSON. It would be nice if tools can
be used to convert from one format to the other one. Currently, due to some specialties
in libconfig, a manual conversion is still required.

Fetcher and interfaces
======================

- No plan

Tools and utilities
===================

- No plan

Lua
===

- API between SWUpdate and Lua is poorly documented.
- Store in SWUpdate's repo Lua libraries and common functions to be reused by projects.

Handlers:
=========

New Handlers
------------

Users develop own custom handlers - I just enforce and encourage everyone
to send them and discuss how to integrate custom handler in mainline.

Some ideas for new handlers:
        - FPGA updater for FPGA with Flash
        - Package handler to install packages (ipk, deb)
          Packages can be inserted into the SWU and the atomicity is
          guaranteed by SWUpdate.
        - Lua handlers should be added if possible to the project
          to show how to solve custom install.

Handlers installable as plugin at runtime
------------------------------------------

The project supports Lua as script language for pre- and postinstall
script. It will be easy to add a way for installing a handler at run-time
written in Lua, allowing to expand SWUpdate to the cases not covered
in the design phase of a product.

Of course, this issue is related to the security features: it must be
ensured that only verified handlers can be added to the system to avoid
that malware can get the control of the target.

Current release supports verified images. That means that a handler
written in Lua could be now be part of the compound image, because
a unauthenticated handler cannot run.

Support for BTRFS snapshot
--------------------------

BTRFS supports subvolume and delta backup for volumes - supporting subvolumes is a way
to move the delta approach to filesystems, while SWUpdate should apply the deltas
generated by BTRFS utilities.

Security / Crypto engines
=========================

- add support for asymmetryc decryption
- rework support for crypto engine - let possible to load multiple libraries at
  the same time. Currently, there is support for openSSL, WolfSSL and mbedTLS.
  However, WolfSSL are mising together. There should be a way to select one or more 
  libraries and independently the algorithms that SWUpdate should support.
  Some hacks are currently built to avoid conflicts (pkcs#7 and CMS are the same
  thing, but supported by different libraries), and they should be solved.
- add more algorythms for decryption, as AES-CTR can be very useful to decrypt
  chunks in delta updates.
- Support for TPM2 to store secrets (requires rework above).

Back-end support (suricatta mode)
=================================

Back-end: responsiveness for IPC
--------------------------------

Suricatta is implemented as process that launches functions for the selected module.
This means that the IPC does not answer if Suricatta is doing something, specially if it is
downloading and upgrading the system. This can be enhanced adding a separate thread for IPC and of course
all required synchronization with the main modules.

Back-end: check before installing
---------------------------------

In some cases (for example, where bandwidth is important), it is better to check
if an update must be installed instead of installing and performs checks later.
If SWUpdate provides a way to inform a checker if an update can be accepted
before downloading, a download is only done when it is really necessary.

Back-end: hawkBit Offline support
---------------------------------

There are several discussions on hawkBit's ML about how to synchronize
an offline update (done locally or via the internal Web-server) with
the hawkBit's server. Currently, hawkBit thinks to be the only one
deploying software. hawkBit DDI API should be extended, and afterwards
changes must be implemented in SWUpdate.

Back-end: support for generic down-loader 
-----------------------------------------

SWUpdate in down-loader mode works as one-shot: it simply try to download a SWU
from a URL. For simple applications, it could be moved into `suricatta` to detect
if a new version is available before downloading and installing.

Test and Continuous Integration
===============================

The number of configurations and features in SWUpdate is steadily increasing and
it becomes urgent to find a way to test all incoming patch to fix regression issues.
One step in this direction is the support for Travis build - a set of configuration
files is stored with the project and should help to find fast breakages in the build.
More in this direction must be done to perform test on targets. A suitable test framework
should be found. Scope is to have a "SWUpdate factory" where patches are fast integrated
and tested on real hardware.

Documentation
=============

Documentation is a central point in SWUpdate - maintaining it up to date is a must in this project. 
Help from any user fixing wrong sentence, bad english, adding missing topics is high
appreciated.