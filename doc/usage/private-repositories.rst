Private Repositories Configuration
==================================

Gitlab CI
---------

If your Gitlab CI jobs require access to private repos, additional settings are needed both on:

- Your repo: Add ssh private keys in the CI settings.
- The private repos the CI jobs access: Matching public keys must be set as ``Deploy Key``.

#. If you haven't done so, create SSH key pair (`reference on gitlab.com <https://docs.gitlab.com/ce/ssh/README.html#generating-a-new-ssh-key-pair>`__).
#. Navigate to "Settings > CI/CD" in your repo.
#. Expand "``Secret variables``" section.
#. In "Add a variable" section, fill in the following text field/area.

   #. **Key**: ``SSH_PRIVATE_KEY``
   #. **Value**: Copy paste the entire content of your private key file.

     #. Include the header and footer, i.e.  ``-----BEGIN/END RSA PRIVATE KEY-----``.
#. In "Add a variable" section again, fill in the following text field/area.

   #. **Key**: ``SSH_SERVER_HOSTKEYS``
   #. **Value**: Copy paste the entire line of the following: On your Linux computer, run ``ssh-keyscan gitlab.com``. You should get a hash key entry/ies. Copy the entire line that is NOT commented out. For example, the author gets the following, and copied the 2nd line (, which may render as separate lines on your web browser, but it's a long single line):

     ::

      # gitlab.com:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.2
      gitlab.com ssh-rsa RandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequence
      # gitlab.com:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.2
      gitlab.com ecdsa-sha2-nistp256 RandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequenceRandomKeySequence
      # gitlab.com:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.2

#. Add a public key (reference for `Gitlab <https://docs.gitlab.com/ce/ssh/README.html#deploy-keys>`__ and for `GitHub <https://developer.github.com/v3/guides/managing-deploy-keys/#deploy-keys>`__) to the private repos your CI jobs accesses. You may need to ask the admin of that repo.
#. If you are using Docker-in-Docker, make sure that ``TMPDIR`` is set in your ``.gitlab-ci.yml`` file so that the SSH agent forwards properly ::

    # The docker runner does not expose /tmp to the docker-in-docker service
    # This config ensures that the temp folder is located inside the project directory (e.g. for prerelease tests or SSH agent forwarding)
    variables:
      TMPDIR: "${CI_PROJECT_DIR}.tmp"
#. If using a self-signed certificate you may need to make the container aware of the runner's certs ::

    kinetic:
      script:
        # Run the gitlab script, exposing the runner's SSL certs.
        - .industrial_ci/gitlab.sh DOCKER_RUN_OPTS="-v /etc/ssl/certs:/etc/ssl/certs:ro"


References:

- https://docs.gitlab.com/ce/ssh/README.html
- https://docs.gitlab.com/ee/ci/ssh_keys/README.html