# HPC_DOC for DSI CLUSTER
# SSH
cnetid is the username for the cluster. The password is the same as the one used for the cnetid network. 
```
ssh cnetid@fe01.ds.uchicago.edu
```
## With key
```
ssh-keygen
ssh-copy-id -i ~/.ssh/id_rsa.pub ssh cnetid@fe01.ds.uchicago.edu
```
## With Config
```
Host dsi
    HostName fe01.ds.uchicago.edu
    User cnetid
```
Now you can ssh to the cluster by typing
```
ssh dsi
```
# SLURM
## Interactive Job
```
srun -p general -t 4:00:00 --cpus-per-task=16 --mem=40GB --gres=gpu:1 --pty /bin/bash
```
Now we are in the interactive job. We can run your code here, the disk space is mounted as the same the one on the login node.

## Coding and Mounting
If we want to write code on the server, one way is to use vim. Or we can use vscode ssh to the login node, as the disk space is also mounted on the computation node, so we will see the same files on the login node and the computation node.

## Sample Code
```python
# $cnetid is the username for the cluster
#/home/$cnetid/test.py
def main():
    print("Hello World")
    # write log
    with open("log.txt", "w") as f:
        f.write("Hello World")

if __name__ == "__main__":
    main()
```
We can run the code on computation node if we are in the interactive job. Or we can submit a job to run the code.
```
srun -p general -t 4:00:00 --cpus-per-task=16 --mem=40GB --gres=gpu:1 --pty "python test.py"
```
## Submitting a Job
### Sample SLURM Script
We can write a slurm script to submit a job. The sample slurm path is /home/$cnetid/test.slurm

```bash
#!/bin/bash
#SBATCH --job-name=test
#SBATCH --output=test.out
#SBATCH --error=test.err
#SBATCH --partition=general
#SBATCH --time=4:00:00
#SBATCH --cpus-per-task=16
#SBATCH --mem=16GB
#SBATCH --gres=gpu:1 # 1 GPU, for sample code, we don't need GPU

python test.py
```

```
sbatch -p general -t 4:00:00 --cpus-per-task=16 --mem=40GB --gres=gpu:1 test.slurm
```

If we want to know the explanation of the parameters, we can try this good [website](https://explainshell.com/explain?cmd=sbatch+-p+general+-t+4%3A00%3A00+--cpus-per-task%3D16+--mem%3D40GB+--gres%3Dgpu%3A1+test.slurm)


## Checking the Job Status

```
squeue -u $cnetid
```
## Canceling a Job

```
scancel $jobid
```
## Checking the Job Output/Error

```
cat test.out
cat test.err
```

# Storage
TLDR: Save your code on /home/$cnetid, save your data on /net/scratch/$cnetid, and back your data periodically, especially for /net/scratch/$cnetid.

**There is no backup for /net/scratch/$cnetid and the files will be cleaned periodically** (maybe every 2 months).




# Acknowledgements
Also please check
- https://github.com/chicago-cdac/clinic-sample/blob/main/docs/slurm.md

The repo borrows heavily from the following repo:
- https://github.com/PrincetonUniversity/hpc_beginning_workshop
Note: The repo is not tested on the cluster. Please use it as a reference only.

