+++
title       = "What to do when the Juniper Network Connect app on OS X does not connect"
slug        = "Ewwww"
date        = "2016-02-04T22:50:09+00:00"
categories  = ["macOS"]
tags        = ["VPN", "Juniper", "tools"]
+++
If you use Juniper's Network Connect app to connect to your office VPN over HTTPS, you might occasionally experience strange errors like "A software error caused the Network Connect tunneling service to terminate." Shortly after that, the application may either crash or prompt you for login credentials again.

The solution involves removing all of the Network Connect files and allowing your browser to download and install the app all over again, as it did the first time you tried to connect to your office VPN.

(Note: as a precaution you may want to simply move the Network Connect.app file from /Applications to ~/Desktop, just in case your browser is not able to download it as needed.)

To remove all files associated with the Network Connect application, execute the following commands in Terminal.app.

```bash
# sudo rm -rf /Applications/Network\ Connect.app`
# sudo rm -rf /usr/local/juniper`
# sudo rm -rf ~/Library/Application\ Support/Juniper\ Networks`
# sudo rm ~/Library/Preferences/net.juniper.NetworkConnect.plist`
```

Now try connecting to your office VPN again and the browser should prompt you to redownload the application. When I tried this I got an error the first time on Firefox saying the installation failed, but I was able to manually verify that the files were in place. My second attempt succeeded. Go figure.