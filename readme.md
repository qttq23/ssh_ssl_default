
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
cat /etc/ssh/sshd_config
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


# 2. ssl

## overview
SSL (deprecated) is an old version of TLS.  
HTTPS uses TLS to secure the connection between client and server.  
TLS is based on SSH/Public Key Crypto. (as described above)  
- in handshake phase, the client requests certificate from server and agree on the symmatric key.
- once the handshake is successful, later messages are encrypted.
- the message sent to server can only decrypted by server.
- message sent to client can only decrypted by client.

A server can create self-signed certificate. client and server can use that certificate to secure connection and messages.   
but client has no way to guarantee that the certificate is from the right server or from the man-in-middle.  

CA (Certificate Authorities) presents to solve the problem of man-in-middle attack.  
CA issues a certificate (which includes a public key and private key) for a domain.  
Whenever the client want to ensure that the public key is from the right domain, they get the public key from server and check it with the CA.  
if CA validates that domain matches public key, the certificate (public key) is safe and client are communicating to the right server.  
if CA rejects, the certificate (public key) are not safe and client might be communicating to tampered server.  

Usually, only the server need to be authenticated by CA.  
most of the time, client are not required by server to have authenticated SSL cert.  

(
https://en.wikipedia.org/wiki/Transport_Layer_Security    
https://en.wikipedia.org/wiki/Public_key_certificate
)


## types of certificates
DV, OV, EV  
-> DV is enough for small business

(Validation levels in: https://en.wikipedia.org/wiki/Public_key_certificate)


## common CA, ssl providers
### free certificate (recommended)
let's encrypt:  
(https://letsencrypt.org/getting-started/#without-shell-access)

### cheap certificate (recommended)
ssls: (cheap + 30day trial)  
(hhttps://www.ssls.com/)

namecheap:  
(https://www.namecheap.com/security/ssl-certificates/)

### expensive certificate
digicert  
(https://www.digicert.com/tls-ssl/basic-tls-ssl-certificates)


## set up SSL
### manually on a computer

this demonstrates setup steps for virtual machines such as Google Compute Engine.  
1. first, get the cert.
(for demo, you should use the free DV certficate from Let's Encrypt:  
- install Let's Encrypt on local machine (eg: Windows)
follow to step 3 at below link:  
https://certbot.eff.org/instructions?ws=other&os=windows

- generate ssl cert:  
certbot certonly --manual --preferred-challenges http

it will prompt to ask you for some info such email, domain.  
Then you have to set up the file to your server (if you use --preferred-challenges http).  
or set up a DNS record (if you use --preferred-challenges dns).  
the purpose is to prove your control over the domain.  
  
once done, the cert will be generated at the directory shown in the command prompt.  
Now you can upload this cert to virtual machine and use it to set up HTTPS.  

https://eff-certbot.readthedocs.io/en/stable/using.html#manual
)

2. for nodejs express server:
https://stackoverflow.com/a/70443344/16550663


### on Google Cloud Run
ssl for cloud run:   
- add custom domain in cloud run panel (must have a domain first)
- add CNAME record in owned domain panel.
- wait about 15mins to 1hour for google to provide free ssl cert for new custom domain.

https://cloud.google.com/run/docs/mapping-custom-domains#run

### on Firebase Hosting
?

