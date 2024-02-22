# Commit Admins Guide

## Add a User to dgx0

- CSAIL users cannot access dgx0 directly. A new dgx0 username has to be created separately. To do so:
  
  `sudo adduser <username>`

- When asked for a kerberos password, leave it empty (so that the user can use their original kerberos password).

- The uid/gid for the newly created user needs to be matched with their CSAIL kerberos uid/gid for the user to be able to access their files on the NFS. First, obtain the kerberos uid for the user by running the command on the lanka login node (not the dgx0):

  `id <username>`

  Now, assign the same uid/gid to the newly created user on the dgx0 with the commands:

  `sudo usermod -u <id> <username>`

  `sudo usermod -g <id> <username>`

  Verify the user has the correct uid/gid by running the command on the dgx0:

  `id <username>`

## Enable a User to Use Slurm on Lanka

- To enable a user to use Slurm:

  `sudo sacctmgr add user name=<username> account=root`

