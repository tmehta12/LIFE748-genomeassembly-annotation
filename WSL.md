## Installing Windows Subsystem for Linux (WSL) 

There are two options (option 1 is probably the best!):

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

### Option 2 - University Software Application approach (might be deprecated)
You will need to do a few steps (~5 mins):

1. Open Install “University Software Application” in the Windows start menu.
1. Install Windows Subsystem For linux (Ubuntu). 
1. Allow the reboot
   
1. Start Ubuntu programme from the start menu
1. Use Ubuntu shell

**NOTE:**
>These installs will not follow you around campus machines. Therefore, copy any data from your home to your M drive that you wish to keep.
>Also be prepared to repeat the above procedure everytime you use a "new" computer on campus.
