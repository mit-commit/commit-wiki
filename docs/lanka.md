# Using the Lanka Cluster

Lanka is a 24-node Intel Xeon E5-2695 v2 @ 2.40GHz Infiniband cluster with two sockets per node, each with 12 cores, for a total of 576 cores and a theoretical peak computational rate of 11059 GFlop/s. Each node has 128GB of memory, of which ~120GB is safely usable.

To access the frontend node, `ssh <csail_username>@login.csail.mit.edu`, then `ssh slurm-login`. DO NOT ssh into any of the compute nodes.

**Important**: The cluster's compute nodes do not have access to afs. Therefore, you must create a directory in CSAIL's shared scratch file system corresponding to your username (i.e. `/data/scratch/<username>`) and do all cluster computing from that directory.

Email commit-sysadmin@lists.csail.mit.edu for an account.

## Running on Lanka with SLURM

We use the [SLURM](http://slurm.schedmd.com) workload manager for the batch system. If you're familiar with another batch system, the [rosetta stone](http://slurm.schedmd.com/rosetta.pdf) of workload managers may be helpful.

### Required SLURM Flags:

- `--partition`: Partitions available now are `lanka-v2` and `lanka-v3`

- `--qos`: the only acceptable value for us is `commit-main`.

- `--time`: in minutes. 

An example command would look like this `srun -N 1 -n 1 --qos commit-main --time=60 --mem 102400 --partition lanka-v3 --exclusive ls`

### Important commands

- `sbatch <file>`: submit `<file>` to the batch system (see below).
- `scancel <job_id>`: stop a job/delete it from the queue
- `squeue`: see queued jobs
- `sinfo -N -l`: view cluster status

### Running interactively

For debugging or testing, you can run interactively on the cluster. This is done with the `srun` command. For example, to run a interactively for 1 hour, you would run `srun --partition=lanka-v2 --qos=commit-main --time=01:00:00 --pty bash -i`.

### Running batch jobs

For most cases, the batch system is the way to go. Basically, write a simple batch script and submit it to the system via `sbatch <foo.batch>` and it will run when resources are available. For a full description of the `sbatch` syntax, see the man page or the [SLURM](http://slurm.schedmd.com) website, but a quick example is below:

```bash
#!/bin/bash
#SBATCH --partition lanka-v3
#SBATCH --qos commit-main
#SBATCH --tasks-per-node=24
#SBATCH -N 2
#SBATCH --cpu_bind=verbose,cores
#SBATCH --exclusive
#SBATCH -t 10
cd $SLURM_SUBMIT_DIR
srun --cpu_bind=verbose,cores ./hellompi
```

## Compiling for Lanka

Currently, MVAPICH2 is installed as the MPI implementation on the cluster. MPI compilers are located in `/usr/local/bin` and you may need to ensure `/usr/local/lib` appears in your `LD_LIBRARY_PATH`.

Building on csail login machines is discouraged. Instead, build on a lanka compute node. Note that the different kinds of lanka nodes are different architectures, so be careful about building on e.g. lanka-v2 and running on lanka-v3. [spack](https://spack.io/) may help for cross-compilation.

### Turbo Boost

Turbo Boost has been disabled on all nodes to reduce timing inconsistencies and performance variability. This setting should remain off by default. Check with `cat /sys/devices/system/cpu/intel_pstate/no_turbo` (should return 1). You can fail if turbo boost is enabled with `[ "$(cat /sys/devices/system/cpu/intel_pstate/no_turbo)" = "1" ] || (echo "TurboBoost is on and could disrupt benchmarks. Failing..." && exit 1)`.

## File Permissions and .bashrc

Since the compute nodes do not have direct access to AFS, where your `.bashrc` file is typically located, a common issue arises in maintaining environment settings across jobs. A practical solution is to utilize a soft link to your `.bashrc` within a directory inside AFS, configured with specific permissions to allow system-wide readability without the need for Kerberos tokens.

### Steps to Setup the Soft Link for `.bashrc`

1. **Create a Publicly Readable Directory in AFS**: First, you need to create a directory within your afs directory that will store your `.bashrc`. This directory must be set with permissions that allow `system:anyuser` to read and list contents. For example, if you're creating a directory named `public_configs` in your AFS space, you would use the following commands:

```bash
mkdir ~/public_configs
fs setacl ~/public_configs system:anyuser rl
```

2. **Move `.bashrc` to the Public Directory**: Move your `.bashrc` file to this newly created directory. This ensures that the file is accessible from compute nodes within the Lanka Cluster.

```bash
mv ~/.bashrc ~/public_configs/
```

3. **Create a Soft Link in Your Home Directory**: To ensure seamless integration with your environment, create a symbolic link in your home directory that points to the `.bashrc` file in the `public_configs` directory.

```bash
ln -s ~/public_configs/.bashrc ~/.bashrc
```

### Ensuring SLURM Jobs Source Your `.bashrc`

Now you may run your bashrc explicitly from a SLURM job:

```bash
source ~/public_configs/.bashrc
```


# Other Machines
## Non-Slurm Machine Etiquette

**Since these machines are shared, you need to coordinate with other users to avoid affecting other people's results! Please post in the lanka slack channel. Do not let others use these machines without making them do this!**

## The Lanka-dgx0 cluster.

The dgx0 machine has eight Tesla V100-SXM2-32GB GPUs. You can access the dgx0 machine via `ssh lanka-dgx0` from within csail.  We don't use SLURM on this machine so please try to coordinate with others in the lanka slack channel and least check via  `who`. To configure which GPU you use, set `CUDA_VISIBLE_DEVICES` to the desired GPU(s) (0-7). For more details on the machine, specifically the cuda drivers, check `nvidia-smi`. This machine is currently running ubuntu 20 (compared to 22) on the other machines.

## The bigram cluster.
The bigram machine is a Intel(R) Xeon(R) Platinum 8180 CPU cluster (with 224 processors) and with roughly three terrabytes of RAM. You can access the bigram machine via `ssh bigram` from within csail (e.g. from the login node). We don't use SLURM on this machine so please try to coordinate with others in the lanka slack channel and check via `who`.  Use the `taskset` command to configure which CPUs a given task uses.
