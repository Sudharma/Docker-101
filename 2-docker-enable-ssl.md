# Enabling TLS with Docker (Mutual TLS)

## Creating certificates.

### Create Server Certificates
1. Create CA private key `openssl genrsa -aes256 -out ca-key.pem 4096`
2. Create CA Cert `openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem` 
   Make sure that “Common Name” matches the hostname you use to connect to Docker:
3. Create Server private key `openssl genrsa -out server-key.pem 4096`
4. Create Server CSR  `openssl req -subj "/CN=$HOST" -sha256 -new -key server-key.pem -out server.csr`
   $HOST can be IP address as well. but DNS is always recommended.
5. `echo subjectAltName = DNS:$HOST,IP:10.10.10.10,IP:127.0.0.1 >> extfile.cnf`
6. `echo extendedKeyUsage = serverAuth >> extfile.cnf`
7. Create Server Cert with CA `openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem \`
   `-CAcreateserial -out server-cert.pem -extfile extfile.cnf`

### Create Client Certificates
1.  Create Client private key `openssl genrsa -out key.pem 4096`
2.  Create Client CSR `openssl req -subj '/CN=client' -new -key key.pem -out client.csr`
3.  `echo extendedKeyUsage = clientAuth > extfile-client.cnf`
4. Create Client Cert with CA `openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem \`
  `-CAcreateserial -out cert.pem -extfile extfile-client.cnf`
5. ` rm -v client.csr server.csr extfile.cnf extfile-client.cnf`
6. Read only private keys to owner `chmod -v 0400 ca-key.pem key.pem server-key.pem`
7. World Readable only for certs `chmod -v 0444 ca.pem server-cert.pem cert.pem`
8. Copy the `ca.pem` , `cert.pem` and `key.pem` to the client machine  `sudo scp  -T username@$HOST:"~/ca.pem ~/cert.pem ~/key.pem" ~/.docker/`
9. To enable TLS on client `export DOCKER_HOST=tcp://$HOST:2376 DOCKER_TLS_VERIFY=1`

## Handling systemd
```` 
systemctl stop docker
sudo systemctl edit docker
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2376 --tlsverify --tlscacert=/etc/docker/certs/ca.pem --tlskey=/etc/docker/certs/server-key.pem --tlscert=/etc/docker/certs/server-cert.pem
````

## Reference
https://docs.docker.com/engine/security/https/
https://success.docker.com/article/using-systemd-to-control-the-docker-daemon