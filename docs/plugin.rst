================
Plugins Tutorial
================

Why do we need plugins on NINJA-IDE?
====================================

Plugins are small pieces of code which can interact with NINJA-IDE and
add specific features to it. This is useful if you want to have special
features which NINJA-IDE does not provide, may be a strange feature.
Many times developers around the world try to collaborate to an open
source project but that task could be hard for many reasons.In this
cases plugins are a good option to collaborate, you just need to learn
the project API.


Architecture
============

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/architecture.png
   :align: center
      :alt: 

      The image above show the connections between plugins and NINJa-IDE.
      Plugins get services using a service locator, the service locator
      returns the specific service and then the plugin uses the service to
      talk with NINJA-IDE

How to create plugins
=====================

We recommend that you install and use a oficial plugin called
**pluginProject**. That plugin helps you to create the skeleton of all
plugin for NINJA-IDE, also that plugin allow to test your plugin on
NINJA-IDE and packages your plugin to share it. You can install
**pluginProject** from thge Plugin Manager inside NINJA-IDE (Go to the
Plugins menu and Manage Plugins).

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/install_pluginProject.png
   :align: center
   :alt: 

Plugin Descriptor file
----------------------

This is just a JSON notation file with the extension “.plugin”. This
file helps NINJA-IDE to detect and manage your plugin. The following
information about the plugin should be inside it.::

    {
      "module": "my_plugin",
      "class": "MyPluginExample",
      "authors": "Martin Alderete <malderete@gmail.com>",
      "version": "0.1",
      "url": "http://code.google.com/p/ninja-ide",
      "description": "Este plugin es de prueba"
    }

-  module: Indicates the name of the module where the Plugin class
   resides which will be instantiated by NINJA-IDE.
-  class: Indicates the name of the class which implements the Plugin.
-  authors: String with the author(s).
-  version: Indicates the Plugin version.
-  url: Indicates the url of the plugin may be where is the doc.
-  description: Plugin description

Service locator class
---------------------

This class provide a easy way to request and get the NINJA-IDE services
from your plugin. This class has two methods one for get a service and
other for get all services names.

To get a service::

    one_service = service_locator.get_service("name_of_the_Service")

To check all the possible services::

       for service_name in service_locator.get_availables_services():
          print service_name

Plugin class
------------

All plugins must inherits from this class. This is the base class that
NINJA-IDE provides to creates plugins. This class also inherits from
`QObject`_ because of that your plugins are compatibles with
`signals/slots`_ of the Qt library.

Attributes
~~~~~~~~~~

-  self.metadata: A Python Dictionary with the content of the plugin
   descriptor file.
-  self.locator: A instance of ServiceLocator class
-  self.path: A string with the plugin directory
-  self.logger: a Instance of `PluginLogger`_.

PluginLogger
::::::::::::

This is the logger for plugins, allows to record events occured on
plugins. This is a wrapper over logging.Logger class of `logging`_
module.

Methods
~~~~~~~

.. method:: initialize

This method is called for NINJA-IDE when your plugin is ready to start.
This is the recommended place to request/get the NINJA-IDE services for
your plugin.

.. method:: finish

This method is called when NINJA-IDE is shutting down.

.. method:: get\_preferences\_widget

This method is called for NINJA-IDE when the user open the preferences
dialog. This method allows us to integrate a custom configuration widget
in NINJA-IDE preferences. Is important that this m TRUNCATED! Please
download pandoc if you want to convert large files.

Example
~~~~~~~

::

    from ninja_ide.core import plugin
    from ninja_ide.core import plugin_interfaces
    from PyQt4.QtGui
    import QWidget
    
    class MyPluginExample(plugin.Plugin):
    
        def initialize(self):
            print "The plugin is loading..."
            print "plugin information: %s" % self.metadata
            print "service locator: %s" % self.locator
     
       def finish(self):
            print "The plugin is shutting down because the user has closed NINJA-IDE"
     
       def get_preferences_widget(self):
            pass

Plugins Interfaces
------------------

Some actions from plugins require objects with special interfaces, here
we describe the possibles interfaces.

IProjectTypeHandler
~~~~~~~~~~~~~~~~~~~

This interface is useful if you want to add a new type of project to
NINJA-IDE.::

        def get_pages(self):
            """
            Should Returns a collection of QWizardPage or subclass
            """

        def on_wizard_finish(self, wizard):
            """
            Called when the user finish the wizard
            """

        def get_context_menus(self):
            """
            Should Returns an iterable of QMenu for the context type of the new project type
            """

