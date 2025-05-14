# Migrate Project from Greene to OpenShift
This guide walks you through migrating a custom environment (e.g., CUDA + Conda + PyTorch) from NYU Greene to OpenShift. It ensures consistent runtime, easy reuse, and cloud-HPC portability by leveraging Podman, Quay, and Singularity.

## What is ext3 in Singularity?

In Singularity, an ext3 image is a writable file-system-based container image format. It simulates a full file system and allows adding new files and modifying existing content without root privileges. This is essential in HPC systems where users cannot run privileged operations, and singularity build is typically restricted.

Using ext3 overlays allows local environments (e.g., Conda, custom Python packages) to be injected into a base Singularity image pulled from a remote registry such as Quay.

## Why Singularity
HPC environments typically do not allow container builds (e.g., singularity build) due to security restrictions. Users run containers with the same privileges as the host user, and no root access is granted inside the container. As a result, it’s not possible to install system packages or modify the base image on the cluster directly.

## Structure
```
[Local machine (with root access)]
        |
        | Use Podman to build an Ubuntu + CUDA + Conda environment
        v
[Local Podman image]
        |
        | Push image to Quay (for OpenShift or public access)
        v
[Quay-hosted OCI image] -- podman pull --> (reuse on Greene)
        |
        | Pull the image from Quay using Singularity or save image as a .tar
        v
[Local Test] -- on Greene -->
        |
        | Add & Test environment and application in Singularity on Greene
        V
[CP ext3]
        |
        | Make a copy of the current Singularity ext3 folder
        v
[Update the Image]
        |
        | CP the ext3 folder into Image by updating Dockerfile and rebuild. 
        v
[Run PyTorch scripts on OpenShift]

```

## Step 1: Build Environment Locally with Podman
On your local machine (with root access):
Dockerfile has been provided in  current folder.
```bash
# Example: Build an Ubuntu + CUDA + Conda environment
podman build -t myenv:cuda-conda -f Dockerfile
```
Your Dockerfile may include:
```bash
FROM nvidia/cuda:12.2.0-devel-ubuntu22.04
RUN apt-get update && apt-get install -y wget git
# Install Miniconda, PyTorch, etc.
```

## Step 2: Push Image to Quay
Follow the previous tutorial to open a repository on QUAY and push this custom image onto it.

## Step 2.5: (Optioanl) Save Image as a .tar
```bash
# Replace XXX with your Image
podman image save XXX
```
We can use singularity to pull from a .tar
```bash
# Replace XXX with your Image
singularity build XXX_tar.sif docker-archive://XXX.tar
```
Reference: https://docs.sylabs.io/guides/3.1/user-guide/singularity_and_docker.html

## Step 3: Pull and Test on Greene
We highly recommand you to test the Image on Greene using singularity, this will help you ensure a smooth transition between Greene and OpenShift.
```bash
# Pull from Quay
singularity pull docker://quay.io/<username>/myenv:cuda-conda
# Or SCP to Greene, or the dtn server
scp myenv.tar user@greene.hpc.nyu.edu:~
```
Executing containers with ext3 overlay. ou will need a overlay file for writable layer, available in /scratch/work/public/overlay/
```bash
singularity exec --overlay ext3/ myenv-cuda-conda.sif /bin/bash
bash /scratch/work/public/CondaSetup.sh
source ext3/env.sh
```
Now you can modify the this env on Greene. For example, you can use 'pip install' to add more libraries. Please test it before moveing to next step.

## Step 4: Manage Singularity ext3 Image
To persist and test with ext3 overlay:
```bash
# Make a backup
cp -r myenv.ext3 myenv_backup.ext3
# Mount and test PyTorch scripts
singularity exec myenv.ext3 python pytroch_test.py
```

## Step 5: Update the Image (Optional)
If changes are made to the container environment:
	1.	Modify the ext3 folder.
	2.	Rebuild your Dockerfile using updated ext3 contents.
	3.	Push the new image back to Quay.
```bash
# Example: Include custom ext3 as a layer
COPY myenv.ext3 /opt/env/myenv.ext3
```

### Step 6: Push and Run
You can now push the updated image to Quay and deploy it on OpenShift. Please refer to our YAML example(job-ubuntu.yaml) and update it with your own storage and GPU configuration.