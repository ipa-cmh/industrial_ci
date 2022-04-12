Quick Start
============

With a few steps, you can start in your client repository using CI confiurations stored in `industrial_ci`.

For Travis CI
--------------

1. Activate CI for your github repository on `Travis CI <https://travis-ci.com/>`__).

   a) You may do so either at https://travis-ci.com/github/YOUR_GITHUB_ORGANIZATION or at https://travis-ci.com/github/YOUR_GITHUB_USER (depending on where your repository sits).
   b) Activate CI beta for your bitbucket repository is similar, but the target link that your status badge directs to needs to be modified from https://travis-ci.com/USERNAME/REPO_NAME to https://travis-ci.com/bitbucket/USERNAME/REPO_NAME. By default, the generated link directs to a github repository.

2. Add `.travis.yml` file to your repository root (`complete template <https://github.com/ros-industrial/industrial_ci/blob/master/doc/.travis.yml>`__):

::

  language: generic
  services:
    - docker

  env:
    matrix:
      - ROS_DISTRO="indigo"

  install:
    - git clone --quiet --depth 1 https://github.com/ros-industrial/industrial_ci.git .industrial_ci -b master
  script:
    - .industrial_ci/travis.sh

* Note: The name `.industrial_ci` is NO longer REQUIRED for the cloned folder starting version 0.3.2; you can pick any name (recommended practice to keep the folder hidden (by prepending ".").

For Gitlab CI
-------------

1. Enable CI for your repo. Please refer to `official doc <https://docs.gitlab.com/ee/ci/quick_start/>`__ for the steps to do so. Note for Gitlab CI, necessary steps might be different between hosted version (i.e. the one on gitlab.com) v.s. the one on your own server, which Gitlab doesn't always clarify in its documentation.

   1. For your server version, enable a runner for your Gitlab project which uses the Docker executor. See instructions on how to `install <https://docs.gitlab.com/runner/install/index.html>`__ and `register <https://docs.gitlab.com/runner/register/index.html>`__ such a runner with your Gitlab instance if you haven't done so yet.

2. In `.gitlab-ci.yml` file in your client repo, add the following minimal configuration (this snippet can be the entire content of the file), replacing indigo for your chosen distro:

::

   image: docker:git
   services:
     - docker:dind
   before_script:
     - apk add --update bash coreutils tar
     - git clone --quiet --depth 1 https://github.com/ros-industrial/industrial_ci .industrial_ci -b master
   indigo:
     script: .industrial_ci/gitlab.sh ROS_DISTRO=indigo


For Bitbucket Pipelines
-----------------------

1. Enable CI for your repo. Please refer to `official doc <https://confluence.atlassian.com/bitbucket/get-started-with-bitbucket-pipelines-792298921.html>`__ for the steps to do so.
2. In the `bitbucket-pipelines.yml` file in your client repo, add the following minimal configuration (this snippet can be the entire content of the file), replacing indigo for your chosen distro:

::

   image: docker:git

   pipelines:
     default:
        - step:
            services:
              - docker
            script:
              - apk add --update bash coreutils tar
              - git clone --quiet --depth 1 https://github.com/ros-industrial/industrial_ci .industrial_ci -b master
              - .industrial_ci/bitbucket.sh ROS_DISTRO=indigo

   definitions:
     services:
       docker:
         memory: 2048


For GitHub Actions
-----------------------

1. Create `.github/workflows/industrial_ci_action.yml` (industrial_ci_action.yml is arbitrary, `complete template <doc/industrial_ci_action.yml>`__)  with the following configuration, (this snippet can be the entire content of the file), replacing melodic for your chosen distro:

::

   name: CI

   on: [push, pull_request]

   jobs:
     industrial_ci:
       strategy:
         matrix:
           env:
             - {ROS_DISTRO: melodic, ROS_REPO: testing}
             - {ROS_DISTRO: melodic, ROS_REPO: main}
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v1
         - uses: 'ros-industrial/industrial_ci@master'
           env: ${{matrix.env}}

For Google Cloud Build
----------------------
1. Connect your source code repository to your Google Cloud Project. Please refer to the `official documentation <https://cloud.google.com/build/docs/automating-builds/create-manage-triggers#gcloud>`__ for the steps to do so.
2. In the `cloudbuild.yaml` file in your client repo, add the following minimal configuration

::

  steps:
    - name: 'ros:melodic'
      entrypoint: 'bash'
      args:
        - '-c'
        - |-
          git clone --depth 1 https://github.com/ros-industrial/industrial_ci .industrial_ci -b master
          .industrial_ci/ci.sh
      env:
        - 'ISOLATION=shell'
        - 'CI=true'

