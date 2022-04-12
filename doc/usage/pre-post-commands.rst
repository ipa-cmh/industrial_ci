Pre/post Command Configuration
===============================

You may want to add custom steps prior/subsequent to the setup defined in ``industrial_ci``. Example usecases:

* A device driver package X in your repository or in your repository's dependency requires a prorietary library installed. This library is publicly available, but not via apt or any package management system and thus the only way you can install it is in a classic way (unzip, run installer etc.) (`More discussion <https://github.com/ros-industrial/industrial_ci/issues/14>`__).

* You want to run ``ros_lint`` (`this discussion <https://github.com/ros-industrial/industrial_ci/issues/58#issuecomment-223601916>`__ may be of your interest).

Customize within the CI process
-------------------------------

If what you want to customize is within the `CI process <#what-are-checked>`__, you can specify the script(s) in ``BEFORE_*`` and/or ``AFTER_*`` variables.
The variables can be set for all functions, using the upper-case name, e.g. to run a script before ``install_target_dependencies`` you can specify ``BEFORE_INSTALL_TARGET_DEPENDENCIES`` or ``AFTER_INSTALL_TARGET_DEPENDENCIES`` to be run afterrwards.
``BEFORE_INIT`` will be run before anything else, ``AFTER_SCRIPT`` can be used to specify as script to be run after all successful tests.

For example::

  env:
    global:
      - BEFORE_INIT='./your_custom_PREprocess.sh'
      - AFTER_SCRIPT='./your_custom_POSTprocess.sh'
  script:
    - .industrial_ci/ci.sh

Multiple commands can be passed, as in a general ``bash`` manner.::

    - BEFORE_INIT='ls /tmp/1 && ls /tmp/2 || ls /tmp/3'

Multiple commands are easier to be handled if they are put into a dedicated script::

    - BEFORE_INIT='./my_before_script.sh'

NOTE: In general the scripts are run as root in a Docker container. If you configure a different (base) Docker image, the user could be changed to non-root. But since we need to install packages the (base) image should set-up ``sudo`` for this user.

The hooks will get run without a ROS environment (``setup.bash``).
If you need this environment, you can use the ``rosenv`` helper.
Optionally, it takes a command to be executed.

Examples:

* ``AFTER_SETUP_UPSTREAM_WORKSPACE='rosenv && echo "$ROS_DISTRO'"``
* ``AFTER_SETUP_UPSTREAM_WORKSPACE='rosenv ./my_script.sh'``

Furthermore, these  hooks scripts are run in a sub-shell and cannot change the build environment.
If a dependency needs to extend the build environment, the `*_EMBED` script can be used::

    - AFTER_INIT='./your_custom_PREprocess.sh'
    - AFTER_INIT_EMBED='source /opt/dependency/prepare_environment.sh'

**rosenv cannot be used in \*_EMBED hooks!**

Per default all scripts are run with unset variables disabled in bash.
It is possible to opt-out for an individual command by prefixing it with `ici_with_unset_variables`.

Customize outside of the CI process
------------------------------------

As `explained in Docker's usage <#use-custom-docker-images>`__ section, `main CI processes of industrial_ci <#what-are-checked>`__ run on *Docker*. There may be situations where you want to run additional processes before or after the main pipeline. This could be particularly the case when you'd like to take advantage of CI's native resources (e.g. environment variables your CI platform defines) more easily.

You can add your own commands before/after the main processes as follows.

::

  script:
    - ./your_non-docker_before.sh  <-- Runs on CI server natively.
    - .industrial_ci/ci.sh             <-- Runs on Docker on CI server.
    - ./your_non-docker_after.sh   <-- Runs on CI server natively.

NOTE. CI native env vars can be sent to Docker (see `this section <#pass-custom-variables-to-docker>`__). The example above is useful e.g. when you have many variables to deal with. Anyways, both ways are valid.
