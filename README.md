# opensslproject

## Description

****SSL chain creation and installation // Becoming a Root CA****
	
In this project I have created 

a SSL chain of 4 certificates ( 1 Root CA , 2 Intermediate CA's and 1 server certificate )

Tools Used: openssl, gitbash , visual studio code


# Folder and Files structure followed

	- Root CA Folder
		- Certificate Folder
				- milkyway.cert.pem
		- Private Key Folder
				- milkyway.key.pem
		- Root-CA configuration file.cnf
		- serial file.txt
    		 
	- Intermediate CA 1
		- Certificate Folder
				- sun.cert.pem
		- Private Key Folder
				- sun.key.pem
		- CSR Folder
				- sun.csr.pem
		- Intermediate-CA 1 configuration file.cnf
		- index file.txt
		- serial file.txt
	
	- Intermediate CA 2
		- Certificate Folder
				- earth.cert.pem
		- Private Key Folder
				- earth.key.pem
		- CSR Folder
				- earth.csr.pem
		- Intermediate-CA 2 configuration file.cnf
		- index file.txt
		- serial file.txt
			 
	- Server
		-  Certificate Folder
				- www.venus.com.cert.pem
		- Private Key Folder
				- www.venus.com.key.pem
		- CSR Folder
				- www.venus.com.csr.pem
		- Server configuration file.cnf


# Installation and configuration guide

Will publish later
 ...



 **Source referred :**  [https://jamielinux.com/docs/openssl-certificate-authority/]
