# Install jotta cloud

- Install jotta from AUR package. [Check if aur package is in sync on jotta-cli release-notes](https://docs.jottacloud.com/en/articles/1461561-release-notes-for-jottacloud-cli) 
- Run `run_jottad` which enabled user based systemd service
- Login with key generated on jotta web page. [jottacloud: Login help](https://docs.jottacloud.com/en/articles/1437248-login-and-basic-use-with-jottacloud-cli) 
- Setup sync - [jottacloud: Sync-help](https://docs.jottacloud.com/en/articles/5859533-using-the-sync-folder-with-jottacloud-cli)
- Start sync
- Do picture download

```
git clone https://aur.archlinux.org/jotta-cli.git
cd jotta-cli
makepkg -si
run_jottad
jotta-cli login
jotta-cli sync setup --root <jotta root folder>
jotta-cli sync start

jotta-cli ls
jotta-cli download Photos/Timeline/<folder> /home/<user>/Pictures/jotta_mobile_images --merge
```

# Problems with sync

Sometime the login fails if the network is not up yet. If the service does not start after that it is best to restart sync with stop start, a trigger won't work. 


```
jotta-cli sync stop
jotta-cli sync start
```

In the long run it would probably be better to add a dependency in the jottad.service startup on the network. 

Btw the jottad.service is registered on the user, so to see status etc. --user must be added to the systemctl command.

```
systemctl --user status jottad.service
```

