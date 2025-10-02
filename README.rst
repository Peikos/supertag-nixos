About this fork (hic sunt dracones)
===================================

This is a very thin fork of supertag_, which is archived and appears to be unmaintained. My contribution consists of getting the code to run on NixOS in 2025. I know nearly nothing about Fuse and am a beginner for Nix; the main purpose of this fork is to document my steps and save you some dead-ends.

* First attempt was using the AppImage and instructions provided by `Timothy Miller`_. This resulted in permission errors from ``fusermount``.
  
* Second step was to build from source using an adapted Nix Flake from `Daniel (@cpu)`_. After adding the ``fuse``, ``dbus`` and ``sqlite`` dependencies, this gave build errors on the ``fuse-sys`` subcrate in the lines of ``"__atomic_wide_counter_struct_(unnamed_at_/nix/store/gf3wh0x0rzb1dkx0wx1jvmipydwfzzd5-glibc-2_40-66-dev/include/bits/atomic_wide_counter_h_28_3)" is not a valid Ident``.
  
* This could be mitigated by boosting the bindgen version in the ``fuse-sys`` dependencies to at least 0.62.0. I did not try a later version than that in order to minimise further changes needed.
  
* Finally, some minor modifications to the ``fuse-sys`` code needed to be made:
  
  - It appears ``statvfs`` data-structure has been changed since the development of this crate, requiring ``f_type`` to be included and removing one ``0`` padding. I left ``f_type`` as ``0`` for now, so the in-memory representation would not change.
    
  - ``FuseOperations`` required a zero-sized array ``_bitfield_align_1``, which I added.
    
  - A ``invalid_reference_casting`` warning has become a blocking error; for now I just opted to ignore this, which seems to work 🤞.
    
* Now it compiles. Let's ship it.

*As you can see, this is not a fork, merely the application of a few steps to get the code running with the least modifications possible. If someone does decide to pick up this project: hope this helps.*

.. _supertag: https://github.com/amoffat/supertag
.. _Timothy Miller: https://timothymiller.dev/posts/2024/installing-appimages-as-first-class-citizens-in-nixos/
.. _Daniel (@cpu): https://github.com/cpu/rust-flake

Usage
-----

Use ``nix develop`` to enter a development shell, then use regular ``cargo`` commands to build, run or install.

Original README
===============

.. image:: https://raw.githubusercontent.com/amoffat/supertag/master/logo/logo.gif
    :target: https://amoffat.github.io/supertag/
    :alt: Logo

|

.. image:: https://img.shields.io/travis/amoffat/supertag/master.svg?style=flat-square
    :target: https://travis-ci.org/amoffat/supertag
    :alt: Build Status
.. image:: https://img.shields.io/badge/Documentation-v0.1.4-brightgreen?style=flat-square&logo=read-the-docs&logoColor=white&color=1a6cff
    :target: https://amoffat.github.io/supertag/
    :alt: Docs

|

Supertag is a tag-based filesystem, written in Rust, for Linux and MacOS. It provides a tag-based view of your files by
removing the hierarchy constraints typically imposed on files and folders.
In other words, it allows you to think about your files not as objects stored in folders, but as objects that can be filtered by folders.

.. image:: https://raw.githubusercontent.com/amoffat/supertag/master/images/intersection-opt.gif
    :alt: Intersection

Installation
============

Linux
-----

.. code-block:: bash

    curl -Ls https://github.com/amoffat/supertag/releases/latest/download/supertag-x86_64.AppImage > tag
    sudo install tag /usr/local/bin

Mac
---

.. code-block:: bash

    brew install amoffat/rnd/supertag
