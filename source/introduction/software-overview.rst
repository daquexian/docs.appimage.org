.. _software-overview:

Software Overview
=================

.. todo::
   - list deprecated components


AppImage project
****************

.. _ref-appimagekit:

AppImageKit
-----------

`AppImageKit <https://github.com/AppImage/AppImageKit>`_ is the reference implementation of the :ref:`AppImage specification <appimage-specification>`. It is split up into several components, which are described in this subsection.


.. _ref-runtime:

runtime
^^^^^^^

The runtime provides the "executable header" of every AppImage. When executing an AppImage, the runtime within the AppImage is run, which mounts the embedded filesystem image read-only in a temporary location, and launches the payload application within there. After the payload application exited, the runtime unmounts the squashfs image and cleans up the temporary resources (such as, the temporary mountpoint directory).


.. _ref-appimagetool:

appimagetool
^^^^^^^^^^^^

appimagetool is the easiest way to create AppImages from existing directories on the system, the so-called :ref:`AppDir`s. It creates the AppImage by embedding the runtime, and creating and appending the filesystem image.

appimagetool implements all optional features, like for instance :ref:`update information <update-information>`, :ref:`signing <signing>`, and some linting options to make sure the information in the AppImage is valid (for instance, it can validate :ref:`AppStream files <appstream-support>`).


AppRun
^^^^^^

Every AppImage's AppDir must contain a file called :code:`AppRun`, providing the "entry point". When running the AppImage, the :ref:`runtime` executes the :code:`AppRun` file within the :ref:`AppDir`.

:code:`AppRun` doesn't necessarily have to be a regular file. If the application is :ref:`relocatable <relocatable-apps>`, it can just be a symlink to the main binary. Tools like :ref:`linuxdeploy` can turn applications into relocatable applications, and therefore create such a symlink.

In some cases, though, when an existing application must not be altered (e.g., when the license prohibits any modifications) or tools like linuxdeploy cannot be used, :code:`AppRun.c` can be used. :code:`AppRun.c` attempts to make programs load bundled shared libraries instead of system ones by manipulating environment variable. Furthermore, it attempts to prevent warnings users might encounter that are coming from the fact the :ref:`AppDir` is mounted read-only.

Using :code:`AppRun` is not a guarantee that an application will run, and the packager must provide all the resources an application could need manually (or by using external tools) before creating the AppImage with :ref:`appimagetool`. :code:`AppRun` force-changes the current working directory, and therefore applications can not detect where the AppImage was called originally. This may be especially annoying for CLI tools, but can also be a problem for GUI applications expecting paths via parameters.

.. note::
   :code:`AppRun` is legacy technology, and should be avoided if possible. Tools like :ref:`linuxdeploy` deploy applications in a different way, and deprecated its usage. This doesn't mean there's no cases in which :code:`AppRun` might be useful, but it's got several limitations a user must be aware of before using it.


Helpers
^^^^^^^

AppImageKit ships with a few helpers that can be used to verify and validate some AppImage features.


validate
########

:code:`validate` can validate the PGP signatures inside AppImages.


digest-md5
##########

Calculates the MD5 digest used for desktop integration purposes for a given AppImage. This digest depends on the path, not on the contents.


.. _ref-appimageupdate:

AppImageUpdate
--------------

AppImageUpdate_ lets you update AppImages in a decentral way using information embedded in the AppImage itself.

The project consists of two tools: :code:`appimageupdatetool`, a full-featured CLI tool for updating AppImages and dealing with `update information`_, and :code:`AppImageUpdate`, a user interface for updating AppImages written in Qt.

.. _AppImageUpdate: https://github.com/AppImage/AppImageUpdate
.. _update information: https://github.com/AppImage/AppImageSpec/blob/master/draft.md\#update-information


.. _appimaged:

appimaged
---------

`appimaged <https://github.com/AppImage/appimaged>`_ is a daemon that monitors a predefined set of directories on the system, looking for AppImages. It automatically integrates all AppImages it can find during an initial search, and then live watches for new AppImage (or AppImages that were removed) and (de)integrates these immediately.

It is shipped in a few native distribution package formats as well as as AppImage.

.. warning::

   One of the monitored directories is ``~/Downloads``. If the directory is very large, appimaged usually needs quite long to visit all files. It is likely to slow down the system (specifically, the filesystem).


Third-party tools
*****************

This section showcases a couple of third-party tools that can be used to create and handle AppImage files.


linuxdeploy
-----------

linuxdeploy_ is a simple to use tool that can be used to create AppDirs and AppImages. It has been developed in 2018, and describes itself as an "AppDir creation and maintenance tool".

linuxdeploy is the successor of :ref:`linuxdeployqt`, and can be used in all projects that use :ref:`linuxdeployqt`. The list of plugins is continually growing, providing solutions for bundling frameworks such as `Qt <https://github.com/linuxdeploy/linuxdeploy-plugin-qt>`_ as well as complete environments for non-native programming languages such as `Python <https://github.com/linuxdeploy/linuxdeploy-plugin-conda>`_.

.. _linuxdeploy: https://github.com/linuxdeploy/linuxdeploy

.. seealso::

   There's a guide on :ref:`native binary packaging <ref-packaging-native-binaries>` and a general :ref:`linuxdeploy user guide <ref-linuxdeploy>` in the :ref:`ref-packaging-guide`.


.. _ref-appimagelauncher:

AppImageLauncher
----------------

AppImageLauncher_ is a helper application for Linux distributions serving as a kind of "entry point" for running and integrating AppImages.

Quoting the README:

    AppImageLauncher makes your Linux desktop AppImage ready™. By installing it, you won't ever have to worry about AppImages again. You can always double click them without making them executable first, just like you should be able to do nowadays. You can integrate AppImages with a single mouse click, and manage them from your application launcher. Updating and removing AppImages becomes as easy as never before.

    Due to its simple but efficient way to integrate into your system, it plays well with other applications that can be used to manage AppImages, for example app stores. However, it doesn't depend on any of those, and can run completely standalone.

    Install AppImageLauncher today for your distribution and enjoy using AppImages as easy as never before!

    -- https://github.com/TheAssassin/AppImageLauncher/blob/master/README.md

AppImageLauncher doesn't provide any kind of "app store" software, but integrates into system-provided launchers' context menus. It provides tools for updating (based on :ref:`AppImageUpdate`) and removing AppImages.

.. _AppImageLauncher: https://github.com/TheAssassin/AppImageLauncher


Nomad Software Center
---------------------

.. todo::
   describe app store


linuxdeployqt
-------------

.. todo::
   describe linuxdeployqt


.. todo::
   Describe the rest of the third-party tools
