# vagrant-docker-registry

Vagrant box for Private Docker Registry on CentOS

## Steps to sign your own certificate

# Generate a new root key
openssl genrsa -out rootCA.key 2048

# Generate a root certificate (enter anything at the prompts)
openssl req -x509 -new -nodes -key rootCA.key -days 10000 -out rootCA.crt

# Generate a key for your docker-registry server
openssl genrsa -out docker-registry.key 2048

# Make a certificate signing request 
# for "Common Name" make sure to type in the domain your server, in this case its docker-registry.local
# do not enter a challenge password
openssl req -new -key docker-registry.key -out docker-registry.local.csr

# Sign the certficate
openssl x509 -req -in docker-registry.local.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out docker-registry.crt -days 10000

# Add rootCA.crt into client's (hosts that will be accessing to docker-registry server) verified certificate authority
# For CentOS clients
sudo cp rootCA.crt /etc/pki/ca-trust/source/anchors/
sudo update-ca-trust extract
# For Ubuntu clients
sudo cp rootCA.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates

# Restart docker service to pick the changes to our certificate store
sudo service docker restart