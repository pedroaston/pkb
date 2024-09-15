
### Creating a git repository from an existing folder

1. Create an empty repo in GitHub
2. Run the following command sequence, editing with the relevant parameters
```
cd <repo-folder>
git init
git add .
git commit -m "intial commit"
git remote add origin <repo-url>
git push -u origin main
```

### Setup git in a new Linux server

1. Generate SSH Key in the server
2. Create the authentication and signing key with the SSH public key generated in the previous step
3. Configure git config file
4. If SSH key had a custom name, please perform the action mentioned in https://stackoverflow.com/questions/72531064/github-ssh-key-names

NOTE: Please add more detail

## Go get SSH requirements

Currently it seems that go get requires the ssh daemon to be running and the ssh key identity to be added. When I reboot the machine the process seems to not come back up. The following command sequence is required:

```
eval `ssh-agent -s`
ssh-add ~/.ssh/id_ed25519
```

## Github self-hosted runners

Must not forget to boot the runner when I reboot the machine with the "./run.sh & disown" command.