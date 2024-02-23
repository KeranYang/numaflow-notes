### GitHub ssh key

#### Switch to enterprise

1. Generate a new public and private key locally ([reference](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent))

```bash
ssh-keygen -t ed25519 -C "keran_yang@intuit.com"
```

```bash
Enter file in which to save the key (/Users/kyang5/.ssh/id_ed25519)
```

(Press enter, to override the existing key.) no passphrase

2. ## Adding your SSH key to the ssh-agent

```bash
$ eval "$(ssh-agent -s)"
> Agent pid 59566
```

```bash
open ~/.ssh/config
```

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

3. Github CLI Login

```
kyang5@macos-L56MLHQWQ4 ~ % gh auth login                                 
? What account do you want to log into? GitHub Enterprise Server
? GHE hostname: github.intuit.com (or github.dev.intuit.com, depending on your case)
? You're already logged into github.intuit.com. Do you want to re-authenticate? Yes
? What is your preferred protocol for Git operations? SSH
? Upload your SSH public key to your GitHub account? /Users/kyang5/.ssh/id_ed25519.pub
? Title for your SSH key: Keran Public Key
? How would you like to authenticate GitHub CLI? Login with a web browser

! First copy your one-time code: 6961-168D
Press Enter to open github.intuit.com in your browser... 
✓ Authentication complete.
- gh config set -h github.intuit.com git_protocol ssh
✓ Configured git protocol
✓ Uploaded the SSH key to your GitHub account: /Users/kyang5/.ssh/id_ed25519.pub
✓ Logged in as kyang5
```

