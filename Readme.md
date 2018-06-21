# Beyond corp at home #


## Tools ##

* Keycloak 4.0.0 Beta 3
* Apache 2
* mode-auth-openidc (evtl. in universe package from Ubuntu)
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

1. Unpack Keycloack
2. Edit standalone.xml in /standalone/configuration/

add within

<security-realmsY add

<security-realm name="ssl-realm">
     <server-identities>
         <ssl>
             <keystore path="application.keystore" relative-to="jboss.server.config.dir" keystore-password="<yourpassword>" alias="dev.mschmall.de (let's encrypt authority x3)" key-password="<yourpassword>" />
         </ssl>
     </server-identities>
     <authentication>
         <local default-user="$local" allowed-users="*" skip-group-loading="true"/>
         <properties path="application-users.properties" relative-to="jboss.server.config.dir"/>
     </authentication>
             <authentication>
                     <truststore path="server.truststore" relative-to="jboss.server.config.dir" keystore-password="flk48e7" />
             </authentication>

 </security-realm>


Additional search for the HTTPS listener and add




#generate-self-signed-certificate-host="localhost"/>

(adapt for your passwords etc.)

copy /home/flake/keycloak-4.0.0.Beta3/standalone/configuration/application.keystore

Create admin user for keycloak locally

./add-user-keycloak.sh -u admin


3. start keycloak by calling ./standalone.sh -b <IP to bind to>

Start configuring Flows / Grants
(taken from https://www.keycloak.org/docs/3.3/server_admin/topics/authentication/x509.html)

4.

sudo add-apt-repository "deb http://archive.ubuntu.com/ubuntu $(lsb_release -sc) main universe restricted multiverse"
apt-get install libapache2-mod-auth-openidc








<VirtualHost *:80>
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html

	ErrorLog /proc/self/fd/1
	CustomLog /proc/self/fd/2 combined

	OIDCProviderMetadataURL http://keycloak:8080/auth/realms/Testrealm/.well-known/openid-configuration
	#OIDCRedirectURI http://openidc/oauth2callback
	OIDCRedirectURI http://openidc/protected/redirect_uri
	OIDCCryptoPassphrase 0123456789
	OIDCClientID testclient
	OIDCClientSecret 12816dc7-cf40-4abc-8df7-581e56930cf5

	OIDCSessionType server-cache:persistent

	OIDCRemoteUserClaim email
	OIDCScope "openid email"
	OIDCPassClaimsAs environment

	Header setifempty Cache-Control "max-age=0, must-revalidate"

	RedirectTemp /logout http://openidc/protected/redirect_uri?logout=http%3A%2F%2Fopenidc%2F%3Fwe-have-no-loggedout-page-yet

	<Location /protected>
		AuthType openid-connect
		Require valid-user
	</Location>

</VirtualHost>



OIDCProviderMetadataURL https://keycloak.example.net/auth/realms/master/.well-known/openid-configuration
OIDCRedirectURI https://www.example.net/oauth2callback
OIDCCryptoPassphrase random1234
OIDCClientID <your-client-id-registered-in-keycloak>
OIDCClientSecret <your-client-secret-registered-in-keycloak>
OIDCRemoteUserClaim email
OIDCScope "openid email"

<Location /example/>
   AuthType openid-connect
   Require valid-user
</Location>



OpenID Connect Provider error: Remote user could not be set: contact the website administrator
