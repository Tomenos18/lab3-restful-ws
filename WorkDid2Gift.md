# Work did 2 earn the gift - HTTPS support

This is the steps followed to support https request:

First we run the kotlin server and if we use curl to do a request:

```bash
curl -sI http://127.0.0.1:8080
```

And it return:

```bash
HTTP/1.1 404
Content-Type: text/html;charset=utf-8
Content-Language: en
Transfer-Encoding: chunked
Date: Mon, 08 Nov 2021 14:02:03 GMT
```

It's normal to get 404 error because the server didn't configurate to answer.

Now we gonna use https:

```bash
curl -sI https://127.0.0.1:8080
```

And it return nothing, it's because don't support https request.

To configure it, we need to have an SSL certificate, with the following command line we create one:

```bash
openssl req -x509 -out localhost.crt -keyout localhost.key \
  -newkey rsa:2048 -nodes -sha256 \
  -subj '/CN=localhost' -extensions EXT -config <( \
   printf "[dn]\nCN=localhost\n[req]\ndistinguished_name = dn\n[EXT]\nsubjectAltName=DNS:localhost\nkeyUsage=digitalSignature\nextendedKeyUsage=serverAuth")
```

With that we create two files:

* localhost.crt - The public key
* localhost.key - The private key

Now, we can use openssl to generate a PKCS truststore file that contains both our certificate and key with the following command:

```bash
openssl pkcs12 -export -in localhost.crt -inkey localhost.key -name localhost -out localhost.p12
```

(In this step, openssl ask you to password, we use "cocacola" as password)

With that we create the file localhost.p12 (the certificate), now we can use it in the kotlin project. We have to copy that file to _src/main/resources_ directory and copy the following lines in _application.properties_ of that directory:

```
# Changes the server port used by Spring Boot to the default one used for HTTPS on Tomcat
server.port=8443
# The format used for the keystore. It could be set to JKS in case it is a JKS file
server.ssl.key-store-type=PKCS12
# The path to the keystore containing the certificate
server.ssl.key-store=classpath:localhost.p12
# The password used to generate the certificate
server.ssl.key-store-password=cocacola
# The alias mapped to the certificate
server.ssl.key-alias=localhost
```

It's finished and if we run the server, we can run the curl command and we get:

```bash
curl -k -sI https://127.0.0.1:8443
# -k is 
```

And we get:

```bash
HTTP/1.1 404
Content-Type: text/html;charset=utf-8
Content-Language: en
Transfer-Encoding: chunked
Date: Mon, 08 Nov 2021 14:14:10 GMT
```

Now we have a kotlin server that supports https request.
