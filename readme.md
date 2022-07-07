
# 1. ssh

## symmetric crypto
both ends know and use one shared private-key to encrypt message.  
This means the private-key must be pre-shared to each other.  
This is also the drawback of this method.   
because sharing private key through insecure network is dangerous.  

(https://en.wikipedia.org/wiki/Symmetric-key_algorithm)

## asymmetric crypto
both ends has each pairs of public and private keys.  
public key is safe to share across insecure networks.  
private key is supposed to be kept confidentially by the owner.  

when endA wants to send message to endB, endA encrypts the message with endB's public key.  
endB receives encrypted message and only endB can decrypt the message using endB's private key.  

two main applications:  
- encrypt message: as described above.
- verify message ownership: endA encrypt message using endA's private key. endB receives and uses the endA's public key to verify the message is originated from endA.

drawbacks:  
- man-in-the-middle attack: someone will pretend the endB by sending endA the public key. endA thinks that public key is from endB and trust it and send message to attacker.
-> An authorized orginization to certify the endB public key. before endA uses the public key, endA checks with the Authorized Orginization whether the public is belongs to endB. SSL uses this.

- public key is consideribly large and slow when traveling through network.
-> endB encrypts the symmatric key using endA's public key. So that the symmatric can only be decrypted by endA. This is safe even in insecure network. then both ends just use the symmetric key. SSL and SSH uses this.

(https://en.wikipedia.org/wiki/Public-key_cryptography)

## setup ssh server
on linux/ubuntu:  

- install ssh server:  
```
sudo apt update  
sudo apt install openssh-server
```
 
- verify ssh server is running:  
```
sudo systemctl status ssh
```

- optionally, open port for ssh:  
```sudo ufw allow ssh```

- view the ssh server's fingerprint:  
```
cd /etc/ssh/sshd_config
ssh-keygen -l -f <HostKeyFileName>
```

(
HostKeyFileName can be 'ssh_host_rsa_key' or 'ssh_host_ed25519_key' or
you can open file 'sshd_config' to view the key location.
)

that's it.

( references:  
https://linuxize.com/post/how-to-enable-ssh-on-ubuntu-20-04/  
https://docs.bmc.com/docs/display/itda27/About+the+SSH+host+key+fingerprint
)

## setup ssh client
on windows:  
- install PuTTY for command-line execution:  
https://www.putty.org/

- optionally, install WinSCP for copying files:  
https://winscp.net/eng/download.php

- PuTTY's getting started:  
https://the.earth.li/~sgtatham/putty/0.77/htmldoc/Chapter2.html#gs

- after connecting to a ssh server, you will be asked for username and password.

## ssh password authentication
as desribed in `setup ssh client`.

## ssh client's public key store on server??

# 2. ssl ??

