---
layout: post
title: How to connect VS Code to Google Colab?
---

Google Colab offers free CPU, GPU and TPU while VS Code offers a better IDE. So, let's connect them for a better experience.  

There are two methods to do it, with and without SSH connection. The former one is easier but offers VS code only in the browser, while the latter method connects local VS code to Google Colab.

The latter methods also offer the advantage of allowing Github copilot usage.

For the time being, we will start with the first method because it is much easier and faster to do.

# Method 1: Using ColabCode (Without SSH)
Open up your Google Colab and simply run this code.
```
# Mount Google Drive if you want to.
from google.colab import drive
drive.mount('/content/drive')

!pip install colabcode
from colabcode import ColabCode

ColabCode(port=10000, password=”your_password_here”)
```

Click on the “ngrok” link that is appearing and viola, you are done.
If you are interested in checking out the Github repo, you can do so: [Colab_Code](https://github.com/abhishekkrthakur/colabcode)

# Method 2: Using Colab_SSH

This method is long and will take a few more minutes for the first-time set-up. Though it does work nicely, I am not a fan of it because it involves repeated small steps each time you connect.
Run this code in Colab to start with.
```
# Mount Google Drive if you want to.
from google.colab import drive
drive.mount('/content/drive')

# Install colab_ssh on google colab.
!pip install colab_ssh --upgrade

from colab_ssh import launch_ssh_cloudflared, init_git_cloudflared
launch_ssh_cloudflared(password="your_password_here")

# Prevent Colab run-time to end.
import time
while True:
  time.sleep(300)
```

Once you do so, you will get all the steps for the first-time setup. I am reproducing them here.

1. Download Cloudflared (Argo Tunnel) as suitable to your system and store it where ever you like. Also, copy the path to it.
2. Once done, open up the “SSH config” file in a text editor like Notepad.
For windows, you can find it under: “C:\Users\Your_User_Name\.ssh\config”. In case the file is not present, jump to the 4th step.
3. Replace “<PUT_THE_ABSOLUTE_CLOUDFLARE_PATH_HERE>” with the path you copied in step 1 and save this code in the config file.
```
Host *.trycloudflare.com
 HostName %h
 User root
 Port 22
 ProxyCommand <PUT_THE_ABSOLUTE_CLOUDFLARE_PATH_HERE> access ssh — hostname %h
```
4. Open up the local VS Code, and install the “Remote SSH” extension from the marketplace (Press Ctrl + Shift + k, to open the marketplace).
5. Press Ctrl + Shift + P, and type “>Remote-SSH: Connect to Host”. Click on it, to “Add New SSH Host”.
6. Copy-Paste the VS Code Remote SSH hostname from Google Colab and hit enter. The SSH config file will get created if it was not already present.
7. Follow steps 2 and 3, if you were not able to do so earlier. If you are done, just select the SSH config file from the options you get after doing step 6.
8. Click on Connect
9. Select the “Linux” platform.
10. Enter your_password_here

Done. Just install whatever extensions you want to.

The Github Repo for colab_ssh is available here: [Colab_SSH](https://github.com/WassimBenzarti/colab-ssh)

## Few Additional Points for Method 2

1. To connect Colab and VS code next time, just run the code in Colab and follow steps 5 to step 10 discussed above.
2. You will need to install the Github Copilot extension each time you connect. You can do this either by VS Code UI or by running this command in the VS Code terminal.
```code --install-extension github.copilot```
3. To install multiple extensions (for example: Python, Jupyter and GitHub copilot) through singe command, run
```code --install-extension github.copilot --install-extension ms-toolsai.jupyter --install-extension ms-python.python```
4. You can also list all extensions in a single file and run a command to install them. To do so:

Create extensions.txt file listing all your extensions:
```
GitHub.copilot
ms-python.python
ms-toolsai.jupyter
```

Create installext.sh
```
#!/usr/bin/env bash
cat extensions.txt | while read extension || [[ -n $extension ]];
do
  code --install-extension $extension --force
done
```
Allow the permissions, run this code either in VS code terminal or in Colab:
```
# Allow permission
!chmod 755 installext.sh
```
Install extensions by running this in VS Code terminal (While connected to SSH):
```
./installext.sh
```
All done!
Feel free to create an issue if you are facing any problems in implementation.
