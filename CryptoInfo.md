
```
TLS/SSL ==> CRYPTOGRAPHY

KEY                       CIPHER      CRYPTOGRAPHIC HASH
---                   -------------    --------
RSA                      RC4           HMAC-MD5
Diffie-Hellman           3DES          HMAC_SHA_1
DSA                      AES           HMAC_SHA_256
-----------------------------------------------

The Most popular options are:
Key:    RSA 2048 or 4096         (Asymmetric Encryption only)
Cipher: 3DES or AES              (Symmetric Encryption only)
Hash:   HMAC-Md5, SHA1, SHA256   (Hash, also called digest, or digital finger print)
-----------------------------------------------

MD5: 32 nibbles long     => 128 bits long
SHA1: 40 nibbles long    => 160 bits long
SHA256: 64 nibbles long  => 256 bits long

Example on mac:
$echo "Neeraj" | shasum -a 256
f7a423eb45427f11d70b27add72329f36adf2a8f8da0d158eda045594bb4022f

Flow:

(Asymmetric)
Client --------------------->                  hello          ----------------------> Server
Client <--------------------- Asymmetric Key (public Key)     <---------------------- Server

Client ------->    Encrypted Clients Private Key using Server's Public Key  --------> Server
(Symmetric onwards)
Client <-- Encrypted Data using Client Private Key                               <--- Server
Client ------->    Encrypted data using client's private Key                --------> Server
Client <-- Encrypted Data using Client Private Key                               <--- Server


See the example below:

Certificate contains:
{
   Serial Number:
   Issuer: CA authority
   Valid: from-to
   Public Key:
   Subject:
     Site:
     Company:
     Address:
}

openssl req \
  -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key \
  -x509 -days 365 -out certs/domain.crt

common name should be: myregistry.com

1: Generate Certificate Authority Key (ca-key.pem)
   Either using a random number or use a passphrase:
   #openssl genrsa -aes256 -out ca-key.pem -passwout: pass:Neeraj 2048

623  echo 01 > ca.srl
624  cat ca.srl 
625  openssl genrsa -des3 -out ca-key.pem 2048
627  cat ca-key.pem 

2: Generate CA Cerificate using ca-key.pem

628  openssl  req -new -x509 -days 365 -key ca-key.pem -out ca.pem
630  cat ca.pem 

3: Generate Server certifficate Key (server-key.pem)
   Either using a random number or use a passphrase:
   #openssl genrsa -aes256 -out server-key.pem -passwout: pass:Calypso 2048

631  openssl genrsa -des3 -out server-key.pem 2048

4: Generate Cerificate Signing Request (csr) for server using server-key.pem

632  openssl req -subj '/CN=NEERSHAR-M-K0E1' -new -key server-key.pem -out server.csr
634  cat server.csr 

635  openssl rsa -in server-key.pem -out server-key.pem 
638  cat server-key.pem 

6: Generate Server Cerificate using server.csr and CA certificate and CA key.

639  openssl x509 -req -days 365 -in server.csr -CA ca.pem -CAkey ca-key.pem -out server-cert.pem
641  cat server-cert.pem 

7: Generate Client certifficate Key (client-key.pem)
   Either using a random number or use a passphrase:
   #openssl genrsa -aes256 -out client-key.pem -passwout: pass:test-client 2048

643  openssl genrsa -des3 -out client-key.pem 2048
644  cat client-key.pem 

8: Generate Cerificate Signing Request (csr) for client using client-key.pem

645  openssl req -subj '/CN=client' -new -key client-key.pem -out client.csr
647  cat client.csr 

648  openssl rsa -in client-key.pem -out client-key.pem 
651  cat client-key.pem 

10: Create client cnf file.

652  echo extendedKeyUsage = clientAuth > extfile.cnf

11: Generate Client Cerificate using client.csr and CA certificate and CA key and client's cnf file..

653  openssl x509 -req -days 365 -in client.csr -CA ca.pem -CAkey ca-key.pem -out client-cert.pem -extfile extfile.cnf 
655  cat client-cert.pem 

Using these Keys and Certificate, we should be able to encrypt and decrypt the data between client and server.
```
