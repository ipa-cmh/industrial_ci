Operating System Configuration
===============================
*deprecated??*

Ubuntu and its distro are guessed by default from ROS_DISTRO
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

You can specify the OS and its distribution to run the CI job by setting ``OS_NAME`` and ``OS_CODE_NAME``.
By default users don't need to set this and its value will be automatically guessed according to the value of ``ROS_DISTRO``. e.g.:

* ``ROS_DISTRO=indigo``  --> ``OS_NAME=ubuntu OS_CODE_NAME=trusty``
* ``ROS_DISTRO=kinetic`` --> ``OS_NAME=ubuntu OS_CODE_NAME=xenial``
* ``ROS_DISTRO=lunar``   --> ``OS_NAME=ubuntu OS_CODE_NAME=xenial``
* ``ROS_DISTRO=melodic`` --> ``OS_NAME=ubuntu OS_CODE_NAME=bionic``

Use non-default Ubuntu distro
+++++++++++++++++++++++++++++

E.g. ``OS_CODE_NAME=yakkety`` or ``zesty`` for ROS Lunar are available.

Use Debian
++++++++++

E.g.:

* ``OS_CODE_NAME=jessie``
* ``OS_CODE_NAME=stretch``

All combinations available of OS and distros
++++++++++++++++++++++++++++++++++++++++++++++

Possible combination of ``OS_NAME`` and ``OS_CODE_NAME`` depend on available Docker images. See `ros-industrial/docker/ci <https://github.com/ros-industrial/docker/tree/master/ci>`__.
