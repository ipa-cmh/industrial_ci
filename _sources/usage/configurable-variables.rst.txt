General Configuration
=====================

Mandatory Environment variables
------------------------------------

You can configure the behavior in the `CI config <#terminology>`__ in your client repository.

Required environment variables:

.. csv-table:: Mandatory Environment Variables
   :header: "Variable", "Description"
   :delim: ;

    ``ROS_DISTRO``; Version of ROS in all lower case. E.g.: ``indigo``. If it is set in the custom Docker (base) image, it might be omitted in the script call.

Optional environment variables
-------------------------------

Note that some of these currently tied only to a single option, but we still leave them for the future when more options become available.

.. csv-table:: Optional Environment Variables
   :header: "Variable", "Default", "Description"
   :delim: ;

    ``ABICHECK_MERGE``; not set; Used only when ``ABICHECK_URL`` is set. For *Travis CI* it can be set to 'auto' to auto-detect pull requests. If set to ``true`` the merge parent (see `Terminology section <#terminology>`__) will be checked against.
    ``ABICHECK_URL``; not set; Run binary compatibility check with `ABICC <https://github.com/lvc/abi-compliance-checker>`__. The URL should point to a baseline archive (\*.tar.\*,\*.zip, \*.tgz or \*.tbz2). See more in `the ABI checks section <#abi-checks>`__)
    ``ABICHECK_VERSION``; not set; Used only when ``ABICHECK_URL`` is set. Version name (for display only) of the set of code, which the location is specified in ``ABICHECK_URL`` of. The version will be automatically read from the URL passed in ``ABICHECK_URL`` if possible, but for a URL that doesn't point to a version-based file name (e.g. the link for a tagged version on Gitlab doesn't).
    ``ADDITIONAL_DEBS``; not set;  More DEBs to be used. List the name of DEB(s delimitted by whitespace if multiple DEBs specified). Needs to be full-qualified Ubuntu package name. E.g.: ``ros-indigo-roslint ros-indigo-gazebo-ros``
    ``AFTER_SCRIPT``; not set;  Used to specify shell commands that run after all source tests. NOTE: `Unlike Travis CI <https://docs.travis-ci.com/user/customizing-the-build#Breaking-the-Build>`__ where ``after_script`` doesn't affect the build result, the result in the commands specified with this DOES affect the build result. See more `here <./index.rst#run-pre-post-process-custom-commands>`__.
    ``APT_PROXY``; not set;  Configure APT to use the provided URL as http proxy.
    ``BASEDIR``; ``$HOME``;  Base directory in which the upstream, target, and downstream workspaces will be built. Note: this directory is bind-mounted, so it can be read by the CI service, but its contents will not persist in the image configured by ``DOCKER_COMMIT``
    ``BLACK_CHECK``; not set;  If true, will check Python code formatting with `Black <https://black.readthedocs.io/en/stable/>`__.
    ``BUILDER``; ``catkin_tools`` for ROS1, ``colcon`` for ROS2;  Select the builder e.g. to build ROS1 packages with colcon (options: ``catkin_tools``, ``colcon``, ``catkin_make``, ``catkin_make_isolated``).
    ``CATKIN_LINT``; not set. Value range: [true|pedantic];  If ``true``, run `catkin_lint <http://fkie.github.io/catkin_lint/>`__ with ``--explain`` option. If ``pedantic``, ``catkin_lint`` command runs with ``--strict -W2`` option, i.e. more verbose output will print, and the CI job fails if there's any error and/or warning occurs. Industrial CI uses the `latest version available from pypi <https://pypi.org/project/catkin-lint/>`__. If the older version in the `ros repository <http://packages.ros.org/ros/ubuntu/pool/main/c/catkin-lint/>`__ is required, :code:`ADDITIONAL_DEBS='python-catkin-lint'` can be added to the CI Config.
    ``CATKIN_LINT_ARGS``; not set;  If true, you can pass whatever argument(s) ``catkin_lint`` takes, except ``--explain`` that is set by default. Options can be delimit by space if passing multiple.
    ``CC``/ ``CXX`` ; not set;  Environment variables to specify the C/C++ compilers. If required, these can be installed by specifying ``ADDITIONAL_DEBS``. E.g. ``ADDITIONAL_DEBS=clang CC=clang CXX=clang++`` uses Clang to build the workspaces. Note, these are the regular environment variables - and they work as pass-through in ``industrial_ci`` as well.
    ``CCACHE_DIR``; not set;  If set, `ccache <https://en.wikipedia.org/wiki/Ccache>`__ gets enabled for your build to speed up the subsequent builds in the same job if anything. See `detail. <https://github.com/ros-industrial/industrial_ci/blob/master/doc/index.rst#cache-build-artifacts-to-speed-up-the-subsequent-builds-if-any>`__
    ``CMAKE_ARGS``; not set;  CMake arguments that get passed to the builder for all workspaces.
    ``CLANG_FORMAT_CHECK``; not set. Value range: [``<format-style>``|``file``];  If set, run the `clang-format <https://clang.llvm.org/docs/ClangFormat.html>`__ check. Set the argument to ``file`` if the style configuration should be loaded from a ``.clang-format`` file, located in one of the parent directories of the source file.
    ``CLANG_FORMAT_VERSION``; not set;  Version of clang-format to install and use (relates to both the apt package name as well as the executable), e.g., ``CLANG_FORMAT_VERSION=3.8``.
    ``CLANG_TIDY``; not set. Value range: [``true``|``pedantic``];  If set, run `clang.tidy <https://clang.llvm.org/extra/clang-tidy/>`__ to check the code in all packages and fail in case of errors. If ``pedantic``, warnings will be treated as errors as well.
    ``CLANG_TIDY_ARGS``; not set;  Pass additional arguments to ``clang-tidy``, e.g. ``CLANG_TIDY_ARGS='-checks=modernize-*'``
    ``CLANG_TIDY_BASE_REF``; not set.;  If set, clang-tidy tests will be performed on files only that changed since the given ref. If not set, clang-tidy checks are performed on all files. More info see below.
    ``CLANG_TIDY_JOBS``; number of processors;  Maximum number of parallel jobs that execute ``clang-tidy``. The parallel processing is restricted to per build space (=one ROS package, except for ``BUILDER=catkin_make``)
    ``DEBUG_BASH``; not set;  If set with any value (e.g. ``true``), all executed commands that are not printed by default to reduce print space will be printed.
    ``DOCKER_COMMIT``; not set;  If set, the docker image, which contains the build and test artifacts, will be saved in a Docker image. If unset, the container will not be commited and is removed. The value is used to specify an image name during the ``docker commit`` command. *Note* while this allows you to use the resulting docker image with eg. `docker run -it <DOCKER_COMMIT> /bin/bash`, the main intended use is with the `rerun_ci` feature or subsequent `industrial_ci`runs, which also manages attaching the required volumes etc.
    ``DOCKER_COMMIT_MSG``; not set;  used to specify a commit during the docker commit command which is triggered by setting ``DOCKER_COMMIT``. If unset and if ``DOCKER_COMMIT`` is set then the commit message will be empty. See more ``DOCKER_COMMIT``.
    ``DOCKER_IMAGE``; not set;  Selects a Docker images different from default one. Please note, this disables the handling of ``ROS_REPOSITORY_PATH`` and ``ROS_DISTRO`` as ROS needs already to be installed in the image.
    ``DOCKER_PULL``; ``true``;  set to false if custom docker image should not be pulled, e.g. if it was created locally
    ``DOCKER_RUN_OPTS``; not set;  Used to specify additional run options for Docker.
    ``DOWNSTREAM_CMAKE_ARGS``; not set;  Addtional CMake arguments for downstream `workspace <#workspace-management>`__.
    ``DOWNSTREAM_WORKSPACE``; not set;  Definition of downstream `workspace <#workspace-management>`__.
    ``EXPECT_EXIT_CODE``; ``0``;  exit code must match this value for test to succeed
    ``IMMEDIATE_TEST_OUTPUT``; not set;  If true, test output is printed immediately during the tests
    ``NOT_TEST_BUILD``; not set;  If true, tests in ``build`` space won't be run.
    ``NOT_TEST_DOWNSTREAM``; not set;  If true, tests in the downstream workspace won't be run.
    ``OS_CODE_NAME``; derived from ROS_DISTRO;  See `this section for the detail <https://github.com/ros-industrial/industrial_ci/blob/master/doc/index.rst#optional-type-of-os-and-distribution>`__.
    ``OS_NAME``; derived from OS_CODE_NAME;  Possible options: {``ubuntu``, ``debian``}. See `this section for the detail <https://github.com/ros-industrial/industrial_ci/blob/master/doc/index.rst#optional-type-of-os-and-distribution>`__.
    ``PARALLEL_BUILDS``; 0;  Sets the number of parallel build jobs among all packages. ``0`` or ``true`` unsets the limit.
    ``PARALLEL_TESTS``; 1;  Sets the number of parallel test jobs. ``0`` or ``true`` unsets the limit.
    ``PREFIX``; not set;  Prefix string or directory for the workspaces created during the build job. The upstream, target, and downstream workspaces will be created at ``$BASEDIR/${PREFIX}<upstream_ws|target_ws|downstream_ws>``.
    ``PRERELEASE``; ``false``;  If ``true``, run `Prerelease Test on docker that emulates ROS buildfarm <http://wiki.ros.org/bloom/Tutorials/PrereleaseTest/>`__. The usage of Prerelease Test feature is `explained more in this section <https://github.com/ros-industrial/industrial_ci/blob/master/doc/index.rst#run-ros-prerelease-test>`__.
    ``PRERELEASE_DOWNSTREAM_DEPTH``; ``0``;  Number of the levels of the package dependencies the Prerelease Test targets at. Range of the level is defined by ROS buildfarm (`<http://prerelease.ros.org>`__). NOTE: a job can run exponentially longer for the values greater than ``0`` depending on how many packages depend on your package (and remember a job on Travis CI can only run for up to 50 minutes).
    ``PRERELEASE_REPONAME``; ``$TARGET_REPO_NAME``;  The name of the target of Prerelease Test in rosdistro (that you select at `<http://prerelease.ros.org>`__). You can specify this if your repository name differs from the corresponding rosdisto entry. See `here <https://github.com/ros-industrial/industrial_ci/pull/145/files#r108062114>`__ for more usage.
    ``ROS_REPO``; ``testing``;  ``ROS_REPO`` can be used to set ``ROS_REPOSITORY_PATH`` based on known aliases: ``ros``/``main``, ``ros-shadow-fixed``/``testing`` or ``building``. ``ROS_REPO=false`` disables the repository set-up.
    ``ROS_REPOSITORY_KEY``; not set;  Location of ROS' binary repository key, either as URL, file path or fingerprint.
    ``ROS_REPOSITORY_PATH``;; Location of ROS' binary repositories where depended packages get installed from (typically both standard repo (``http://packages.ros.org/ros/ubuntu``) and `"Shadow-Fixed" repository <http://wiki.ros.org/ShadowRepository>`__ (``http://packages.ros.org/ros-shadow-fixed/ubuntu``)). Since version 0.3.4, ``ROS_REPO`` is recommended, and ``ROS_REPOSITORY_PATH`` is for more intermediate usage only (e.g. to specify your own binary repository (non-standard / in house)). Backward compatibility is preserved.
    ``ROSDEP_SKIP_KEYS``; not set;  space-separated list of keys that should get skipped by ``rosdep install``.
    ``ROSINSTALL_FILENAME``; (*deprecated*, default: ``.travis.rosinstall``;  Only used when ``UPSTREAM_WORKSPACE`` is set to ``file``. See ``UPSTREAM_WORKSPACE`` description.
    ``PYLINT_ARGS``; not set;  pass command line arguments to ``pylint`` command (e.g. ``--output-format=parseable --errors-only``) - can e.g. be used to ``ignore_modules``
    ``PYLINT_CHECK``; false;  If ``true``, run ``pylint`` checks
    ``PYLINT_EXCLUDE``; not set;  can be used to exclude files via the ``-not -path`` filter
    ``TARGET_CMAKE_ARGS``; not set;  Addtional CMake arguments for target `workspace <#workspace-management>`__.
    ``TARGET_WORKSPACE``; ``$TARGET_REPO_PATH``;  Definition of sources for target `workspace <#workspace-management>`__.
    ``UNDERLAY``; not set;  Path to an install space (instead of ``/opt/ros/$ROS_DISTRO``) to be used as an underlay of the workspaces being set up be ICI, e.g. a workspace provided by a custom docker image
    ``UPSTREAM_CMAKE_ARGS``; not set;  Addtional CMake arguments for upstream `workspace <#workspace-management>`__.
    ``UPSTREAM_WORKSPACE``; not set;  Definition of upstream `workspace <#workspace-management>`__.
    ``VERBOSE_OUTPUT``; ``false``;  If ``true``, build tool (e.g. Catkin) output prints in verbose mode.
    ``VERBOSE_TESTS``; ``false``;  If ``true``, build tool (e.g. Catkin) output prints in verbose mode during ``run_tests`` step.


**CLANG_TIDY_BASE_REF**

For pull requests, you usually want to (re)test on changed files only. As all CI providers provide corresponding environment variables to recognize a PR, this can be easily configured, e.g. for github actions:

    :push does not check: ``${{ github.base_ref || github.ref }}``
    :push performs full check: ``${{ github.base_ref || '' }}``
    :manually trigger full check: ``${{ github.event_name != 'workflow_dispatch' && (github.base_ref || github.ref) || '' }}``
