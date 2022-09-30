# ML HPC

This repository has been created as part of a demonstration for how to run Machine Learning Workloads on Compute Canada's High Performance Computing Cluster presented at the 2022 ACM International Conference on Mobile Human-Computer Interaction.

## Build PyTorch Container

```bash
sudo singularity build ml_hpc.image pytorch_singularity.def
```

### Transfer Code & Start an Interactive Session

The following details an example for transferring files to an HPC cluster, starting an interactive Slurm
session and training an example model included in this repository on a GPU in a Singularity container.

##### 1. Transfer Files to HPC Cluster

```bash
# Copy image to HPC cluster
scp ml_hpc.image <username>@<compute_cluster_address>:~/<compute_cluster_save_path>
# Compress code
tar --exclude='ml_hpc.image' --exclude='singularity.def' -cjvf /<path_to_compressed_files>/ml_files.tar.gz .
# scp ml_files.tar.gz to HPC cluster
scp ml_files.tar.gz <username>@<compute_cluster_address>:~/<compute_cluster_save_path>
# SSH into HPC cluster
ssh -Y <username>@<compute_cluster_address>
```

##### 2. Start Interactive Session on HPC Cluster

```bash
# Decompress code files (while on HPC cluster)
tar -xjvf ml_files.tar.gz
# Start an interactive session with a GPU node and two CPUs
salloc --time=0:20:0 --gres=gpu:1 --ntasks=1 --cpus-per-task=2 --mem=16G --account=<compute_canada_account>
# Load required modules for using Singularity and using NVIDIA GPU software
module load singularity/3.8
module load cuda/11.2.2 cudnn/8.2.0
# Get a shell to a Singularity container
singularity shell --nv --pwd /code --bind <path_to_cwd>/ml_hpc:/code/ml_hpc ml_hpc.image
```

##### 3. Train Example Model

```bash
# Activate virtual environment with dependencies installed
. ml-env/bin/activate
cd ml_hpc/
# train model found in __main__.py file in ml_model/
python -m ml_model
```

### Additional Resources

Slurm workload manager command line [cheat sheet](https://slurm.schedmd.com/pdfs/summary.pdf).
