# Transfer Data From Local/Greene to OpenShift

## Before:

1. Connect to Openshift use OC
    
    ```bash
    oc login --token=XXXXX
    ```
    
2. Find the name of pod that you want to transfer file to 
    
    ```bash
    oc get pods
    ```
    

## Using OC Command

In OpenShift, the `oc cp` command allows you to copy files and directories between your local file system and a container within a pod. This is particularly useful for tasks such as transferring configuration files, backups, or other necessary data to and from your containers. 

### Copy a file from your local system to a container:

```bash
oc cp /local/path/to/file <pod-name>:/remote/path/to/destination
```

Example: 

```bash
oc cp ~/Test/test.txt ubuntu-0-nzkq5:/home/<your-netid>
```

This command copies the test.txt file from the current local Test directory to the your home directory in your pod on Openshift

### Copy a file from a container to your local system:

```bash
oc cp <pod-name>:/remote/path/to/file /local/path/to/destination
```

### **Copying Directories:**

While `oc cp` can be used to copy directories, it’s important to note that it may not handle symbolic links or file permissions as expected. For more robust directory synchronization, OpenShift provides the `oc rsync` command, which utilizes **rsync** to efficiently transfer directory contents.

```bash
# Synchronize a local directory to a directory in a pod:
oc rsync /local/dir/ <pod-name>:/remote/dir/
```

```bash
# Synchronize a directory from a pod to a local directory:
oc rsync <pod-name>:/remote/dir/ /local/dir/
```

Example:

```bash
oc rsync ./data/ my-pod:/var/lib/data/
```

This command synchronizes the contents of the local data directory to the /var/lib/data/ directory in the my-pod pod.

Linux Command (scp, rsync)

1. rsh to your active pod and start a bash session: 

```bash
oc rsh <pod-name> /bin/bash
```

1. modify ssh config file 

```bash
nano ~/.ssh/config
```

1. Append SSH client configuration 

```bash
Host dtn-1 log-1 log-2 log-3
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
  LogLevel ERROR
  IdentityFile /home/<your-netid>/.ssh/id_rsa
  User <your-netid>
```

Note: *dtn-1* is name of the file transfer node Greene use. And rest are login nodes.

1. Transfer a file from Greene to /home at current pod

```bash
scp dtn-1:/scratch/work/public/singularity/ubuntu-24.04.2.sif /home/<your-netid>
```

Note: In this example, we copy a Singularity image of Ubuntu, which is very large.

Similarly, transfer you can send a file back to greene through scp command.