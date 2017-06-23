# edgerouter-backup

Ubiquiti provides `system config-management commit-archive location` as a way to push configuration commits to a remote location. For my use, I have two problems with it:

1. It backups up in their bracket-y format instead of CLI commands.
2. They use a unique filename for each commit. I want to put my configuration files into source control, so having the same name is preferable.

This backup script hooks into the EdgeRouter `commit` process and generates both CLI and bracket-y style configuration files. The files are then sent to a remote server, and `git commit` and `git push` are run on the remote server -- avoiding having to install `git` on the EdgeRouter itself. This script can be used along side `config-management` or as a replacement.

These scripts do not modify any Ubiquiti-provided files. The locations of all the files will surivive reboots and should surivive a firmware upgrade (untested). I've personally tested this on an ER-8 and ER-X-SFP running v1.9.1 firmware. Should work on any EdgeRouter, might work on the USG / USG Pro as well.

### **IMPORTANT**

These configuration dumps **ARE NOT SANITIZED**. They may contain plaintext passwords for some services. **Do not publish to a publicly accessible git repo.**

### Installation

Copy contents of `config` directory to `/config` on EdgeRouter.

### Configuration

Edit `/config/user-data/edgerouter-backup.conf` with your information:

     #!/bin/bash

     # Path to private key for SSH / SCP
     SSH_KEYFILE=/config/user-data/backup_user_private.key
     SSH_USER=username
     SSH_HOST=host
     
     # Path to git repo on SSH_HOST
     REPO_PATH=\~/edgerouter-backup

     # Names for EdgeRouter configuration backup files.
     FNAME_CONFIG=$HOSTNAME.config.conf
     FNAME_CLI=$HOSTNAME.commands.conf

     
### Remote Host Setup

* Make sure your SSH key works.

* Create git repo in `REPO_PATH`.

* Configure repo as desired.

* Verify that `git commit -m "Commit Message"` and `git push` execute without interaction.