ISymbolsHandler
~~~~~~~~~~~~~~~

This interface is useful if you want to provide symbols for a language
distinct of Python.

IPluginPreferences
~~~~~~~~~~~~~~~~~~

This interface is useful if you want to add a custom preferences for
your plugin.::

    def obtain_symbols(self, source):
        """ 
        Returns the dict needed by the tree
        Source code in plain text
        """

NOTE: Must return the following structure::

       {
             'attributes': {name: line, name: line},
             'functions':  {name: line, name: line},
             'classes':     {name: (line, {
                                    'attributes': {name: line},
                                    'function': {name: line}}
                                    )         
                             }     
       }

Services
--------

Services are the way that plugins talk with NINJA-IDE. Conceptually
services are proxies to NINJA-IDE components. NINJA-IDE defines
differents components, each one of theese has differents features.

editor
------

This service allows to interact with the main parts of NINJA-IDE such
as, the editor, the tab manager and listen signals which NINJA-IDE emit
when save a file, save project, change the current tab and others.

Signals
:::::::

.. method:: editorKeyPressEvent(QEvent)

Emitted when the user press a key

To connect the plugin to this signal do

::

    SERVICE_NAME = "editor"
    editor_service = self.locator.get_service(SERVICE_NAME)
    editor_service.editorKeyPressEvent.connect(self._do_something)
    
    def do_something(self, event):
        #the code go here!

.. method:: beforeFileSaved(fileName)

Emitted **before** save the fileName on the disc

To connect the plugin to this signal do::

    SERVICE_NAME = "editor"
    editor_service = self.locator.get_service(SERVICE_NAME)
    editor_service.beforeFileSaved.connect(self._do_something)
    
    def do_something(self, fileName):
        #the code go here!

.. method:: fileSaved(fileName)

Emitted when the user save a file

To connect the plugin to this signal do::

    SERVICE_NAME = "editor"
    editor_service = self.locator.get_service(SERVICE_NAME)
    editor_service.fileSaved.connect(self._do_something)
    
    def do_something(self, fileName):
        #the code go here!

.. method:: currentTabChanged(fileName)

Emitted when the user change the current tab

To connect the plugin to this signal do::

    SERVICE_NAME = "editor"
    editor_service = self.locator.get_service(SERVICE_NAME)
    editor_service.currentTabChanged.connect(self._do_something)
    
    def do_something(self, fileName):
        #the code go here!

.. method:: fileExecuted(fileName)

Emitted when the user execute a file

To connect the plugin to this signal do::

    SERVICE_NAME = "editor"
    editor_service = self.locator.get_service(SERVICE_NAME)
    editor_service.fileExecuted.connect(self._do_something)
    
    def do_something(self, fileName):
        #the code go here!

.. method:: fileOpened(fileName)

Emitted when the user open a file

To connect the plugin to this signal do::

    SERVICE_NAME = "editor"
    editor_service = self.locator.get_service(SERVICE_NAME)
    editor_service.fileOpened.connect(self._do_something)
    
    def do_something(self, fileName):
        #the code go here!

Methods
:::::::

.. method:: get\_tab\_manager(self)

This method returns the TabWidget
(ninja\_ide.gui.main\_panel.tab\_widget.TabWidget) subclass of
`QTabWidget <http://doc.qt.nokia.com/4.7/qtabwidget.html>`_.

.. method:: add\_menu(self, menu, lang=".py")

This method add an **extra context menu** to the editor's context menu
(`QMenu or subclass <http://doc.qt.nokia.com/4.7/qmenu.html>`_).

.. method:: get\_opened\_documents(self)

This method returns the name of the open files

.. method:: add\_editor(self, fileName="", content=None, syntax=None)

This method creates a new editor. ** fileName: Absolute path to a file
** content: Content for the editor if not fileName ** syntax: Syntax
name, example python **

If the method is called without fileName and content an empty editor is
created.

.. method:: get\_editor(self)

This method returns the actual editor (instance of
ninja\_ide.gui.editor.Editor) This method could return None

.. method:: get\_editor\_path(self)

This mthod returns the actual editor's path. This method could return
None if there is not an editor

.. method:: get\_project\_owner(self, editorWidget=None)

This method return the project where the current file in the editor
belongs, or an empty string. (If the Editor Widget is not specified, it
returns the info from the current editor in focus)

.. method:: get\_text(self)

