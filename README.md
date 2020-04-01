# opensslproject

## Description

#### SSL chain creation and installation // Becoming a Root CA
	
In this project I have created a SSL chain of 4 certificates ( 1 Root CA , 2 Intermediate CA's and 1 server certificate )  

>### Tools Used: openssl , git , visual studio code


#### Final Output

![Screenshot of SSL chain created](screenshot.PNG)

---
## Folder and Files structure followed

>* ### Root CA Folder
>	* Certificate Folder
>		* root_CA certificate
>	* Private Key Folder
>		* root_CA private key
>	* root_CA configuration file.cnf
>* ### Intermediate CA 1 Folder 
>	* Certificate Folder
>		* intermediate_CA1 certificate
>	* Private Key Folder
>		* intermediate_CA1 private key
>	* CSR  Folder
>		* intermediate_CA1 CSR certificate
>	* intermediate_CA1 configuration file.cnf  
>* ### Intermediate CA 2 Folder 
>	* Certificate Folder
>		* intermediate_CA2 certificate
>	* Private Key Folder
>		* intermediate_CA2 private key
>	* CSR  Folder
>		* intermediate_CA2 CSR certificate
>	* intermediate_CA2 configuration file.cnf 
>* ### Server Certificate Folder 
>	* Server Certificate
>	* Server Private Key 
>	* Server CSR Certificate  
  
---


## Installation and configuration guide

1. #### **ROOT CA Certificate generation**

   1. ### Prepare the directory
		```
		# mkdir root
		# cd root
		# mkdir ca
		# cd ca
		# mkdir certs private newcerts
		# touch index.txt
		# echo 1000 > serial 
		```
	2. ### Prepare the configuration file of ROOT CA
		Copy the [root CA configuration file](root_ca.cnf) to `/root/ca/root_ca.cnf.` Configure directory paths , hashing algo , policy_strict/policy loose , intermediaries CA  pathlen , CSR default values , etc.
	3. ### Generating the private key for ROOT
		##### We will create RSA 4096 bits keys for ROOT CA and Intermediaries CA and 2048 bits keys for Server certificates.

		```
		 # openssl genrsa -aes256 -out private/universe.key.pem 4096 
		```
	4. ### Generating the self-signed certificate for ROOT CA	
		##### We will set the validity of ROOT CA certificate for 20 Years , Intermediaries CA certificates for 10 Years and Server certificates for a period of 1 year.
		```
		# openssl req -config root_ca.cnf -key private/universe.key.pem -new -x509 -days 7300 -sha256 -extensions v3_ca -out certs/universe.cert.pem
		```
	5. ### Verifying the generated ROOT CA certificate	
		```
		# openssl x509 -noout -text -in certs/universe.cert.pem
		```	
2. #### **INTERMEDIATE CA 1 Certificate** generation

   1. ### Prepare the directory
		```
		# mkdir ica1
		# cd ica1
		# mkdir certs private csr newcerts
		# touch index.txt
		# echo 1000 > serial 
		```
	2. ### Prepare the configuration file of Intermediate CA 1
		Copy the [Intermediate CA configuration file](intermediate_ca.cnf) to `/root/ca/ica1/intermediate_ca.cnf.` Configure directory paths , hashing algo , policy_strict/policy loose , intermediaries CA  pathlen , CSR default values , etc.
	3. ### Generating the private key for Intermediate CA 1
		##### We will create RSA 4096 bits keys for ROOT CA and Intermediaries CA and 2048 bits keys for Server certificates.

		```
		 # openssl genrsa -aes256 -out private/galaxy.key.pem 4096 
		```
	4. ### Generating the CSR request certificate for Intermediate CA 1

		```
		 # openssl req -config intermediate_ca.cnf -new -sha256 -key private/galaxy.key.pem -out csr/galaxy.csr.pem 
		```
	5. ### Generating the certificate for Intermediate CA 1	
		##### We will set the validity of Intermediaries CA certificates for 10 Years and Server certificates for a period of 1 year.
		```
		# openssl ca -config /root/ca/root_ca.cnf -extensions v3_intermediate_ca -days 3650 -notext -md sha256 -in csr/galaxy.csr.pem -out certs/galaxy.cert.pem
		```
	6. ### Verifying the generated Intermediate CA 1 certificate	
		```
		# openssl x509 -noout -text -in certs/galaxy.cert.pem
		```	
	7. ### Verifying the certificate chain till now : **`ROOT CA-->INTERMEDIATE CA 1`**
		```
		# openssl x509 -noout -text -in certs/galaxy.cert.pem
		```	
3. #### **INTERMEDIATE CA 2 Certificate generation**

   1. ### Prepare the directory
		```
		# mkdir ica2
		# cd ica2
		# mkdir certs private csr newcerts
		# touch index.txt
		# echo 1000 > serial 
		```
	2. ### Prepare the configuration file of Intermediate CA 2
		Copy the [Intermediate CA configuration file](intermediate_ca.cnf) to `/root/ca/ica2/intermediate_ca2.cnf.` Configure directory paths , hashing algo , policy_strict/policy loose , intermediaries CA  pathlen , CSR default values , etc.
	3. ### Generating the private key for Intermediate CA 2
		##### We will create RSA 4096 bits keys for ROOT CA and Intermediaries CA and 2048 bits keys for Server certificates.

		```
		 # openssl genrsa -aes256 -out private/planets.key.pem 4096
		```
	4. ### Generating the CSR request certificate for Intermediate CA 2

		```
		 # openssl req -config intermediate_ca2.cnf -new -sha256 -key private/planets.key.pem -out csr/planets.csr.pem
		```
	5. ### Generating the certificate for Intermediate CA 2	
		##### We will set the validity of Intermediaries CA certificates for 10 Years and Server certificates for a period of 1 year.
		```
		# openssl ca -config /root/ca/ica1/intermediate_ca1.cnf -extensions v3_intermediate_ca -days 3650 -notext -md sha256 -in csr/planets.csr.pem -out certs/planets.cert.pem
		```
	6. ### Verifying the generated Intermediate CA 2 certificate	
		```
		# openssl x509 -noout -text -in certs/planets.cert.pem
		```	
	7. ### Verifying the certificate chain till now : **`ROOT CA-->INTERMEDIATE CA 1-->INTERMEDIATE CA 2`**
		##### Create the `chain.cert.pem` file by concatenating the intermediate CA 1 certificate followed by the root CA certificate.
		```
		# openssl verify -CAfile certs/chain.cert.pem certs/planets.cert.pem
		```	
4. #### **SERVER Certificate generation**

   1. ### Prepare the directory
		```
		# mkdir server
		# cd server
		# mkdir earth
		# cd earth
		```
	2. ### Prepare the Intermediate CA 2 configuration file for signing the server certificate
		Modify the intermediate CA 2 configuration file at `/root/ca/ica2/intermediate_ca2.cnf` . Add the `subjectAltName` and `DNS.0,DNS.1,etc` as per the below code : 
		```
		[ server_cert ]
		# Extensions for server certificates (`man x509v3_config`).
		basicConstraints = CA:FALSE
		nsCertType = server
		nsComment = "OpenSSL Generated Server Certificate"
		subjectKeyIdentifier = hash
		authorityKeyIdentifier = keyid,issuer:always
		keyUsage = critical, digitalSignature, keyEncipherment
		extendedKeyUsage = serverAuth
		subjectAltName = @alt_names

		[alt_names]
		DNS.0 = earth.com
		DNS.1 = www.earth.com
		```
	3. ### Generating the private key for SERVER
		##### We will create RSA 2048 bits keys for Server certificates.

		```
		 # openssl genrsa -aes256 -out earth.key.pem 2048 
		```
	4. ### Generating the CSR request certificate for SERVER

		```
		 # openssl req -key earth.key.pem -new -sha256 -out earth.com.csr.pem
		```
	4. ### Generating the certificate for SERVER	
		##### We will set the validity of Server certificates for a period of 1 year.
		```
		# openssl ca -config /root/ca/ica2/intermediate_ca2.cnf -extensions server_cert -days 365 -notext -md sha256 -in earth.com.csr.pem -out earth.com.cert.pem
		```
	5. ### Verifying the generated SERVER certificate	
		```
		# openssl x509 -noout -text -in earth.com.cert.pem
		```
	7. ### Verifying the certificate chain till now : **`ROOT CA-->INTERMEDIATE CA 1-->INTERMEDIATE CA 2-->SERVER`**
		##### Create the `chain.cert.pem` file by concatenating the intermediate CA 2 certificate followed by the intermediate CA 1 certificate followed by the root CA certificate.
		```
		# openssl verify -CAfile chain.cert.pem earth.com.cert.pem
		```		
5. #### **SERVER Certificate Installation on Apache Web Server**
	##### We will change the certificate format from `.pem` to `.crt` for Apache web server.

   1.  Copy the generated server certificate `earth.com.cert.pem` from `/root/ca/server/earth/` to Apache location `C:\xampp\apache\conf\ssl.crt`
   
   2. Copy the final SSL chain certificate `chain.cert.pem` from `/root/ca/server/earth/` to Apache location `C:\xampp\apache\conf`
   3. Copy the private key `earth.key.pem` from `/root/ca/server/earth/` to Apache location `C:\xampp\apache\conf\ssl.key` after decrypting the encrypted private key as per the below code :
	```
	openssl rsa -in earth.key.pem -out unencryptedearthkey.pem
	```
---


# Queries and feedbacks

 ### Please email me at <gauravsachan9188@gmail.com>

---

 ## Source referred :
 <https://jamielinux.com/docs/openssl-certificate-authority/>



