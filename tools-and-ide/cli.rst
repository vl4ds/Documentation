=================
Command Line Tool
=================

What is a CLI?
^^^^^^^^^^^^^^
A CLI (command line interface) is a user interface to a computer's operating
system or an application in which the user responds to a visual prompt by typing
in a command on a specified line, receives a response back from the system, and
then enters another command.

----

Purpose
-------

A full blown Web IDE that is useful for a professional developer requires many
features. The required feature set for a professional developer to be
successful is fairly long, and often times quite personal to the individual. By
creating a SmartThings CLI, SmartThings enables these professional developers to use the
tools of their choice, while providing a lightweight client allowing them to
interface with the SmartThings platform.

----

Prerequisites
-------------

The CLI tool requires that you have a command prompt that is accessable on your
computer. This means you must have an application like *Terminal* on **OS (X)**
and **Linux** or the command prompt, *CMD* on **Windows**.

Supported Operating Systems:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
**Mac OS (X) (Beta)**

**Windows (Beta)**

**Linux (Alpha)**

.. note::

    The Linux binary should be compatible on any Linux system that meets the following requirements:

    .. code-block:: shell

        ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=2e39e3a5fe149d5ee73ec883d09ccd219252a2a5, not stripped

----

Installation
------------

Download
^^^^^^^^

Download the binary for your OS

**Windows**
`SmartThings Windows CLI <https://cdn-cli.smartthings.com/releases/latest/windows/stdev.exe>`_

Place the .exe file in your path (`tutorial <http://www.howtogeek.com/118594/how-to-edit-your-system-path-for-easy-command-line-access/>`_)

**OS X**
`SmartThings Windows CLI <https://cdn-cli.smartthings.com/releases/latest/osx/stdev>`_

Place the downloaded binary in a safe place, and add it to your system path. This
will make it available in all your terminal sessions.

Run the following command on the executable:
``chmod u+rwx stdev``

.. note::

    OS X security may remove execute privileges and quarantine file.  To fix:
    ``xattr -d com.apple.quarantine stdev``
    ``chmod u+rwx stdev``

**Linux**
`SmartThings Windows CLI <https://cdn-cli.smartthings.com/releases/latest/linux/stdev>`_

Place the downloaded binary in a safe place, and add it to your system path. This
will make it available in all your terminal sessions.

Run the following command on the executable:
``chmod u+rwx stdev``

Verify Installation
^^^^^^^^^^^^^^^^^^^

Executing the following command should produce the output observed below:

.. code-block:: shell

    $ stdev --version
    0.35.0
    $

.. note::

    Actual version number may differ from the above example.

Try running the following to see a list of all the available commands:

    ``stdev --help``

Optionally you can run the following to get help with a specific command:

    ``stdev <command> --help``

The commands available in the CLI will be detailed in the `Commands`_ section.

----

Setup
-----

To set up the CLI tool, you must initialize it by running the ``init`` command:

.. code-block:: shell

    $ stdev init
    ? Email/Username: you@youremail.com
    ? Password: *************
    ? Choose a location Home

    SmartThings Development CLI
    Config File:  <path_to_your_home_dir>/.stconfig
    Environment:  production
    Location:  Home
    Authenticated:  Yes
    Version:  0.35.0

    $

The CLI will prompt you first for you login information. These are the same
credentials that you use to log into the SmartThings platform.

Once authenticated, the CLI will ask you to choose a location to work with. You
can use the up and down arrows on the keyboard to select a location and hit *enter*
to choose it.

The CLI tool is now configured and ready to use.

----

Commands
--------

============================= ======================================
Command Name                  Description
============================= ======================================
:ref:`init`                   Initialize CLI to work with SmartThings.
:ref:`auth`                   Authenticate with SmartThings.
:ref:`location`               Choose a location.
:ref:`generate`               Generate new executable structure by answering a few questions. Will default creation directory to ./ if a path isn't supplied.
:ref:`save`                   Save an executable to your SmartThings account. Optionally include a path to the \*.src directory. Default to ./ if a path isn't supplied. Path may also be to an individual file, however this requires the executable to already exist in your SmartThings account.
:ref:`watch`                  Watch an executable for changes and save them immediately. Default to ./ if a path isn't supplied.
:ref:`publish`                Publish SmartApp/DeviceType located at [path]. Default to ./ if a path isn't supplied.
:ref:`update`                 Check for an update to stdev.
:ref:`info`                   Show current configuration information.
============================= ======================================

.. _init:

init
^^^^

**Usage:** init [options]

  Initialize CLI to work with SmartThings.

**Options:**

    -h, --help  output usage information

.. _auth:

auth
^^^^

**Usage:** auth [options]

  Authenticate with SmartThings.

**Options:**

    -h, --help  output usage information

.. _location:

location
^^^^^^^^

**Usage:** location [options]

  Choose a location.

**Options:**

    -h, --help  output usage information

.. _generate:

generate [path]
^^^^^^^^^^^^^^^

**Usage:** generate [options] [path]

  Generate new executable structure by answering a few questions. Will default creation directory to ./ if a path isn't supplied.

**Options:**

    -h, --help  output usage information

.. _save:

save [path]
^^^^^^^^^^^

**Usage:** save [options] [path]

  Save an executable to your SmartThings account. Optionally include a path to the \*.src directory. Default to ./ if a path isn't supplied. Path may also be to an individual file, however this requires the executable to already exist in your SmartThings account.

**Options:**

    -h, --help  output usage information

.. _watch:

watch [path]
^^^^^^^^^^^^

**Usage: watch [options] [path]**

  Watch an executable for changes and save them immediately. Default to ./ if a path isn't supplied.

**Options:**

    -h, --help  output usage information

.. _publish:

publish [path]
^^^^^^^^^^^^^^

**Usage:** publish [options] [path]

  Publish SmartApp/DeviceType located at [path]. Default to ./ if a path isn't supplied.

**Options:**

    -h, --help  output usage information

.. _update:

update
^^^^^^

**Usage:** update [options]

  Check for an update to stdev.

**Options:**

    -h, --help  output usage information

.. _info:

info
^^^^

**Usage:** info [options]

  Show current configuration information.

**Options:**

    -h, --help  output usage information

----

Common Scenarios
----------------

asdfasdfsad

----

FAQ
---

asdflaasdfasdf

----
