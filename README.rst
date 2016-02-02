Syntaxic sugar for mounting a path with ``ecryptfs`` and a key.


Disclaimer
==========

I'm not a security expert nor have any significant experience in
cryptography. This command circumvent what appears to me as
convolutions to reach the goal I was trying to achieve.

This command SHOULD NOT BE USED if you want to protect yourself
from attacker that could have any type of access to the host
where you'll execute this mount.

- the key is visible to anyone in the process list.

- the key appears in command history

- the key is in clear in the memory of the computer

You can use it to create encrypted backups on a safe computer
to send the encrypted version to an unsafe location. Please remember
thou, that you should not use this command on this remote location
even once to take a peek to your actual backup contents.


Please send me any comments about your concerns here so I can add some
general remarks on the solidity of this process.

And be aware that this solution in such light is maybe insecure.


Description
===========

Quick command to mount a directory with a password::

   ecryptfs-mount PATH KEY


Example
=======

Simple and quick session::

   $ mkdir /tmp/path
   $ ecryptfs-mount /tmp/path n0VmdZhgcs97Dik6_jG6V3Hjispo3P

Storing "hello" in ``afile``::

   $ echo "hello" > /tmp/path/afile

Let's unmount and see the result::

   $ umount /tmp/path
   $ ls /tmp/path/
   ECRYPTFS_FNEK_ENCRYPTED.FWaDRV66PWjAr-SLB2Obuz6Sf4pBONycQQbfoCLv5EndmxDcajuLjc4xHE--

There, it's encrypted.

You could then send this encrypted folder to another host::

    $ scp -r /tmp/path otherhost:path

\.. or mount it in a docker (note the ``--cap-add SYS_ADMIN``)::

    $ docker run -ti \
        -v /tmp/path:/root/protected \
        --cap-add SYS_ADMIN
        myimage

\.. or send it to a backup server::

    $ rsync -avRz /tmp/path otherhost:/backups

.. or tar it, compress it, and store it anywhere.

And if you want to get the content back, on any of these devices::

    $ ecryptfs-mount /encrypted/location n0VmdZhgcs97Dik6_jG6V3Hjispo3P
    $ ls /encrypted/location
    afile
    $ cat /encrypted/location/afile
    hello
    $ umount /encrypted/location

If you don't have the correct password::

    $ ecryptfs-mount /encrypted/location BAD_PASSWORD
    $ cat /encrypted/location/afile
    cat: /encrypted/location/afile: No such file or directory
    $ umount /encrypted/location


Mount Options
=============

You can set your desired mount options with environment variables::

    $ ecryptfs_enable_filename_crypto=y \
    > ecryptfs_passthrough=n \
    > ecryptfs_key_bytes=16 \
    > ecryptfs_cipher=aes \
    > ecryptfs-mount /mnt/path n0VmdZhgcs97Dik6_jG6V3Hjispo3P

If you don't, these are the default values.


Debugging
=========

If something goes wrong, please launch the command with setting
``$VERBOSE`` environment variables, for instance::

    VERBOSE=1 ecryptfs-mount /mnt/path n0VmdZhgcs97Dik6_jG6V3Hjispo3P