This method returns the plain text of the current editor or None if thre
is not an editor.

.. method:: get\_selected\_text(self)

This method returns the selected text of and editor. This method could
return None

.. method:: insert\_text(self, text)

This method inserts text in the current cursor position.

.. method:: get\_file\_syntax(self, editorWidget=None)

This method returns the syntax for the current file, the syntax is
represented as a dictionary that contains the descriptor that ninja
recognize for each language. (If the Editor Widget is not specified, it
returns the info from the current editor in focus)

.. method:: jump\_to\_line(self, lineno)

This method jumps to a specific line in the current editor

.. method:: get\_lines\_count(self)

This method returns the count of lines in the current editor

.. method:: save\_file(self)

This method saves the actual file

.. method:: open\_files(self, files, mainTab=True)

This method opens many files each one in a different editor

.. method:: open\_file(self, fileName='', cursorPosition=0, positionIsLineNumber=False)

This method opens a single file, if the file is already open it get
focus

.. method:: open\_image(self, filename)

This method opens a single image

toolbar
~~~~~~~

This service allows to interact with the toolbar of NINJA-IDE. The
toolbar is a instance of
`QToolbar <http://doc.qt.nokia.com/latest/qtoolbar.html>`_ so we can add
actions (`QAction <http://doc.qt.nokia.com/latest/qaction.html>`_) to
it.

By Default the toolbar of NINJA-IDE looks like the image below

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/toolbar_base.png
   :align: center
   :alt: 

Methods
:::::::

.. method:: add\_action(self, action)

This method allows to add an action (`QAction or
subclass <http://doc.qt.nokia.com/latest/qaction.html>`_) to the
toolbar.

To add one action we should do::

    SERVICE_NAME = "toolbar"
    toolbar_service = self.locator.get_service(SERVICE_NAME)
    
    #instanciate a QAction (or subclass)
    one_Action = QAction(...)
    
    #add the action to the toolbar of NINJA-IDE
    toolbar_service.add_action(one_action)

After the code above the toolbar of NINJA-IDE looks

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/toolbar_agregado.png
   :align: center
   :alt: 
Great! we have added one action to the toolbar of NINJA-IDE.

menuApp
~~~~~~~

This service allows to interact with the **Plugins** menu of NINJA-IDE.
We can insert menus
(`QMenu <http://doc.qt.nokia.com/latest/qmenu.html>`_) or/and actions
(`QAction <http://doc.qt.nokia.com/latest/qaction.html>`_).

By Default the Plugins Menu of NINJA-IDE looks like the image below

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/menu_plugin_base.png
   :align: center
   :alt: 

Methods
:::::::

.. method:: add\_menu(self, menu)

This method allows to add a menu (`QMenu or
subclass <http://doc.qt.nokia.com/latest/qmenu.html>`_) to the NINJA-IDE
plugins menu.

To add one menu to the NINJA-IDE we should do::

    SERVICE_NAME = "menuApp"
    menu_service = self.locator.get_service(SERVICE_NAME)
    
    #instanciate a QMenu (or subclass)
    one_menu = QMenu(...)
    
    #add the menu to NINJA-IDE
    menu_service.add_menu(one_menu)

After the code above the Plugins Menu of NINJA-IDE looks

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/menu_plugin_agregado.png
   :align: center
   :alt: 

.. method:: add\_action(self, action)

This method allows to add an action (`QAction or
subclass <http://doc.qt.nokia.com/latest/qaction.html>`_) to the
NINJA-IDE plugins menu.

To add one action to the NINJA-IDE we should do::

    SERVICE_NAME = "menuApp"
    menu_service = self.locator.get_service(SERVICE_NAME)
    
    #instanciate a QAction (or subclass)
    one_action = QAction(...)
    
    #add the action to NINJA-IDE
    menu_service.add_action(one_action)

After the code above the Plugins Menu of NINJA-IDE looks

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/menu_plugin_action_agregado.png
   :align: center
   :alt: 

misc
~~~~

This service allows to interact with the miscelaneos container (misc) of
NINJA-IDE. This container is at the bottom of the user interface. The
container has a collection of widgets and shows an icon for each one
them. Only one widget is visible at a time. We can add widgets (`QWidget
or subclass <http://doc.qt.nokia.com/latest/qwidget.html>`_) to the misc
container.

By Default the Plugins Menu of NINJA-IDE looks like the image below

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/misc_base.png
   :align: center
   :alt: 
The image above show the misc container the console and the icons.


