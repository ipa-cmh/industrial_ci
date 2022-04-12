Running Locally
===============================

There are a few ways to run CI jobs locally.

Simplest way to run locally
++++++++++++++++++++++++++++++++

Since version 0.3.3, you can run ``industrial_ci`` on your local host. This can be useful e.g. when you want to integrate industrial_ci into your CI server.

NOTE that this way the CI config (e.g. ``.travis.yml``, ``.gitlab-ci.yml``) are not used. So whatever configurations you have in your CI configs need to be added manually.

To do so,

0. `Install Docker <https://docs.docker.com/engine/installation/linux/>`__
1. Build and install industrial_ci (which is `a catkin package <http://wiki.ros.org/ROS/Tutorials/CreatingPackage#ROS.2BAC8-Tutorials.2BAC8-catkin.2BAC8-CreatingPackage.What_makes_up_a_catkin_Package.3F>`__). Source setting.
2. Change directory to the package you like to test.
3. Run ``run_ci`` script with your settings.

Example:

::

  $ cd ~/cws/src && git clone https://github.com/ros-industrial/industrial_ci.git -b master && cd ~/cws
  $ catkin config --install
  $ catkin b industrial_ci
  $ source install/setup.bash
  $ roscd ros_canopen   (or any package you test)
  $ rosrun industrial_ci run_ci ROS_DISTRO=indigo ROS_REPO=main

(ROS_DISTRO could be read from your environment as well)

Run locally using Travis config
++++++++++++++++++++++++++++++++

Since v0.6.0, you can run locally using ``.travis.yml`` you already defined for your repository, using `industrial_ci/scripts/run_travis script <https://github.com/ros-industrial/industrial_ci/blob/master/industrial_ci/scripts/run_travis>`_. See the help of that script.

::

   rosrun industrial_ci run_travis --help

Run locally using GitHub-Workflow config
++++++++++++++++++++++++++++++++++++++++

You can run GitHub actions locally using `act <https://github.com/nektos/act>`__.
For its installation, follow the `official install instructions <https://github.com/nektos/act#installation>`__.
Installation in short:

* install docker engine;
* install ``act`` using `bash script <https://github.com/nektos/act#bash-script>`__ (tested on Ubuntu and its derivatives) or download the `static binaries <https://github.com/nektos/act/releases>`__;
* When asked about which ``act`` image you would like to install, choose medium (default choice).

Before running a GH-Action locally, please check that you are using the `industrial_ci` as follows:

::

  - uses: ros-industrial/industrial_ci@master
    env:
      ROS_DISTRO: ${{ matrix.ROS_DISTRO }}
      ROS_REPO: ${{ matrix.ROS_REPO }}

Or for more complicated cases:

::

  - uses: ros-industrial/industrial_ci@master
      with:
        config: ${{toJSON(matrix.env)}}

Often used configuration is actually not supported by GH (more details in #590)

::

  - uses: ros-industrial/industrial_ci@master
      env: ${{matrix.env}}

After that, go to the package you would like to test and start a workflow using act:

* ``act`` - execute all workflows
* ``act -l`` - list all defined workflows
* ``act -j <my_workflow>`` - execute specific workflow

Some useful flags:

* to get more detailed output, use ``-v`` flag
* to reuse action containers, use ``-r`` flag (makes your actions much faster)
* for everything else check `act flags <https://github.com/nektos/act#flags>`__

Recurring runs for debugging
++++++++++++++++++++++++++++
Please note that ``run_ci`` and ``run_travis`` will download all dependencies every time, just as CI services would do.
For recurring runs, e.g. in a debugging session, this might not be desired.

As an alternative ``rerun_ci`` could be used. It take the same argument as ``run_ci`` (note for `some limitations <#note-for-rerun-ci-limitations>`__), but will run the build incrementally and only download or compile after changes.

This results in much faster execution for recurring runs, but has some disadvantages as well:

* The user needs to clean-up manually, an instruction to do so is printed at the end of all runs.
* All parameters incl. the repository path have to be passed explicitly to allow for proper caching.
* The apt dependencies won't get updated in recurring runs.
* Incremental builds might not work properly for all cases. Especially, it does not help with prerelease tests.

Example:

::

  $ rosrun industrial_ci rerun_ci . ROS_DISTRO=melodic ROS_REPO=main

This will run the tests and commit the result to a Docker image ``industrial-ci/rerun_ci/ros_canopen:$HASH``.
The hash is unique for each argument list, so ``rerun_ci . ROS_DISTRO=melodic`` and ``rerun_ci . ROS_DISTRO=kinetic`` do not mix  up.
However, it will keep consuming disk space with each new combination.

The cached images can be listed with
::

  $ rosrun industrial_ci rerun_ci --list

Note for rerun_ci limitations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``rerun_ci`` is managing ``DOCKER_COMMIT`` and ``DOCKER_COMMIT_MSG`` variables under the hood, so if the user set them they will not take effect, unlike `normal cases <#re-use-the-container-image>`__.

If you are using this feature to have a cached way to run ci locally you probably want your dependencies to be updated just as they are when run on a remote ci service.  To achieve this you can cause the target workspace to be pulled by adding this argument: ``AFTER_SETUP_TARGET_WORKSPACE='vcs pull ~/target_ws/src/'``.
