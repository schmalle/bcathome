# Beyond corp at home #


## Tools ##

* Keycloak 4.0.0 Beta 3
* Apache 2
* mode-auth-openidc
* nginx
* letsencrypt


## Preparation

1. Generate a certificate for your domain using letsencrypt
2. Convert it to pkcs12 to import it in your java key store

```
openssl pkcs12 -export -in /etc/letsencrypt/live/yourdomain.com/fullchain.pem -inkey /etc/letsencrypt/live/yourdomain.com/privkey.pem -out /etc/letsenscrypt/live/yourdomain.com/pkcs.p12 -name mytlskeyalias -passout pass:mykeypassword
```

Key store should now like this this

<add keystore pic here>

3. Generate own ca and client certificate

Create a Certificate Authority root (which represents this server)
Organization & Common Name: Some human identifier for this server CA.

```
openssl genrsa -des3 -out ca.key 4096
openssl req -new -x509 -days 365 -key ca.key -out ca.crt
```

Create the Client Key and CSR
Organization & Common Name = Person name

```
openssl genrsa -des3 -out client.key 4096
openssl req -new -key client.key -out client.csr
openssl x509 -req -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out client.crt
```

Convert Client Key to PKCS
So that it may be installed in most browsers.

```
openssl pkcs12 -export -clcerts -in client.crt -inkey client.key -out
client.p12
```

Convert Client Key to (combined) PEM

Combines client.crt and client.key into a single PEM file for programs using openssl.

```
openssl pkcs12 -in client.p12 -out client.pem -clcerts
```




## Installation ##

1. Install Keyclock and create user admin / verysecure




****
vagrant box add ubuntu/bionic64
vagrant init ubuntu/bionic64
vagrant up
vagrant ssh
vagrant update
vagrant upgrade
wget http://download.oracle.com/otn-pub/java/jdk/10.0.1+10/fb4372174a714e6b8c52526dc134031e/jdk-10.0.1_linux-x64_bin.tar.gz
mv jdk-10.0.1 /opt/

cd /vagrant

update-alternatives --install /usr/bin/javac javac /opt/jdk-10.0.1/bin/javac 1
update-alternatives --install /usr/bin/java java /opt/jdk-10.0.1/bin/java 1

apt-get mysql-server
apt install unzip

wget https://downloads.jboss.org/keycloak/4.0.0.Beta3/keycloak-4.0.0.Beta3.zip
unzip keycloak-4.0.0.Beta3.zip
cd keycloak-4.0.0.Beta3/bin/
sudo add-apt-repository "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc) main universe restricted multiverse"
apt-get install libapache2-mod-auth-openidc


 Keycloack 4.0.0.Beta 3


****




openssl genrsa -des3 -out ca.key 4096
openssl req -new -x509 -days 365 -key ca.key -out ca.crt

openssl genrsa -des3 -out client.key 4096
openssl req -new -key client.key -out client.csr

openssl x509 -req -days 365 -in client.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out client.crt
