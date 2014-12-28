wrapencfs - wrap commands into encfs
====================================

wrapencfs wraps arbitary commands into a encfs call to provide
an encyrpted view onto a local directory using encfs --reverse.

The intended use are backups with rsync to remote destionations
that you don't trust.

It is based on the hoxu/encrb tool, but differs from encrb in that
it does not hardcode the actual rsync command but rather
provides a wrapper around the command one wants to call, which
gives the user full flexibility over the options to rsync, for
instance.

Example
-------

    $ ./wrapencfs $HOME/documents rsync -Pav {}/ some.server.eu:/home/user/backups/documents

First run will generate a random password, save it to KEYFILE and prompt for
encfs settings (pressing enter picks the defaults). Further runs will not
require any user input.

Usage
-----

    Usage: wrapencfs [options] /path/to/encrypt <command>
    Any occurence of the string "{}" in the command will be replaced by the encrypted path.
    
    Options:
      -h, --help            show this help message and exit
      -k KEYFILE, --keyfile=KEYFILE
                            encfs keyfile to use [~/.wrapencfs/encfs.xml]
      -p PASSFILE, --passfile=PASSFILE
                            file containing encfs keyfile password
                            [~/.wrapencfs/password]
      --no-env-check        Disable environment tool version checking
      -t CUSTOM_TMP, --tmpdir=CUSTOM_TMP
                            Define a custom tmp dir, otherwise the system default
                            will be used
      -v, --verbose         Verbose output


Backing up keyfile and password
-------------------------------

**Remember to back up the keyfile and the password**! If you lose them, you
will lose access to the encrypted data. Naturally you should not back up the
keyfile to the same place where you put the actual backups. And it is a very
good idea to have multiple backups of the keyfile (and password). For maximum
security, keep the data, the keyfile and the password at different locations.

Recovering backup
-----------------

To recover files from encrypted backup use:

    # ENCFS6_CONFIG=/path/to/encfs.xml encfs /path/to/ecrypted/backup /path/to/mountpoint

Dependencies
------------

* encfs 1.7.3 or later
* python2

License
-------

wrapencfs is licensed under GPLv3, see http://www.gnu.org/licenses/gpl-3.0.html
