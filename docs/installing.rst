====================
Installing NINJA-IDE
====================

First steps with NINJA-IDE
==========================

So you have decided to install NINJA-IDE (good for you my young padawan!). The
first thing you should do is visit this page:

http://ninja-ide.org/downloads/

There you will be able to find installers for Linux, Windows and Mac. Once you
download the proper package for your system, you just need to execute that
package and you will be able to be coding in NINJA-IDE in a heartbeat!

Installation on Mac OS with Homebrew
------------------------------------
It is also possible to install Ninja-IDE on Mac OS X with `Homebrew
<http://brew.sh>`_ by using the command: ::

    brew install ninja-ide

When Ninja-IDE is installed you can start the application by typing: ::

    $ ninja-ide

Make sure that macfsevents (``$ pip install macfsevents``) is installed before
you install Ninja-IDE.

Executing NINJA-IDE from source
===============================

If you are a leading edge guy/girl/alien, you can use NINJA-IDE from source and
enjoy always from the latest changes in the code (Also, if you are an alien
please let us know. We will definitely like to hear about that). To execute
NINJA-IDE from sources you just need to satisfy a small set of dependencies:

-  Python >= `2.7 <http://www.python.org/download/>`_ (or Python 3)
-  PyQt >= `4.8 <http://www.riverbankcomputing.co.uk/software/pyqt/intro>`_
-  Linux: pyinotify (Ubuntu$ sudo apt-get install python-pyinotify)
-  Windows: `pywin32 <http://sourceforge.net/projects/pywin32/files/>`_
-  MAC OS: macfsevents ($ pip install macfsevents)

Then, if you have Git installed, you can clone the NINJA-IDE repository like
this: ::

    $ git clone https://github.com/ninja-ide/ninja-ide.git

Or you can get the latest version of the code by downloading this zip:

https://github.com/ninja-ide/ninja-ide/zipball/master

**Now you have everything you need to execute NINJA-IDE from source!**

.. warning::

   We always try to keep the repository stable, but with the latest code,
   some kind of new bugs might appear... The good news is that you can be the
   first one reporting these kind of things and help us fix it faster.

Ok, so... let's execute NINJA-IDE from source. Go to the ninja-ide folder (the
one where you cloned the repository to, or where you uncompressed the zip
file), and just type: ::

    $ python ninja-ide.py

And you are done!
