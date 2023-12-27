# Security
PKI: Public key Infrastructure

K8s use different tools to generate certificate such as :
- EASYRS    
- OPENSSL
- CFSSL

### OPENSSL to create Certificates

![create certificates ](images/Screenshot%202023-12-13%20at%2014.40.39.png)

Use the same process to create certficates for admin, etcd, api-server and all k8s components


To decode a Certificate:
```sh
openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout
```

Inspecting Service logs 
```sh
journalctl -u etcd.service -l
```