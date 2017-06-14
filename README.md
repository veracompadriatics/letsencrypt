# Notes on how to issue a Let's Encrypt trusted certificate keypair on Ubuntu Linux
# install certbot client (Let's encrypt client)
sudo apt-get install python-certbot-apache

# the Let's Encrpyt certbot must prove domain ownership by several methods as described here: http://letsencrypt.readthedocs.io/en/latest/challenges.html
# here, the HTTP challenge will be used: the server running the certbot command below must be publicly available on port 80
# also, DNS hostname for which the cert is issued must resolve to the host where the command is run
# as example, to temporary allow port 80 on 10.0.0.1 via iptables:
sudo iptables -t nat -A PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.0.0.1:80

# run certbot, using port 80 http challenge
certbot certonly --standalone --preferred-challenges http

# Public key and private key material generated in folder:
/etc/letsencrypt/live/\<hostname>/

# Public key file:
/etc/letsencrypt/live/<hostname>/fullchain.pem

# Private key file:
/etc/letsencrypt/live/<hostname>/privkey.pem

# optionally, encrypt private key with password (sometimes required when importing into 3rd party solutions):
openssl rsa -des3 -in /etc/letsencrypt/live/<hostname>/privkey.pem -out privkey.key

# or create a keypair.pfx pass protected archive, also sometimes required by applications:
openssl pkcs12 -export -out keypair.pfx -inkey /etc/letsencrypt/live/<hostname>/privkey.pem -in /etc/letsencrypt/live/<hostname>/fullchain.pem

# the final result is the private key (privkey.pem) and public material (fullchain.pem), ready to be imported into a web server or application which will present this certificate identity.

# to disable port 80 access enabled previously:
sudo iptables -t nat -D PREROUTING -p tcp --dport 80 -j DNAT --to-destination 10.0.0.1:80
