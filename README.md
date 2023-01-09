# Setting up conda environments in OPS server without internet connection

Below is a guide to set up the conda environment on the OPS server.
Since the OPS server is not connected to the internet, we have to set up
a mirror environment first on the internet PC (via the **Ubuntu Virtualbox**) and
then packaged it and transfer to the OPS PC manually via thumbdrive to be uploaded to the OPS 
server.

## 1) Download and set up miniconda on the Internet PC.

A specific version of the **miniconda** software should have already been downloaded and saved on the internet PC. 
If it is not available, we will download the [latest miniconda][latest miniconda] version. 
Next, we will install it into a designated folder **mfo-conda-envs** on the **Ubuntu Virtualbox**. 

Skip this section if the miniconda base/root environment has already been set up previously.

```console
mssop1@tnmo:~$ bash Miniconda3-py310_22.11.1-1-Linux-x86_64.sh
```

Do not use the default destination "miniconda3" folder
during the installation process. Install it in the **mfo-conda-envs** folder.
Choose "yes" when asked whether to initialize Miniconda3 by running `conda init` at the end the installation process.
After the process is completed, close and relaunch the terminal.

We will then need to install 2 additional packages `conda-pack` and `cookiecutter` to the base/root environment.

```console
(base) mssop1@tnmo:~$ conda install conda-pack cookiecutter --channel conda-forge
```

After installing the additional packages, we package the base environment 
into to a file **mfo-conda-base.tar.gz**. 
This file need to be transferred and uploaded to the OPS server to 
build the conda base environment over there.

 ```console
(base) mssop1@tnmo:~$ conda-pack --output mfo-conda-base.tar.gz
```

## 2) To create a new conda environment and package it into a file

We would now try to create an example of a new application environment named `jupyterlab-env`

```console
(base) mssop1@tnmo:~$ conda create --name jupyterlab-env nb_conda_kernel jupyter_contrib_nbextensions jupyterlab --channel conda-forge
```

After creating the new `jupyterlab-env`, we can now package it into a file **jupyterlab-env.tar.gz**. 
Please ensure that we are in the `base` environment before we execute the following command.

```console
(base) mssop1@tnmo:~$ conda-pack --name jupyterlab-env
```

Similarly, the resultant output file **jupyterlab-env.tar.gz** need to be 
transferred and uploaded to the OPS server to build the corresponding conda environment over there.

---
**Note** 

Remember to install the package **ipykernel** for each new conda environment you plan to create. 
The purpose is for all these environments to be accessible from **jupyterlab** 
launched using the `jupyterlab-env` environment. Hence, there is no need to duplicate 
and install **jupyterlab** in all environments. 

---
## 3) Set up and initialize the base environment on the OPS server

**mfo-conda-base.tar.gz (prepared on internet PC) --> set up as conda base/root environment on OPS server**

We would designate a specific folder **mfo-conda-envs** to house all the conda environments 
for separate projects/applications. 
Create the folder if it does not exists.

```console
[svc_ten_sftp@MSSPNLPSCRAPA ~] $ mkdir mfo-conda-envs
```

We need to set up the conda **base** environment first by extracting the 
**mfo-conda-base.tar.gz** file into the **mfo-conda-envs** folder, then activate
and initialize it as our customized conda base environment. 
If the base environment has already been set up and initialized previously in the folder, 
then we can skip all subsequent steps below: 

```console
[svc_ten_sftp@MSSPNLPSCRAPA ~] $ tar -xzf mfo-conda-base.tar.gz -C mfo-conda-envs
```

```console
[svc_ten_sftp@MSSPNLPSCRAPA ~] $ source mfo-conda-envs/bin/activate
```
    
```console
(base) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ conda-unpack
```

```console
(base) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ conda init
```

After the executing the `conda init` command, close and relaunch the terminal. 
This completes the setup of our customized conda base environment in the OPS server. 
From now on, we are ready to add new environments for all our 
projects/applications into the **mfo-conda-envs** folder.

## 4) To set up subsequent new conda environments for our projects

**(2 example files prepared on internet PC) atmos-env.tar.gz, jupyterlab-env.tar.gz --> Set up 2 environments: atmos-env & jupyterlab-env on OPS server**

Let's assume we would like to call our example new conda environments `atmos-env` and `jupyterlab-env`. 
We first need to create the `atmos-env` and `jupyterlab-env` folders 
within the **envs** folder located inside the **mfo-conda-envs** folder.

```console
(base) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ mkdir -p mfo-conda-envs/envs/atmos-env
```

```console
(base) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ mkdir -p mfo-conda-envs/envs/jupyterlab-env
```

Once these folders are created, we can start to extract for example 
the **atmos-env.tar.gz** file into its corresponding environment folder
and activate it. 

```console
(base) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ tar -xzf atmos-env.tar.gz -C mfo-conda-envs/envs/atmos-env
```

```console
(base) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ conda activate atmos-env
```

```console
(atmos-env) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ conda-unpack
```

```console
(atmos-env) [svc_ten_sftp@MSSPNLPSCRAPA ~] $ conda deactivate
```

This completes the setup of the `atmos-env` environment. 
Then we will repeat the same steps above for the `jupyterlab-env`.

Thus, we have successfully transferred the conda environments 
originally set up on the internet PC to the OPS server.



[latest miniconda]: https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
