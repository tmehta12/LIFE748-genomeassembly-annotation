# Install Conda on WSL (~ 5 min)

1. Open WSL terminal with Ubuntu (e.g., "Windows PowerShell" > type `wsl` > type `cd`)
1.  Download the Miniconda installer:
Use the `wget` command to download the Miniconda installer for Linux from the official site. For the latest version, you should check the Miniconda page for the correct link. 
`#` will be used to add notes to the code

```
#make a new dir
mkdir -p ~/miniconda3 

#use wget to download file
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh

#run the downloaded file - this will install the contents
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3

#delete the file
rm -rf ~/miniconda3/miniconda.sh
```

2. Initialise your shell:
After installing, initialise your newly-installed Miniconda. The following commands initialise for bash and zsh shells:
```
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```

### Check and update conda 

```
#source your `~/.bashrc` file by typing:
source ~/.bashrc
```

>**NOTE:Accept (a) or say yes (y) to any prompts from below**

```
#run command to check install
conda --version
#check for updates
conda update conda
```

## More setup and install
>**Accept or say yes to any prompts from below**

1. Create a Conda environment:
   ```
   conda create -n genomics_env
	```
	
2. Activate the environment:
   ```
   conda activate genomics_env
   ```
   
3. Add necessary channels:
```
   conda config --env --add channels bioconda
   conda config --env --add channels conda-forge
```

5. Install packages in the environment:
```
	conda install -c bioconda fastqc
	conda install -c bioconda flye
	conda install -c bioconda quast
```
### NOTE:
>We may use the software below later but do not install in the same conda environment. Remove `#` to use. 
```
#conda install -c bioconda checkm-genome
#conda install -c bioconda fastqc flye quast
#conda install -c bioconda porechop
```

Now you have a Conda environment named `genomics_env` with all the necessary software installed. Remember to activate this environment every time you work on genomic analysis.


To test that these software tools are installed correctly, you can perform the following checks:

For each software, run the version command or help command:
 - This display a message confirming the software version if it's correctly installed.
   For example:
```
fastqc --version
flye --version
quast --version
```

3. Check Conda's installed package list:
  	```
  	conda list
   ```

If all commands return the expected output without errors, it's a good indication that the software tools have been installed correctly in your Conda environment.

## NOTE: You can create a conda environment on your own laptop (Windows, Mac or Linux)

- For this, you will need to find the correct package here: https://www.anaconda.com/download/success
- Follow the guidance for your operating system here: https://docs.conda.io/projects/conda/en/stable/user-guide/install/index.html
- For this workshop, I suggest using the WSL method and then for the assessments, you can choose to use WSL, your laptop, or the HPC
- NOTE: On M‑series Macs, many Bioconda packages are still only built for osx-64, so you must either (a) force an osx-64 environment that runs under Rosetta emulation - this is found with the quast package where you might get an error like `nothing provides boost >=1.66.0,<1.66.1.0a0 needed by quast-4.6.3-py27pl526h3727419_2`. The most common workaround on M‑series Macs when a Bioconda package is missing osx-arm64 builds, is to create an intel env. Run the following to conda install quast:

1. Create an Intel environment (note CONDA_SUBDIR)
```
CONDA_SUBDIR=osx-64 conda create -n quast_env -c conda-forge -c bioconda quast
```

2. Activate it
```
conda activate quast_env
```

3. Persist the subdir inside that environment
```
conda env config vars set CONDA_SUBDIR=osx-64
```
