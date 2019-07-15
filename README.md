## Getting Started:

> **WARNING**: You must be enrolled in the Apple Developer Program to generate passes. You can't do the following with a *free* account.

1. Obtain Apple's WWDR certificate from [here](http://developer.apple.com/certificationauthority/AppleWWDRCA.cer). Open the file in keychain and export it as a .pem file (filename: ```wwdr.pem```)

2. Create a Pass Type ID in the Apple Developer Portal. It will ask you for a CSR file. Generate one with keychain, then upload that and download the ```pass.cer``` file. Open the file in keychain and export it as a .p12 file (filename: ```Certificates.p12```)

3. Run the following commands to generate a client certifcate and a private key. First passphrase prompt is the one you chose in Keychain. The following passphrase prompts are for the ```key.pem``` file itself. This is what you'll use in the application. 

```sh
openssl pkcs12 -in "Certificates.p12" -clcerts -nokeys -out certificate.pem
openssl pkcs12 -in "Certificates.p12" -nocerts -out key.pem
```
You should now have 3 files ready for the server:

```
certificate.pem
key.pem
wwdr.pem
```
You can delete the ```Certificates.p12``` file.

## Docker Compose Example:
```
version: '3'
services:
  applewallet:
    image: shauder/apple-wallet-shortcut:latest
    container_name: applewallet
    restart: unless-stopped
    networks:
      - default
    port:
      - "5002:5002"
    environment:
      - PASS_TYPE_IDENT=<pass.tld.yourdomain>
      - TEAM_IDENT=<team ID>
      - PASS_PASSWORD=<private key passphrase>
      - RETURN_ADDRESS=http://<yourdomain.tld>:5002
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - <certfiles directory>:/app/crts
```