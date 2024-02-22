# Using the Lanka Cluster

Lanka is a 24-node Intel Xeon E5-2695 v2 @ 2.40GHz Infiniband cluster with two sockets per node, each with 12 cores, for a total of 576 cores and a theoretical peak computational rate of 11059 GFlop/s. Each node has 128GB of memory, of which ~120GB is safely usable.

To access the frontend node, ssh to `lanka.csail.mit.edu`. DO NOT ssh into any of the compute nodes.

**Important**: The cluster's compute nodes do not have access to afs. Therefore, you must create a directory in CSAIL's shared scratch file system corresponding to your username (i.e. `/data/scratch/<username>`) and do all cluster computing from that directory.

Email commit-sysadmin@lists.csail.mit.edu for an account.

## Compiling for Lanka

Currently, MVAPICH2 is installed as the MPI implementation on the cluster. MPI compilers are located in `/usr/local/bin` and you may need to ensure `/usr/local/lib` appears in your `LD_LIBRARY_PATH`.

As the frontend node is not the same architecture as the compute nodes, make sure to use the following compiler flags: `-O3 -march=corei7-avx -mtune=corei7-avx`.

## Running on Lanka

We use the [SLURM](http://slurm.schedmd.com) workload manager for the batch system. If you're familiar with another batch system, the [rosetta stone](http://slurm.schedmd.com/rosetta.pdf) of workload managers may be helpful.

### Important commands

- `sbatch <file>`: submit `<file>` to the batch system (see below).
- `scancel <job_id>`: stop a job/delete it from the queue
- `squeue`: see queued jobs
- `sinfo -N -l`: view cluster status

### Running batch jobs

For most cases, the batch system is the way to go. Basically, write a simple batch script and submit it to the system via `sbatch <foo.batch>` and it will run when resources are available. For a full description of the `sbatch` syntax, see the man page or the [SLURM](http://slurm.schedmd.com) website, but a quick example is below:

```bash
#!/bin/bash
#SBATCH --tasks-per-node=24
#SBATCH -N 2
#SBATCH --cpu_bind=verbose,cores
#SBATCH --exclusive
#SBATCH -t 10
cd $SLURM_SUBMIT_DIR
srun --cpu_bind=verbose,cores ./hellompi
```