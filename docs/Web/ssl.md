# Ssl
![SSLCertificate](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/ssl.png){: style="height:100px;width:100px" align=right }
Some very useful ssl commands to check, validate SSL certificates
## Check certificate from URL
```bash
openssl s_client -showcerts -connect domain.com:443
```

### Validate SSL key,cert and CSF match
```bash
  openssl x509 -noout -modulus -in vettom.crt | openssl md5  #Hash of cert
  openssl rsa -noout -modulus -in vettom.key | openssl md5   #Hash of Key
  openssl req -noout -modulus -in vettom.csr | openssl md5   #Hash of csr
```

### Print certificate information
```bash
openssl req -in sample.csr -text -noout
openssl x509 -in vettom.crt -text -noout
```

### Remove password 
```bash
openssl rsa -in key.pem -out server.key 
```

### Self signed certificate
```bash
openssl req \ -newkey rsa:2048 -nodes -keyout vettom.key \ 
   -x509 -days 365 -out vettom.crt
```

### SAN certificate request generator
```bash
  cat > sample.cnf << EOF    

   [req] 
   default_bits = 2048 
   prompt = no 
   default_md = sha256 
   req_extensions = req_ext 
   distinguished_name = dn 

   [ dn ] 
   C=GB 
   ST=London 
   L=London 
   O=AVettom PLC 
   CN = www.sample.com 
   [ req_ext ] 
   subjectAltName = @alt_names 
   [ alt_names ] 
   DNS.1 = *.sample.com 
   DNS.2 = anotherdomain.com
EOF 

# Generate CSR from config file
openssl req -new -sha256 -nodes -out sample.csr -newkey rsa:2048 \
     -keyout sample.key -config sample.cnf
```

## Pfx file handling
```bash
# extract public and private key
openssl pkcs12 -info -in cert.pfx

# Extract private key
openssl pkcs12 -in certname.pfx -nocerts -out key.pem -nodes

# export certificate
openssl pkcs12 -in certname.pfx -nokeys -out cert.pem

# Pfx file with chain

# Prepare Private Key, Cert and chin in PEM format
openssl pkcs12 -export -out newpfxcert.pfx -inkey private.key -in cert.cer \
-certfile chain.cer
```