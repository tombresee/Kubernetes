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





|


-----
Links
-----

* https://collabnix.github.io/kubelabs/api.html

* https://collabnix.github.io/kubelabs/   ******

* https://e2286dbf385641eb8b0777c1b208cc2d-167772178-30000-cykoria04.environments.katacoda.com/#/cronjob?namespace=default

* https://collabnix.github.io/kubelabs/Kubernetes_Intro_slides-1/Kubernetes_Intro_slides-1.html

* https://github.com/collabnix/kubelabs

* https://collabnix.com/kubectl-cheatsheet/

* `OReilly Book <https://learning.oreilly.com/library/view/kubernetes-up-and/9781098110192/titlepage01.html>`_

* `Sphinx <http://sphinx.pocoo.org>`_

* https://kubernetes.io/docs/reference/kubectl/cheatsheet/

* https://www.docker.com/blog/how-kubernetes-works-under-the-hood-with-docker-desktop/

* https://birthday.play-with-docker.com/kubernetes-docker-desktop/

* htps://github.com/kubernetes-sigs/metrics-server

* https://www.docker.com/101-tutorial/

* https://betterprogramming.pub/dockers-voting-app-on-swarm-kubernetes-and-nomad-8835a82050cf

* https://docs.docker.com/engine/reference/commandline/container_ls/

* https://github.com/docker/labs/blob/master/beginner/chapters/votingapp.md     

* https://collabnix.com/kubectl-cheatsheet/

|
|
|
|

