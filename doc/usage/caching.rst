Cache Configuration
===========================================

Cache build artifacts to speed up the subsequent builds (if any).

If ``CCACHE_DIR`` is set (not set by default), `ccache <https://en.wikipedia.org/wiki/Ccache>`__ gets enabled for your build to speed up the subsequent builds in the same job if anything.
Recommended value is ``$HOME/.ccache``, but any non-used directory works.

https://docs.travis-ci.com/user/caching/#Arbitrary-directories

 * Enable cache. How to do so depends on the CI system of your choice.

   On Travis CI, add as follows (`refrence <https://docs.travis-ci.com/user/caching/#Arbitrary-directories>`__)::

    cache:
      directories:
        - $HOME/.ccache  # can be any valid cache location


 * Define ``CCACHE_DIR`` variable. You can apply to all of your jobs by something like below::

    env:
      global:
        - CCACHE_DIR=$HOME/.ccache
      matrix:
       :

Or define ``CCACHE_DIR`` per job.

NOTE:
  * Beware, if you use `run_ci <https://github.com/ros-industrial/industrial_ci/blob/master/doc/index.rst#id39>`__, the files will be owned by root!
  * Caching may not work for packages with "smaller" number of files (see also `this discussion <https://github.com/ros-industrial/industrial_ci/pull/182>`__).
  * With Gitlab CI, cache should always inside the project folder (`reference <https://docs.gitlab.com/ee/ci/yaml/README.html#cachepaths>`__)::

     variables:
       CCACHE_DIR: ${CI_PROJECT_DIR}/ccache

     cache:
       key: "${CI_JOB_NAME}"
       paths:
         - ccache