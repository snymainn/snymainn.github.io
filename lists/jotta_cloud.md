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

In the long run it would probably be better to add a dependency in the jottad.service startup on the network. A quick fix is to add a delay in the startup of the service:
```
sudo vim /usr/lib/systemd/user/jottad.service
#Add before ExecStart to sleep 10 seconds before start:
ExecStartPre=/bin/sleep 10 
```

Btw the jottad.service is registered on the user, so to see status etc. --user must be added to the systemctl command.

```
systemctl --user status jottad.service
```

# How to sync images that is backed up from mobile 

Jottacloud does not backup the mobile images to a sync folder, but to a backup folder online. That means that it is not automatically synched to a pc where jottacloud is running. 

The workaround for this is the following procedure:
- (Optional) Clean your local and remote Archive sync folder if you want to make sure only the new images are present there
- Log in to jotta web with the following command: ```jotta-cli web```
- Go to Backups > Photo Timeline Uploads
- Mark and move/copy the images you want to sync to the computer to Archive
- When images is moved/copied to Archive, use jotta-cli to syncronize Archive to pc with this command:
```
jotta-cli download Archive <your Archive location on your pc> --merge
```
- I prefer to keep the cloud Timeline and remote and local Archive clean because I only want any new images taken by the phone to be present after the sync procedure. This is because I move the images to Jottacloud sync folder on the pc after I have synched them to my local Archive directoy. 