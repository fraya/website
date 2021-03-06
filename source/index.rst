.. raw:: html

   <div class="row-fluid">
     <div class="span4">
       <div class="well">
	 <p>Dylan is a multi-paradigm functional and object-oriented programming language.
	 It is dynamic while providing a programming model designed to support efficient
	 machine code generation, including fine-grained control over dynamic and static
	 behaviors.</p>
       </div>
     </div>
     <div class="span8">
.. raw:: html

     <div id="code-carousel" class="carousel slide">
       <ol class="carousel-indicators">
         <li data-target="#code-carousel" data-slide-to="0" class="active"></li>
         <li data-target="#code-carousel" data-slide-to="1"></li>
         <li data-target="#code-carousel" data-slide-to="2"></li>
       </ol>
       <div class="carousel-inner">
         <div class="item active">
.. code-block:: dylan

  define class <vehicle> (<object>)
    slot owner :: <string>,
      init-keyword: owner:,
      init-value: "Northern Motors";
  end;

  define class <car> (<vehicle>)
  end;

  define class <truck> (<vehicle>)
    slot capacity,
      required-init-keyword: tons:;
  end;

.. raw:: html

         </div>
         <div class="item">
.. code-block:: dylan

  define generic tax
      (v :: <vehicle>) => (tax-in-dollars :: <float>);

  define method tax
      (v :: <vehicle>) => (tax-in-dollars :: <float>)
    100.00
  end;

  define method tax
      (c :: <car>) => (tax-in-dollars :: <float>)
    50.0
  end;

  define method tax
      (t :: <truck>) => (tax-in-dollars :: <float>)
    // standard vehicle tax plus $10/ton
    next-method() + t.capacity * 10.0
  end;

.. raw:: html

         </div>
         <div class="item">
.. code-block:: dylan

  define function make-fibonacci()
    let n = 0;
    let m = 1;
    method ()
      let result = n + m;
      n := m;
      m := result  // return value
    end
  end;

  define constant fib = make-fibonacci();

  for (i from 1 to 15)
    format-out("%d ", fib())
  end;

.. raw:: html

         </div>
       </div>
       <a class="carousel-control left" href="#code-carousel" data-slide="prev">&lsaquo;</a>
       <a class="carousel-control right" href="#code-carousel" data-slide="next">&rsaquo;</a>
     </div>
     </div>

   </div>
   <div class="row-fluid">
     <div class="span6">
       <h2>Recent News</h2>

.. include:: news/recent.rst.inc

.. raw:: html

       <div class="pull-right"><a href="news/index.html">All news &raquo;</a></div>
     </div>
     <div class="span6">
       <h2>Get Started</h2>

`Downloading Dylan </download/index.html>`_  is easy.
Once downloaded, we recommend reading through `Introduction to Dylan <https://opendylan.org/documentation/intro-dylan/>`_ and exploring `some examples <https://github.com/dylan-lang/opendylan/tree/master/sources/examples>`_.

.. raw:: html

     <h2>Learn Dylan</h2>

Dylan has a large amount of documentation available:

* `Introduction to Dylan <https://opendylan.org/documentation/intro-dylan/>`__:
   A tutorial written for those with solid programming
   experience in C++ or another object-oriented, static language. It
   provides a gentler introduction to Dylan than does the Dylan
   Reference Manual (DRM).
* `Dylan Programming Guide <https://opendylan.org/books/dpg/>`_:
   A book length Dylan tutorial.
* `Dylan Reference Manual <https://opendylan.org/books/drm/>`_:
   The official definition of the Dylan language and standard library.
* `Open Dylan Documentation <https://opendylan.org/documentation/>`_:
   All of the Open Dylan documentation.

.. raw:: html

     </div>
     </div>
   </div>

.. toctree::
   :maxdepth: 1
   :hidden:
   :glob:

   */*
   articles/*/*
   documentation/cheatsheets/*
   news/*/*/*/*
   community/gsoc/*

.. -*- tab-width: 4 -*-
