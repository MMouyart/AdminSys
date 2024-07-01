# SSH and keys management
## SSH commands
Connect to a remote host
```ssh <username>@<remote host>:<port (optional)>```

Create local ssh tunnel
```ssh -l <local port>:<destination>:<destination port> <next hop>```

Create remote forwarding tunnel
```ssh -R <local port>:localhost<destination port> <next hop>```

Create a dynamic ssh tunnel
```ssh -D <local port> <destination>```
        
Create a X11 forwarding
```ssh -X <destination>```

Create assymetric key pairs
```ssh-keygen -t <algorithm>```

Usable algorithm : dsa, ecdsa, ecdsa-sk, ed25519, ed25519-sk, rsa

Remove key for specific host
```ssh-keygen -R <ip address>```

Check current stored keys
```bash
ls ~/.ssh
ls /etc/ssh
```

Configure ssh assymetric login
```bash
create key pair
share public key to other entity
store public key under ~/.ssha/authorized_keys
make sure in /etc/ssh/sshd_config th parameter PubkeyAuthentication yes is not commented
```
To share public key
```ssh-copy-id <ip address>```

Automate key management
```bash
ssh-agent /bin/bash
ssh-add
```

## Keys management with GPG
Create gpg key
```gpg --gen-key```

List gpg keys
```gpg --list-keys```

Export gpg key
```bash
gpg --export <user> <public key file>
gpg --export -a <user> <public key file>.asc
```

Encrypt file using gpg key
```bash
gpg --out <output file> --recipient <destination user> --encrypt <input file>
gpg --out <output file>.asc -a --recipient <destination user> --encrypt <input file>
```

Decrypt file using gpg
```gpg --out <ouput file> --decrypt <input file>```

Sign file using gpg
```bash
gpg --output <output file> --sign <input file>
gpg --ouput <output file>.asc --sign -a <input file>
```

Verify signed file using gpg
```gpg --verify <input file>```
