.. _acl-eauth:

==============================
External Authentication System
==============================

Salt's External Authentication System (eAuth) allows for Salt to  pass through
command authorization to any external authentication system, such as PAM or LDAP.

The external authentication system allows for specific users to be granted
access to execute specific functions on specific minions. Access is configured
in the master configuration file and uses the :ref:`access control system
<acl>`:

.. code-block:: yaml

    external_auth:
      pam:
        thatch:
          - 'web*':
            - test.*
            - network.*
        steve:
          - .*

The above configuration allows the user ``thatch`` to execute functions
in the test and network modules on the minions that match the web* target.
User ``steve`` is given unrestricted access to minion commands.

.. note:: The PAM module does not allow authenticating as ``root``.

To allow access to :ref:`wheel modules <all-salt.wheel>` or :ref:`runner
modules <all-salt.runners>` the following ``@`` syntax must be used:

.. code-block:: yaml

    external_auth:
      pam:
        thatch:
          - '@wheel'
          - '@runner'

The external authentication system can then be used from the command-line by
any user on the same system as the master with the ``-a`` option:

.. code-block:: bash

    $ salt -a pam web\* test.ping

The system will ask the user for the credentials required by the
authentication system and then publish the command.

Tokens
------

With external authentication alone, the authentication credentials will be
required with every call to Salt. This can be alleviated with Salt tokens.

Tokens are short term authorizations and can be easily created by just
adding a ``-T`` option when authenticating:

.. code-block:: bash

    $ salt -T -a pam web\* test.ping

Now a token will be created that has a expiration of 12 hours (by default).
This token is stored in a file named ``.salt_token`` in the active user's home 
directory.

Once the token is created, it is sent with all subsequent communications.
User authentication does not need to be entered again until the token expires.

Token expiration time can be set in the Salt master config file.
