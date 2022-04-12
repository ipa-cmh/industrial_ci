Workspace Configuration
=======================

Workflow
---------
The default test will just build the packages in the target repository and optionally run the contained tests.
This behavior can be expanded with addtional workspaces

A. Upstream workspace: Source packages that are needed for building or testing the target or downstream packages

   1. Fetch source code (``UPSTREAM_WORKSPACE``)
   2. Install dependencies with ``rosdep``
   3. Build workspace ``$BASEDIR/${PREFIX}upstream_ws``, chained to /opt/ros (or ``UNDERLAY``)

B. Target workspace: Packages in your target repository that should get build and tested

   1. Fetch source code (``TARGET_WORKSPACE``)
   2. Install dependencies with ``rosdep``
   3. Build workspace ``$BASEDIR/${PREFIX}target_ws``, chained to upstream workspace or /opt/ros (or ``UNDERLAY``)
   4. run tests (opt-out with ``NOT_TEST_BUILD``)

C. Downstream workspace: Packages that should get tested against your target repository

   1. Fetch source code (``DOWNSTREAM_WORKSPACE``)
   2. Install dependencies with rosdep
   3. Build workspace ``$BASEDIR/${PREFIX}downstream_ws``, chained to target workspace
   4. run tests (opt-out with ``NOT_TEST_DOWNSTREAM``)

Workspace definition
---------------------

Each workspace can be composed as a sequence of the following items:

* URL of a source repository with the pattern ``<scheme>:<resource>#<version>``, e.g. ``github:ros-industrial/industrial_ci#master``.
  Supported scheme are:

  * ``github`` for GitHub repositories
  * ``gitlab`` for Gitlab repositories
  * ``bitbucket`` for Bitbucket repositories
  * ``git``/``git+*``: for any other git repository

  Please note that a version is mandatory. If you really want to use the default branch, which is error-prone and therefore not recommended, you can set it to ``HEAD``.

* URL (=starts with http or https) of a ``*.repos`` or ``*.rosinstall`` file
* relative path of a ``*.repos`` or ``*.rosinstall`` file
* (relative) directory path to a source directory
* directory path prefixed with ``-`` to remove the directory, as a path relative to either the source space or the target repository
* ``.`` to copy the full target repository

For backwards compatibility, ``UPSTREAM_WORKSPACE`` can be set to ``debian`` and ``file`` as well, but not in combination with the other options and with a deprecation warning.
In case of ``file``, it will be replaced by ``$ROSINSTALL_FILENAME`` or ``$ROSINSTALL_FILENAME.$ROS_DISTRO``, if the latter exists.
**In "file" mode the target repository will not get removed automatically anymore and therefore might get built twice!**

Examples:
----------

To depend on a different GitHub repository, e.g. ros_control:
::

  UPSTREAM_WORKSPACE='github:ros-controls/ros_control#melodic-devel'


To depend on a different GitHub repository, e.g. ros_control, but only a subset of it:
::

  UPSTREAM_WORKSPACE='github:ros-controls/ros_control#melodic-devel -rqt_controller_manager'

**This does not remove the package, but the entire folder**

To depend on a remote rosinstall file instead, but still without ``rqt_controller_manager``:
::

  UPSTREAM_WORKSPACE='https://raw.githubusercontent.com/ros-controls/ros_control/melodic-devel/ros_control.rosinstall -ros_control/rqt_controller_manager'

Or to use a local copy:

::

  UPSTREAM_WORKSPACE='ros_control.rosinstall'

Works with (remote) ``*.repos`` as well:
::

  UPSTREAM_WORKSPACE='https://raw.githubusercontent.com/ros2/turtlebot2_demo/master/turtlebot2_demo.repos'

Or mixed:

::

  DOWNSTREAM_WORKSPACE="github:ros-simulation/gazebo_ros_pkgs#melodic-devel https://raw.githubusercontent.com/ros-controls/ros_control/melodic-devel/ros_control.rosinstall -ros_control additional.repos"

To depend on a different repository of a private server using git and the SSH protocol:
::

  UPSTREAM_WORKSPACE='git+ssh://git@private.server.net/repository#branch'

To filter the target workspace:
::

  TARGET_WORKSPACE='. -broken_package_path'
