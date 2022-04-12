Pre-release Test Configuration
==============================

Running `docker-based ROS Prerelease Test <http://wiki.ros.org/bloom/Tutorials/PrereleaseTest/>`__ is strongly recommended when you make a release. There are, however, some inconvenience (requires host computer setup, runs on your local host, etc. Detail discussed in `a ticket <https://github.com/ros-industrial/industrial_ci/pull/35#issue-150581346>`__). ``industrial_ci`` provides a way to run it on your CI.

To do so, add a single line to your `CI config <#terminology>`__:

::

  ROS_DISTRO=indigo PRERELEASE=true

Or with more configuration:

::

  ROS_DISTRO=indigo PRERELEASE=true PRERELEASE_REPONAME=industrial_core PRERELEASE_DOWNSTREAM_DEPTH=0

In addition to the downstream packages from ROS distro, you can specify ``UPSTREAM_WORKSPACE`` and ``DOWNSTREAM_WORKSPACE`` as well.

NOTE: A job that runs Prerelease Test does not run the checks that are defined in `travis.sh <https://github.com/ros-industrial/industrial_ci/blob/master/travis.sh>`__. To run both, use ``matrix`` in `CI config <#terminology>`__.

See the usage sample in `.travis in industrial_ci repository <https://github.com/ros-industrial/industrial_ci/blob/master/.travis.yml>`__.

The following is some tips to be shared for running Prerelease Test on CI using ``industrial_ci``.

(Workaround) Don't want to always run Prerelease Test
-----------------------------------------------------

The jobs that run Prerelease Test may usually take longer than the tests defined in `travis.sh <https://github.com/ros-industrial/industrial_ci/blob/master/travis.sh>`__, which can result in longer time for the entire CI jobs to finish. This is usually okay, as developers who are concerned with PRs might not wait for the CI result that eagerly (besides that, most CI servers limit the maximum run time as 50 minutes so there can't be very long run). If you're concerned, however, then you may want to separately run the Prerelease Test. An example way to do this is to create a branch specifically for Prerelease Test where `CI config <#terminology>`__ only defines a check entry with ``PRERELEASE`` turned on. E.g.:

::

  :
  env:
    matrix:
      - ROS_DISTRO=indigo PRERELEASE=true
  :

Then open a pull request using this branch against the branch that the change is subject to be merged. You do not want to actually merge this branch no matter what the CI result is. This branch is solely for Prerelease Test purpose.
