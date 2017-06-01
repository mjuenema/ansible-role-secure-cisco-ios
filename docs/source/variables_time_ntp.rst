Time and NTP
~~~~~~~~~~~~

Set timezone
^^^^^^^^^^^^

.. code-block:: yaml

    secure_cisco_ios_timezone: UTC
    secure_cisco_ios_timezone_offset_hours: 0
    secure_cisco_ios_timezone_offset_minutes: 0

Configure the router as an NTP client
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: yaml

    secure_cisco_ios_ntp_servers: []

``secure_cisco_ios_ntp_servers`` must be a list of dictionaries with the following keys.

-  ``server`` (required)
-  ``version`` (1-4)
-  ``iburst``
-  ``burst``
-  ``minpoll`` (2^x seconds)
-  ``maxpoll`` (2^x seconds)
-  ``prefer``
-  ``source`` (source interface)
-  ``key`` (authentication key)

Example:

.. code-block:: yaml

    secure_cisco_ios_timezone: AEST
    secure_cisco_ios_timezone_offset_hours: 10
    secure_cisco_ios_timezone_offset_minutes: 0
    secure_cisco_ios_ntp_servers:
      - {'server': 10.2.3.5', 'source': 'Loopback0'}
      - {'server': 10.2.3.4', 'iburst': true, 'minpoll': 4, 'source': 'Loopback0'}

Configure the router as an NTP server
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: yaml

    secure_cisco_ios_ntp_master: false

Configure NTP keys
^^^^^^^^^^^^^^^^^^

The keys are a list of cleartext keys, MD5 hashing
will be performed automatically. The keys are numbered sequentially,
i.e. the second key in the list will have a key ID of 1 as the numbering
starts with 0. This is implemented by `Looping over a list with an
index <https://docs.ansible.com/ansible/playbooks_loops.html#looping-over-a-list-with-an-index>`__

.. code-block:: yaml

    secure_cisco_ios_ntp_keys: []

Example

.. code-block:: yaml

    secure_cisco_ios_ntp_keys:
      - CLEARTEXTKEY1
      - CLEARTEXTKEY2

    secure_cisco_ios_ntp_servers:
      - {'server': 10.2.3.5', 'key': 2}
      # Use CLEARTEXTKEY2 for server 10.2.3.5
