Docker Configuration
=====================

Use custom Docker images
------------------------

As you see in the `optional variables section <./index.rst#optional-environment-variables>`__, there are a few different ways to
specify *Docker* image if you like. Here are some more detail:

Pulling Docker image from an online hub
+++++++++++++++++++++++++++++++++++++++

You can pull any *Docker* image by specifying in ``DOCKER_IMAGE`` variable.
If your *Docker* image is ROS-based, you can omit ``ROS_DISTRO`` as long as the Dockerfile sets this environment variable (``ENV ROS_DISTRO``).
However, ``ROS_REPO`` (or non-recommended ``ROS_REPOSITORY_PATH``), and ``ROS_DISTRO`` can still be used to modify the target container.

Please note that the entrypoint and command of the image will get ignored.

Pass custom variables to Docker
-------------------------------

On CI platform usually some variables are available for the convenience. Since all checks using ``industrial_ci`` are NOT running directly on
the operating system running on CI, but instead running on *Docker* where those variables are not defined, dozens of them are already passed
for you (you can see `the list of those variables <https://github.com/ros-industrial/industrial_ci/blob/master/industrial_ci/src/docker.env>`__).

Still, you may want to pass some other vars. ``DOCKER_RUN_OPTS='-e MY_VARIABLE_VALUE'`` should do the trick.
You can even set it to a specific value: ``DOCKER_RUN_OPTS='-e MY_VARIABLE_VALUE=42'`` (format varies per CI platform. These are Gitlab CI example).

Re-use the container image
--------------------------

NOTE: This is still experimental.

``industrial_ci`` builds a *Docker* image using the associated repository on the specified operating system per every job. While the built Docker container is thrown away once the job finishes by default, there's a way to access the built image post job so that you can re-use it.

To do so, simply set ``DOCKER_COMMIT`` the name of the image of your choice. Then you'll be able to access that image. For example in your CI config (e.g. ``.travis.yml``), add something like ::

  variables:
      DOCKER_COMMIT=registry.gitlab.com/your-org/your-repo:your_img
  :
  script:
      - docker push $DOCKER_COMMIT
