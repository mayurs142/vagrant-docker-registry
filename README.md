# Vagrant machine for Private Docker Registry
>Everything required to create a vagrant machine running your own private docker registry!
>It is running registry and nginx docker containers inside vagrant machine. 

# Requirements
  - [VirtualBox][1]
  - [Vagrant][2] (1.9.5+)
  - [Docker][5]

# Usage 
```sh
$ git clone git@github.com:mayurs142/vagrant-docker-registry.git
$ cd vagrant-docker-registry
$ vagrant up
```

# Steps to sign your own certificate
There are a couple of certificates generated and kept in *[registry/files][3]* and *[registry/nginx][4]* directories.
Below are the steps to generate them:
```sh
# Generate a new root key
$ openssl genrsa -out rootCA.key 2048

# Generate a root certificate (enter anything at the prompts)
$ openssl req -x509 -new -nodes -key rootCA.key -days 10000 -out rootCA.crt

# Generate a key for your docker-registry server
$ openssl genrsa -out docker-registry.key 2048

# Make a certificate signing request 
# for "Common Name" make sure to type in the domain your server, in this case its docker-registry.local
# do not enter a challenge password
$ openssl req -new -key docker-registry.key -out docker-registry.local.csr

# Sign the certficate
$ openssl x509 -req -in docker-registry.local.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out docker-registry.crt -days 10000
```
    
# Verify
Add *rootCA.crt* into trusted certificate authority of any client machine that connects to this Docker registry!
Steps are as follows:
1. Copy certificates into client's machine
```sh
# For CentOS clients
$ sudo cp rootCA.crt /etc/pki/ca-trust/source/anchors/
$ sudo update-ca-trust extract

# For Ubuntu clients
$ sudo cp rootCA.crt /usr/local/share/ca-certificates/
$ sudo update-ca-certificates
```
2. Make hosts entry into client's */etc/hosts* file
```sh
# Run with root user
$ echo "10.1.1.30 docker-registry.local" >> /etc/hosts
```
3. Login to registry
Default credentials are admin/admin in [registry.passwd][6] file
```sh
$ docker login -u admin -p admin https://docker-registry.local
```

[1]: https://www.virtualbox.org/wiki/Downloads
[2]: http://www.vagrantup.com/downloads.html
[3]: https://github.com/mayurs142/vagrant-docker-registry/tree/master/registry/files
[4]: https://github.com/mayurs142/vagrant-docker-registry/tree/master/registry/nginx
[5]: https://docs.docker.com/install/
[6]: https://github.com/mayurs142/vagrant-docker-registry/blob/master/registry/nginx/registry.passwd
