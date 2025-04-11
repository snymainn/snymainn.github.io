# Install jotta cloud

- Install jotta from AUR package
- Run `run_jottad` which enabled user based systemd service
- Login with key generated on jotta web page 
- Setup sync
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

