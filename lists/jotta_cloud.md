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