Methods
:::::::

.. method:: add\_widget(self, widget, icon\_path, description)

This method allows to add widgets (`QWidget or
subclass <http://doc.qt.nokia.com/latest/qwidget.html>`_) to the misc
container.

To add a widget to the misc container we should do::

    SERVICE_NAME = "misc"
    misc_service = self.locator.get_service(SERVICE_NAME)
    
    #instanciate a QWidget (or subclass)
    my_widget = QWidget(...)icon_path = "some_plate/where/the/icon/is.png"
    description = "This is my widget in NINJA-IDE"
    
    #add the widget to NINJA-IDE
    misc_service.add_widget(my_widget, icon_path, description)

After the code above the misc container of NINJA-IDE looks

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/misc_agregado.png
   :align: center
   :alt: 
Great! we have added one widget with a
`QWebView <http://doc.qt.nokia.com/4.7-snapshot/qwebview.html>`_ to the
misc container of NINJA-IDE.

explorer
~~~~~~~~

This service allows to interact with the NINJA-IDE explorer container
which hold the
`TreeProjectsWidget </p/ninja-ide/wiki/APIDetails#3.5.5.2>`_ and the
`TreeSymbolsWidget </p/ninja-ide/wiki/APIDetails#3.5.5.5>`_. Before
explain the explorer service, we are going to see some important
classes.

Methods
:::::::

.. method:: get\_tree\_projects(self)

Returns the `TreeProjectsWidget </p/ninja-ide/wiki/APIDetails#3.5.5.2>`_

.. method:: get\_tree\_symbols(self)

Returns the `TreeSymbolsWidget </p/ninja-ide/wiki/APIDetails#3.5.5.5>`_

.. method:: get\_current\_project\_item(self)

Returns the current item of the tree projects (if possible) Note: This
method is a shortcut of self.get\_tree\_projects().currentItem()

.. method:: get\_project\_item\_by\_name(self, projeectName)

Return a ProjectItem based on the name provided, or None if an item with
that name can not be found.

.. method:: set\_symbols\_handler(self, file\_extension, symbols\_handler)

Add a new Symbol's handler for the given file extension Note:
symbols\_handler SHOULD have a special interface see
ninja\_ide.core.plugin\_interfaces

Example: if you want to add a new symbols handler for C++, your plugin
should do the following::

    SERVICE_NAME = 'explorer'
    self.explorer_s = self.locator.get_service(SERVICE_NAME)  
    cpp_symbols_handler = CppSymbolHandler(...)
    self.explorer_s.set_symbols_handler('.cpp', cpp_symbols_handler)

Then all symbols in .cpp files will be handle by cpp\_symbols\_handler

.. method:: set\_project\_type\_handler(self, project\_type, project\_type\_handler)

Add a new Project Type and the handler for it Note:
project\_type\_handler SHOULD have a special interface see
ninja\_ide.core.plugin\_interfaces

Example: If you want to add a custom type of project, your pluging
should do the following::

    SERVICE_NAME = 'explorer'
    self.explorer_s = self.locator.get_service(SERVICE_NAME)
    foo_project_handler = FooProjectHandler(...)
    self.explorer_s.set_project_type_handler('Foo Project', foo_project_handler)

Then 'Foo Project' will appear in the New Project wizard and
foo\_project\_handler instance controls the wizard

.. method:: add\_tab(self, tab, title)

Add a tab (`QTabWidget or
subclass <http://doc.qt.nokia.com/4.7/qtabwidget.html>`_) with the given
title (string)

.. method:: get\_actual\_project(self)

Returns the path of the opened projects

.. method:: get\_opened\_projects(self)

Returns a list of strings with the paths of the opened projects, or an
empty list if there isn't any opened projects

.. method:: add\_project\_menu(self, menu, lang='all')

Add an extra menu(`QMenu or
subclass <http://doc.qt.nokia.com/4.7/qmenu.html>`_) to the project
explorer for the files specified in lang. Note: lang is a file extension
such as .php, .py, .cpp A special case is, if you want to add an extra
menu for all kind of files, in that case you need specify lang='all'.

Example 1: if you want to add an extra menu for Python files, your
plugin should do the following::

    SERVICE_NAME = 'explorer'
    self.explorer_s = self.locator.get_service(SERVICE_NAME)
    extra_menu = MyCustomMenuForPHPFiles()
    self.explorer_s.add_project_menu(extra_menu, lang='.php')

