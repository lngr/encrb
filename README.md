encrb - encrypted remote backups
================================

encrb incrementally backs up local directories to a remote machine 
as encrypted files using encfs --reverse and rsync.

Example
-------

    $ ./encrb $HOME/documents $HOME/projects some.server.eu:/home/user/backups/

First run will generate a random password, save it to KEYFILE and prompt for
encfs settings (pressing enter picks the defaults). Further runs will not
require any user input.

Usage
-----

    $ ./encrb --help
    Usage: encrb [options] dir-to-back-up1... remotepath
    
    Options:
      -h, --help            show this help message and exit
      -k KEYFILE, --keyfile=KEYFILE
                            encfs keyfile to use [~/.encrb/encfs.xml]
      -p PASSFILE, --passfile=PASSFILE
                            file containing encfs keyfile password [~/.encrb/
                            password]
      -b BWLIMIT, --bwlimit=BWLIMIT
                            Bandwidth limit (KiB/s) for rsync [0]
      -P, --port=PORT       Port used by rsync [22]
      -v, --verify          Instead of backing up, verify that the backups 
                            match sources
      --backup-keyfile      Backup Encfs keyfile on remote machine - 
                            NOT RECOMMENDED
      --no-env-check        Disable environment tool version checking
      -i INCLUDEFILE, --include-from=INCLUDEFILE
                            Read rsync include patterns from file
      -e EXCLUDEFILE, --exclude-from=EXCLUDEFILE
                            Read rsync exclude patterns from file
      --no-delete           Do not delete existing remote files that are no 
                            longer present locally or have been excluded
      -P --Port				Port used by rsync (22 default)
      -t --tmpdir			Define a custom tmp dir where the reverse encfs will be mounted, if not set, the system default temp dir will be used.

Backing up keyfile and password
-------------------------------

**Remember to back up the keyfile and the password**! If you lose them, you
will lose access to the encrypted data. Naturally you should not back up the
keyfile to the same place where you put the actual backups. And it is a very
good idea to have multiple backups of the keyfile (and password). For maximum
security, keep the data, the keyfile and the password at different locations.
If remote environment is semi-trusted (e.g. your home server) you could use
"--backup-keyfile" parameter to backup keyfile remotely.

Excluding files and directories
-------------------------------

To exclude local files from being remotely backed up you can use
"--exclude-from" option. All entries must be *relative* to your backup
directory. For example, if you call encrb with "--exlcude-from ~/.encrb/
exclude.txt", and exclude.txt would have the following content:
    
    # Complete directories
    Downloads/
    
    # Contents of the directory, but not itself
    Pictures/*

    # Large files
    vm_disk.img

it would not backup "Downloads/" directory, the content of "Pictures/*" 
and "vm_disk.img" file remotely, *relative* to your backup dir. So if you are 
backing up "/home/user/" path, rsync would exclude "/home/user/Downloads/" 
directory and "home/user/" vm_disk.img" file completely, but would create an 
empty "Pictures" directory on the remote machine. Note: empty and lines 
starting with "#" are ignored.

No-delete sync
--------------

"--no-delete" option can protect from accidental local file deletion.
A good idea is to run encrb with this option on hourly basis and only
allow deletion (i.e. no option provided) on weekly basis thus still reclaiming
space on the remote machine but having an option for fast file recovery on
accidental file removal.


Running from crontab
--------------------

You most likely want to run encrb from crontab. I recommend using flock or
something similar to make sure no multiple instances are running, like this:

    30 0 * * * flock -n /tmp/encrb.lockfile /path/to/encrb --bwlimit 100 $HOME/docs $HOME/projects some.server.eu:/home/user/backups/

Recovering backup
-----------------

To recover files from encrypted backup use:

    # ENCFS6_CONFIG=/path/to/encfs.xml encfs /path/to/ecrypted/backup /path/to/mountpoint

Dependencies
------------

* encfs 1.7.3 or later
* python2
* rsync
* sshfs

License
-------

encrb is licensed under GPLv3, see http://www.gnu.org/licenses/gpl-3.0.html
