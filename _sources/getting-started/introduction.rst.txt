Introduction
============

This package contains `CI (Continuous Integration) <https://en.wikipedia.org/wiki/Continuous_integration>`__ scripts that any ROS-powered packages can commonly use.
Some notable feature:

* Checks if your package builds, installs without issues. If unit/system tests are defined run them. `ROS Prerelease Test <http://wiki.ros.org/bloom/Tutorials/PrereleaseTest>`__ can optionally be run.
* Proven to cover the general requirements of the ROS-based robotics repositories. Easily configurable.
* Users can add custom pre/post processes.
* Covers ROS1 Indigo, Jade, Kinetic, Lunar, Melodic, Noetic and ROS2 distributions.
* This repo provides scripts for `Bitbucket CI`, `Gitlab CI`, `GitHub Actions` and `Travis CI` only, but it can be easily adapted for other CI services.

For a brief overall introduction, you could also check a presentation:

* `ROS-Industrial community meeting <http://rosindustrial.org/news/2016/6/14/ros-i-community-web-meeting-june-2016>`__