Example 2: if you want to add an extra menu for all files, your plugin
should do the following::

    SERVICE_NAME = 'explorer'
    self.explorer_s = self.locator.get_service(SERVICE_NAME)
    extra_menu = MyCustomMenuForPythonFiles()
    self.explorer_s.add_project_menu(extra_menu, lang='all')

Signals
:::::::

.. method:: projectExecuted(projectPath)

Emitted when the user execute a project

To connect the plugin to this signal do::

    SERVICE_NAME = "explorer"
    explorer_service = self.locator.get_service(SERVICE_NAME)
    explorer_service.projectExecuted.connect(self._do_something)
    
    def do_something(self, projectPath):  
        #the code go here!

.. method:: projectOpened(projectPath)

Emitted when the user open a project

To connect the plugin to this signal do::

    SERVICE_NAME = "explorer"
    explorer_service = self.locator.get_service(SERVICE_NAME)
    explorer_service.projectOpened.connect(self._do_something)
    
    def do_something(self, projectPath):
        #the code go here!


TreeProjectsWidget
::::::::::::::::::

This class inherits from
`QTreeWidget <http://doc.qt.nokia.com/latest/qtreewidget.html>`_ and
represent a tree with all the NINJA-IDE projects and their content
(folders and files).

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/treeprojects.png
   :align: center
   :alt: 

ProjectTree
:::::::::::

This class inherits from This class inherits from
`QTreeWidgetItem <http://doc.qt.nokia.com/latest/qtreewidgetitem.html>`_
and is used for represent projects (root of tree).

This class has general information of the project:

-  self.path
-  self.isFolder
-  self.projectType
-  self.description
-  self.url
-  self.license
-  self.mainFile
-  self.extensions
-  self.pythonPath
-  self.programParams
-  self.venv

Methods
_______

def lang(self)
^^^^^^^^^^^^^^
Returns the programming language of the project


def get\_full\_path(self)
^^^^^^^^^^^^^^^^^^^^^^^^^

Returns the full path of the project

ProjectItem
:::::::::::

This class inherits from This class inherits from
`QTreeWidgetItem <http://doc.qt.nokia.com/latest/qtreewidgetitem.html>`_
and is used for represent content of projects (folder and files).

This class has general informarmation about the file.

-  self.path: Absolute path to the given item (folder or file)
-  self.isFolder: Boolean value depending on the item is folder or item.

Methods
_______

def get\_full\_path(self)
^^^^^^^^^^^^^^^^^^^^^^^^^

Returns the full path of the file

TreeSymbolsWidget
:::::::::::::::::

This class inherits from
`QTreeWidget <http://doc.qt.nokia.com/latest/qtreewidget.html>`_ and
represents the content of a file **classes**, **methods**, **functions**
and **global variables**. NINJA-IDE **only** handles symbols for Python
files, but we can add handlers for differents files.

TreeSymbolsWidget looks

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/symbolstree.png
   :align: center
   :alt: 

Testing your plugin
-------------------

To test your plugin you have different ways:

-  **Hacker way**

You have to move your plugin code and the plugin descriptor file to
~/.ninja\_ide/addins/plugins/. Re run NINJA-IDE and see what happen.

-  ** `pluginProject </p/ninja-ide/wiki/APIDetails#3>`_ (Recommended
   way)**

You have to install the oficial
`pluginProject </p/ninja-ide/wiki/APIDetails#3>`_ plugin, create a new
project, select **NINJA-Plugin-Project** type and follow the wizard.
When you finished the wizard, the new project will be open and some code
will be into some files. Go to the root of the project and right-click
in it, go to **"Plugin Tools"** and then select **"Test this plugin on
NINJA-IDE"**, that will launch a new instance of NINJA-IDE with your
plugin.

-  ** NINJA-IDE embedded console (Recommended way)**

You can test the NINJA-IDE plugins API from the embedded console in
NINJA-IDE, to do it you have to open the console (F4) and write your
plugin code then you will see the results in real time on NINJA-IDE. For
Example, see the session below when the user is playing with the API.

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/playing_with_the_API.png
   :align: center
   :alt: 

Debugging you plugin
--------------------

When you install/test a plugin, it could fails.If the plugin fails
NINJA-IDE shows you a dialog with information (plugin name and
traceback) about it. The image below shows how NINJA-IDE report you
plugin errors.

.. figure:: http://plugins.ninja-ide.googlecode.com/hg/ninja_img/traceback_widget.png
   :align: center
   :alt: 

