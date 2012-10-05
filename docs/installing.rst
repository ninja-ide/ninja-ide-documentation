====================
Installing NINJA-IDE
====================

First steps with NINJA-IDE
==========================

So you have decided to install NINJA-IDE (good for you my young padawan!). The
first thing you should do is visit this page:

http://ninja-ide.org/downloads/

There you will be able to find installers for Linux, Windows and Mac. Once
you download the proper package for your system, you just need to execute that
package and you will be able to be coding in NINJA-IDE in a heartbeat!

Executing NINJA-IDE from sources
================================

If you are a leading edge guy/girl/alien, you can use NINJA-IDE from sources
and enjoy always from the latest changes in the code (Also, if you are an alien
please let us know. We will definitely like to hear about that).
To execute NINJA-IDE from sources you just need to satisfy a small set of
dependencies:

-  Python >= 2.7 (Not Python3 yet)
-  PyQt >= 4.8
-  Linux: pyinotify (Ubuntu$ sudo apt-get install python-pyinotify)
-  Windows: pywin32 (http://sourceforge.net/projects/pywin32/files/pywin32/)
-  MAC OS: macfsevents ($ pip install macfsevents)

Then, if you have Git installed, you can clone the NINJA-IDE repository like
this:::

    $ git clone https://github.com/ninja-ide/ninja-ide.git

Or you can get the latest version of the code by downloading this zip:

https://github.com/ninja-ide/ninja-ide/zipball/master

**Now you have everything you need to execute NINJA-IDE from sources!**
(WARNING: we always try to keep the repository stable, but with the latest code,
some kind of new bugs might appear... The good news is that you can be the first
one reporting these kind of things and help us fix it faster)

Ok, so... let's execute NINJA-IDE from sources. Go to the ninja-ide folder (the
one where you cloned the repository to, or where you uncompressed the zip file),
and just type:::

    $ python ninja-ide.py

And you are done!