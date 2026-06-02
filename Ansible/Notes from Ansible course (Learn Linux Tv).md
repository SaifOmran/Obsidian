- We add the machine’s public SSH key to GitHub so GitHub can verify that the machine making the `push` or `pull` owns the corresponding private key.  
  
- We should clone the repository using SSH instead of HTTPS to avoid entering username/password or token credentials repeatedly.  
  
- `eval` executes text output as a shell command.  
  
Example:  
  
```bash  
eval $(ssh-agent)  
```  
  
This starts the SSH agent and configures the current shell environment to communicate with it.  
  
The SSH agent stores SSH authentication data such as:  
- private keys  
- decrypted passphrases in memory  
  
so you do not need to enter the passphrase every time.  
  
- To add your private key to the SSH agent:  
  
```bash  
ssh-add  
```  
  
or explicitly:  
  
```bash  
ssh-add ~/.ssh/id_ed25519  
```  
  
Then:  
- enter the passphrase once (if the key has one)  
- the agent keeps the key loaded for future SSH/Git operations.

- You can make an alias and put it in `.bashrc` for easier management
```bash
alias ssha='eval $(ssh-agent) && ssh-add'
```

