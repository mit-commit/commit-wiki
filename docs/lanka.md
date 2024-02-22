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

As the frontend node is not the same architecture as the compute nodes, make sure to use the following compiler flags: `-O3 -march=corei7-avx -mtune=corei7-avx`.

### Turbo Boost

Turbo Boost has been disabled on all nodes to reduce timing inconsistencies and performance variability. This setting should remain off by default. Check with `cat /sys/devices/system/cpu/intel_pstate/no_turbo` (should return 1). You can fail if turbo boost is enabled with `[ "$(cat /sys/devices/system/cpu/intel_pstate/no_turbo)" = "1" ] || (echo "TurboBoost is on and could disrupt benchmarks. Failing..." && exit 1)`.