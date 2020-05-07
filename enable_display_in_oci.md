## How to Export GUI Display for OCI Compute

The easist way is to use MobaTerm (https://mobaxterm.mobatek.net/download.html). You can, however, use other ways such as VNC too. This guide will explain how to export display with MobaTerm. The rest of the guide assume you have MobaTerm installed locally (Home edition is sufficient)  


Run the following from SSH:  

- sudu su -
- cd /etc/ssh/
- vi sshd_config
- Search for the line that has X11UseLocalhost yes (it’s commented out).
- remove the comment from the beginning of the line and change the setting to no.
- Search for the line that has X11DisplayOffset (it’s commented out).
- remove the comment from the beginning of the line.
- save the file  

![Screenshot 7](images/weblogic_install/screenshot7.jpg)  

- systemctl stop sshd.service
- systemctl start sshd.service
- systemctl enable sshd.service
- Install xauth & xterm: yum -y install xauth xterm
- Install Xtst: yum -y install libXtst.x86_64
- Exit root environment by running: exit
- Restart SSH session

- To verify the GUI export is working, you can install xclock and run xclock to test from SSH console
