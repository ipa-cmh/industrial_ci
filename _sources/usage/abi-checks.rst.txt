ABI Check Configuration
=======================

Generally speaking, the `ABI <https://en.wikipedia.org/wiki/Application_binary_interface>`__ of a library can break for various reasons. A detailed explanation and a list of DOs and DON'Ts can be found in the `KDE Community Wiki <https://community.kde.org/Policies/Binary_Compatibility_Issues_With_C%2B%2B>`__.

The ABI checks with ``industrial_ci`` can be enabled by setting 'ABICHECK_URL' to the **stable version** of your code.

ABI check example configs
+++++++++++++++++++++++++

Simplest example: Check against a specific stable branch (e.g. ``kinetic`` branch) for push and pull request tests::

  - ROS_DISTRO=kinetic
    ABICHECK_URL='github:ros-industrial/ros_canopen#kinetic'

If pull requests should be checked against the merge parent instead of the stable version (Travis CI only). The only benefit is that PRs might pass even if the target branch breaks the ABI to the stable version.::

  - ROS_DISTRO=kinetic
    ABICHECK_URL='github:ros-industrial/ros_canopen#kinetic'
    ABICHECK_MERGE=auto

URL can be specified in shortcut form ``provider:organization/repository#version``, which is supported for bitbucket, github and gitlab. "``version``" can be either one of the name of the branch, the tagged version, or even a commit. Some (more) concrete examples:

- github:ros-industrial-release/ros_canopen-release#upstream
- gitlab:ipa-mdl/ci-example#master
- github:ros-planning/moveit#0.9.9

Alternatively you can use the following forms as URL.:

- https://github.com/ros-industrial/ros_canopen/archive/kinetic.zip
- https://github.com/ros-industrial-release/ros_canopen-release/archive/upstream.zip
- https://gitlab.com/ipa-mdl/ci-example/repository/master/archive.zip
- https://github.com/ros-planning/moveit/archive/0.9.9.tar.gz

With this format, the URL needs to point to an actual archive. E.g. on GitHub, URL for a branch's archive can be https://github.com/organization/repository/archive/branch.zip

Tips for ABI check feature
++++++++++++++++++++++++++

It is up to each repository's maintainer for which baseline code you check ABI against. Here are some recommendations per possible situation:

- Development branch and stable branch (i.e. mirroring the released code) are separately maintained --> checking against stable branch.
- No stable branch -->

  - Check against the stable tagged version.
  - Or you could check against the same branch. This way:

    - ABI check runs per every change/push into your branch, which is superfluous.
    - Reasonable for pull requests.