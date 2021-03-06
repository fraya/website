*********************
Single File Libraries
*********************

==============  =============================================
DEP #:          6
Type:           Standards Track
Author:         Carl Gay
Status:         Draft
Created:        12-Feb-2013 (Darwin's birthday)
Last-Modified:  17-Feb-2013
Post-History:   17-Feb-2013 hackers@lists.opendylan.org
Dylan-Version:  2012.1
==============  =============================================

.. contents:: Contents
   :local:

Revision History
================

The revision history of this document is available on GitHub:
https://github.com/dylan-lang/website/commits/master/source/proposals/dep-0006-single-file-library.rst

Abstract
========

Open Dylan currently requires users to create three source files to
define a simple library like hello-world:

#. A project file (i.e., .lid file) to describe source files to
   include, link options, etc.

#. A library definition source file, often named library.dylan.

#. At least one source file for main program logic, in a module
   defined by the library definition.

This DEP proposes a standard way to define a Dylan library in a single
source file.  This DEP targets implementations of Dylan but does not
propose a change to the Dylan language itself.  In particular it is
proposed as an enhancement for Open Dylan.

Rationale
=========

The three files described above are a lot of overhead for new users
who want to get a feel for the language, who are generally used to
creating a single file and running it directly, perhaps after
compiling it.  A simpler setup would provide a better initial
experience for new users, and would help to make Dylan more attractive
for "scripting" purposes.  The mantra has been that Dylan was designed
for large projects, but there's no reason it can't also excel at
scripting, with this change and the right set of support libraries.

Note that even though the ``make-dylan-app`` program will generate a
skeleton project with three files (and a registry), this does not
remove the need to jump back and forth between the library definition
file and the main source file as you figure out which modules you need
to use.  There is also the issue of new users figuring out that
``make-dylan-app`` exists in the first place.

There are plenty of libraries that are small enough to comfortably fit
in a single file.  Of the existing libraries in `the dylan-lang github
repository <https://github.com/dylan-lang>`_, at least ten could
comfortably fit in a single file.

Having a library in a single file simplifies sharing of examples and
small tests.  Indeed, one of the motivations for this feature was to
be able to contribute Dylan solutions for the `The Computer Language
Benchmarks Game <https://benchmarksgame-team.pages.debian.net/benchmarksgame/>`_, which
prefers to receive one file per solution.  Obviously it is possible to
explain to them about LID files and how to unpack and compile a
multi-file solution for each problem, but it is more complex than it
needs to be.

This DEP is only the first step to adding better support for
scripting.  More is needed, such as supporting "hash bang" lines
(``#!/usr/bin/dylan``) and only recompiling when the source has
changed.

Gwydion Dylan already supports a simple form of single-file library,
but it has limitations that this DEP removes, specifically the
inability to deal with import conflicts.


Goals
=====

* Make it possible to use the full power of the Dylan module system to
  define a library in a single file.

* Make all LID file options available in the single-file library
  format.

* Introduce a minimum of new syntax.  It should be obvious to someone
  already used to Dylan what's going on.


Background
==========

A typical hello-world application written in Dylan might look like
this:

**hello-world.lid**

.. code-block:: dylan

    Library: hello-world
    Files: library.dylan
           hello-world.dylan

**library.dylan**

.. code-block:: dylan

    Module: dylan-user

    define libary hello-world
      use common-dylan;
      use io;
    end;

    define module hello-world
      use common-dylan;
      use format-out;
    end;

**hello-world.dylan**

.. code-block:: dylan

    Module: hello-world

    format-out("Hello, world!\n");

Therefore a way to encode their information into a regular dylan
source file is needed.  After the implementation of this DEP, the
above library can be defined in a single source file, as follows:

**hello-world.dylan**

.. code-block:: dylan

    Module: hello-world
    Use-Libraries: common-dylan; io
    Use-Modules: common-dylan; format-out

    format-out("Hello, world!\n");

This continues to use the standard `Dylan Interchange Format
<https://opendylan.org/books/drm/Dylan_Interchange_Format>`_ as defined
in the DRM, with a set of headers, followed by a blank line, followed
by a *code body*.


Specification
=============

Replacing the LID File
----------------------

LID files have the same format as the header section of a Dylan
Interchange Format source file.  When defining a Dylan library in a
single source file, all LID keywords may appear in the header section.
The compiler or interpreter should handle them in the same way it
would if they were in a separate .lid file.  There is no conflict
between the keywords used in LID files and those used in Dylan source
files.  See https://opendylan.org/documentation/library-reference/lid.html
for existing Open Dylan LID file keywords.

Library Header
~~~~~~~~~~~~~~

The ``Library`` header is optional in a single-source library.  If
present, it defines the name of the library.  If missing, the library
name is the same as the module name specified by the ``Module``
header.

Files Header
~~~~~~~~~~~~

The ``Files`` header should not appear in a single-file library.  If
it does, it may be ignored.


Replacing library.dylan
-----------------------

When a compiler or interpreter is given a .dylan file to compile or
execute, these new headers should be respected:

Use-Libraries Header
~~~~~~~~~~~~~~~~~~~~

The ``Use-Libraries`` header names libraries used by the library being
defined.

The format of this header is::

  Use-Libraries: libspec1 ; libspec2 ; ...

In brief, each *libspec* has the exact same syntax as a *use-clause*
in `define library
<https://opendylan.org/books/drm/Definition_Macros#define_library>`_,
but with the leading token ``use`` removed.  Examples::

  Use-Libraries: system; io { import: streams, format-out}; common-dylan
  Use-Libraries:
    system;
    io { import: streams, format-out};
    common-dylan

Note that Dylan source code comments are allowed in this header, and
must be ignored.

Use-Modules Header
~~~~~~~~~~~~~~~~~~

The ``Use-Modules`` header names modules used by the library being
defined.

The format of this header is::

  Use-Modules: modspec1 ; modspec2 ; ...

Again, each *modspec* has the exact same syntax as a *use-clause* in
`define module
<https://opendylan.org/books/drm/Definition_Macros#define_module>`_,
but with the leading token ``use`` removed.  Examples::

  Use-Modules: operating-system; common-dylan; format-out
  Use-Modules:
    operating-system,  // from system
      prefix: "os/",
      export: all;
    common-dylan;
    format-out         // from io

Note that Dylan source code comments are allowed in this header, and
must be ignored.

Export-Names Header
~~~~~~~~~~~~~~~~~~~

The ``Export-Names`` header specifies a list of names exported from
the module named in the ``Module`` header.  The syntax matches the
syntax in the ``exports`` clause of `define module
<https://opendylan.org/books/drm/Definition_Macros#define_module>`_,
but with the token ``exports`` replaced by the ``Export-Names:``
header keyword.  Examples::

  Export-Names: foo, bar, baz

Because it is only possible to define one module in a single-file
library there is no need for a way to specify which modules are
exported from the library.  If the ``Export-Names`` header is
missing then no modules are exported from the library.  If the
``Export-Names`` header is present then the module named in the
``Module`` header is the sole module exported from the library.

Model Implementation
====================

A simple (but not recommended) way to implement this proposal
would be via the following source transformations.  This example is
provided primarily to demonstrate that a single-file library has the
same semantics as a multi-file library and to make it clear that the
new headers defined in this proposal have the same syntax as that of
``define library`` and ``define module``.

#. Generate a ``library.dylan`` file::

     Module: dylan-user

     define library <Library-or-Module-header-value-here>
       use libspec1;
       use libspec2;
       ...
       export  <Library-or-Module-header-value-here>;
     end;

     define module <Module-header-value-here>
       use modspec1
       use modspec2
       ...
       export <Export-Names-header-value-here>;
     end;

   Note the addition of semicolons where necessary.

#. Generate a LID file that includes all the headers except for
   ``Module`` and add a ``Files`` header listing ``library.dylan`` and
   ``the-given-file.dylan``.

#. Compile the generated LID file as usual.

Cost to Implementors
====================

[Someone needs to correct this.  I (cgay) am not familiar with the
compiler internals.]

For the current Open Dylan command-line tools the cost of this
proposal is likely fairly low.  It should involve some code to handle
the case where a ".dylan" file is passed on the command line.  It will
need to parse the file headers and create a "project" object in memory
with generated source records for the library and module definitions
and a LID file representation.  Ideally the compiler will be able to
pinpoint errors in the library and module definitions to the
appropriate header lines.

The Open Dylan IDE can leverage the work done on the command-line
tools, but no doubt it makes some assumptions about projects always
having LID files.  There will be substantial work involved if anyone
wants to support this feature in the IDE.
