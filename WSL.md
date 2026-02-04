## Installing Windows Subsystem for Linux (WSL) or HPC usage

There are a few options on systems to use to complete the workshop (option 1 is probably the best!):

### Option 1 - Ubuntu via wsl.exe and powershell
You will need to do a few steps (~3 mins):

1. Open "Install University Applications” in the Windows start menu.
1. Search for "wsl" and install "Enable Windows Subsystem For Linux (WSL)". 
1. Allow the reboot

1. Open "Powershell".
1. Run `wsl.exe --install Ubuntu`
   
1. Once installation is complete (~2 mins), it will ask to confirm your username (automatically puts it as your MWS name) and then enter your MWS password (this can't be empty!).
1. Ubuntu will then load within powershell.

**NOTE:**
>If you need to reload on the same computer/session, open powershell and type `wsl` and it'll load Ubuntu back in, then `cd ~` to change to the home directory - you shouldn't need to re-enter your password.

---

### Option 2 - using the HPC
You will need to do a few steps (~3 mins) and will need your HPC/cluster account for the session - please ensure to keep your password safe and use your MWS username for login.

1. Login to the HPC 
    - Load "git bash" or a command line prompt ("powershell" in windows or "terminal" on mac) on your system
    - ssh <username>@login.pgb.liv.ac.uk
    - Enter your unique password
    - Accept the fingerprint (on first login)
    - Immediately initiate an interactive session on a compute (worker) node by typing and executing: `srun --nodes=1 --ntasks-per-node=1 --time=03:00:00 --pty bash -i`
    - NOTE: To indicate an active interactive session, the terminal line will change from showing [<username>@login ~] to something like [<username>@compute01 ~]

Now, you will source the shared Conda bin dir (by adding to ~/.bashrc if desired) - the Conda environment will have all packages installed for this workshop 
1. Add to ~/.bashrc (once only) or run each time, by typing and executing:
   - `export PATH="/mnt/hc-storage/groups/LIFE748/2526/software/miniconda3/bin:$PATH"`
   - `conda config --add envs_dirs /mnt/hc-storage/groups/LIFE748/2526/software/miniconda3/envs`
1. Source bashrc by typing and executing:
   - `source ~/.bashrc`
1. Activate and check the version numbers of the installed software packages in the conda environment:
   - `conda activate genomics_env`  # this should load the env containing fastqc, flye, spades, quast, prokka, bakta, artemis
   - `fastqc --version`
   - `flye --version`
   - `quast --version`
   - `spades.py --version`
   - `prokka --version`
   - `bakta --version`
   - `artemis --version` 
1. You should now be ready to start the workshop!

**NOTE:**
>If you need to reload your shell session, then just re-run all the same steps!

---

### Option 3 - University Software Application approach (might be deprecated)
You will need to do a few steps (~5 mins):

1. Open Install “University Software Application” in the Windows start menu.
1. Install Windows Subsystem For linux (Ubuntu). 
1. Allow the reboot
   
1. Start Ubuntu programme from the start menu
1. Use Ubuntu shell

**NOTE:**
>These installs will not follow you around campus machines. Therefore, copy any data from your home to your M drive that you wish to keep.
>Also be prepared to repeat the above procedure everytime you use a "new" computer on campus.
