# This note is to describe how to connect to github via ssh key

## Step 1: Generate SSH Key
``` 
ssh-keygen -t ed25519 -C "your_email@example.com"
```

## Step 2: Add private key to SSH agent
```
ssh-add ~/.ssh/id_ed25519
```

## Step 3: import SSH public to github
