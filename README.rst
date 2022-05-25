.. highlight:: rst

-----------------
Kubernetes
-----------------


^^^^^^^^^^
Commands
^^^^^^^^^^


|


Shortcuts::

       Level 1
       =======
       
       
       Level 2
       -------
       
       Level 3
       ^^^^^^^
       
       Level 4
       """""""


|


Commands::

       Level 1
       =======
       
       
       Level 2
       -------
       
       Level 3
       ^^^^^^^
       
       Level 4
       """""""



|



^^^^^^^^^
Windows 7
^^^^^^^^^

* Install `Python 2.7 <http://www.python.org/ftp/python/2.7/python-2.7.msi>`_
* Install `Python Setuptools <http://pypi.python.org/packages/2.7/s/setuptools/setuptools-0.6c11.win32-py2.7.exe#md5=57e1e64f6b7c7f1d2eddfc9746bbaf20>`_ (a package manager)
* Set PATH environment variable for Python scripts:





^^^^^^^^^^^^^^^
Debianoid Linux
^^^^^^^^^^^^^^^

* Python is installed (Unless you're using a brutally lightweight distro. Probably not ideal for documentation production).
* Install Sphinx by typing the following commands to the terminal::
  
     sudo apt-get install python-pip
     sudo pip install sphinx



|



--------------------------
Creating the documentation
--------------------------

* Now, still in the terminal, create a directory for your documentation and move there.

  Windows XP::

     md mydoc
     cd mydoc

  Win7/Linux::

     mkdir mydoc
     cd mydoc

* And finally generate a basic documentation template::

     sphinx-quickstart

* Quickstart will ask you some questions.
  The only questions that should interest you for now are:

  - *Project name:*
  - *Author name:*  
  - *Project version*

  You can skip the others by pressing Enter.
  This will set up default settings.

  You can change any of these options later (you will be changing at least the version as your project develops).
  

* The master document, ``index.rst``, serves as a table of contents and
  welcome page for the documentation. 

  It contains a heading, table of contents, and a section called
  *Indices and Tables* with references for module index, search and so on.


* Now create some content.

  Create a new reStructuredText file, for example ``example.rst``, in the documentation directory.
  Add it to table of contents (add ``example`` to ``toctree`` in ``index.rst``.)

  Open the file in any text editor (MS Word is not a text editor).
  When saving the file, **make sure** to use the UTF-8 encoding. 

  Use `source code of this tutorial <https://raw.github.com/kiith-sa/reStructuredText-tutorial/master/README.rst>`_ 
  as a reference.

  Use ``Ctrl-C`` and ``Ctrl-V`` . Do random stuff to try what does what.


  For example you can do this:

  * Text::
    
       An extremely awesome sentence. Another mega-awesome sentence.
       Lines that are together form a paragraph.
    
       Lines that are apart form separate paragraphs.

  * *emphasized text* : ``*emphasized text*``
  * **strong text**   : ``**strong text**``
  * ``literal text``  :: 

     ``literal text``

  * `A link <http://www.google.com>`_ : :: 

     `A link <http://www.google.com>`_


  * An image: ``.. image:: image.png``

  * Bullet lists::

       * this is
       * a list
       
         * with a nested list
         * and some subitems
       
       * and here the parent list continues

  * Numbered lists::
       
       1. This is a numbered list.
       2. It has two items too.
     
  * Can be automatically numbered::

       #. This is a numbered list.
       #. It has two items too.

  * Tables::
     
       ====== ============ =======
       No.    Availability Name
       ====== ============ =======
       1      N/A          Biros
       2      42           piskoty
       3      N/A          beton
       ====== ============ =======





---------------------
Example documentation
---------------------

* `Python documentation <http://docs.python.org/>`_
* `Zope documentation <http://docs.zope.org/zope2/index.html>`_
* `D:YAML documentation <http://dyaml.alwaysdata.net/static/html/doc_0.4/index.html>`_


-----
Links
-----

* `Sphinx <http://sphinx.pocoo.org>`_
