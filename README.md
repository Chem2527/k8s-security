# k8s-security
# Day 1 (25/10/2025)
## Curriculum

- 1. Cluster setup
- 2. Cluster Hardening
- 3. System Hardening
- 4. Minimize Microservice Vulneribilities
- 5. Supply chain Security
- 6. Monitoring, Logging & Runtime Security

## Basics of CIS Benchmarks

- CIS benchmarks are best-practice security guidelines developed by center for internet security (CIS)
- They provide step-by-step instructions on how to secure systems
- CIS benchmarks are available for providers,devices,web servers

- **Note** - CIS Benchmarks, Organisation Specific security Benchmarks are enough for strong security posture.
- [CIS BENCHMARKS LIST](https://www.cisecurity.org/cis-benchmarks)

## CIS Benchmarks for Kubernetes
  
- CIS Benchmarks at Cluster level
  1. Control plane node
     ```
     apiserver
     etcd
     scheduler
     controller-manager
     ```
  2. Worker Node
    ```
    kubelet
    kubeproxy
    ```
- Pod security Standards
- CNI
- Secrets
- General

## Security Shared Responsibility model in managed kubernetes offerings
<img width="148" height="149" alt="image" src="https://github.com/user-attachments/assets/41ca90c1-df24-4c12-898a-0e66f428a067" />

- **kubebench** : Tool that checks whether kubernetes is deployed securely or not by running checks.

# Day 2 (28/10/2025)
- choosing this os  as part of demo:  24.04 lts x64
- ensure to modify the sg in such a way that it only allows traffic from our ip.

## To secure etcd, we need to focus on 3 areas:
```
1. Plain Text Data Storage
2. TLS encryption
3. Certificate based authentication
```
- 1. Plain Text Data Storage:
     
- By default etcd stores data in plain text in the etcd
- If an attacker somehow gets access to etcd, they can read secrets in plain text
- Install etcd under /tmp directory
- Port 2379 is the default client port for etcd
- Port 2380 is used for internal communication between etcd nodes in a cluster
- etcd stores data in the form of key = value

  ```
  etcdctl put course "this is k8s security"
  etcdctl get course ## we will get the above value
  ```
- If you go inside the /tmp directory where we installed etcd we will be able to see **default.etcd** and inside this we have **member**
- If we do **ls -la** we can see **snap,wal** and if we do **grep -R "this is k8s security" .** we can see 2 matches and just do cat for both of these  
- How to configure the api server so that it will securely store the data in the etcd

- 2. TLS encryption:

- The data in-transit b/w api server and etcd can also be intercepted.
- Intercepted: It someone (usually an attacker) can see, capture, or modify the data while it's traveling from one place to another.
- We need to ensure that data which is flowing b/w **API server and ETCD** is protected. Encrypt channel b/w **API server and ETCD**.

- login to server in 2 separate tabs
- In tab 1 type
  ```
  tcpdump -i lo -X port 2379 #(At this point no data is flowing)
  ```
- In tab 2 type
- netstat -tulnp # (we can see 2379 is listening)
- In tab 2 type
  ```
  etcdctl get course
  ```
  
- In tab 1 see the data flow which contains key words of this is k8s security as we passed the same value under secret.


  - Doing the same practical hands-on but this time with tls enabled and using tcp dump we will check whther we r able to see the data flow or not.

 

## Day 3 (30/10/2025)
 **Overview of certificate authority**
- Kubernetes components like api server, kubelet, controller manager,etcd should communicate with each other over secure channel.
- These components need a mechanism to verify each others identity.
- Certificate authority: It issues digital certificates

- **Use-cases where CA certificate will be used**
- To generate TLS certificates for secure communication (we can generate tls for etcd so that etcd will listen on https)
- Generate certificates for client/component for authentication

- Command for verifying CAfile
```
openssl verify -CAfile <CA_certificate.crt> <certificate_to_verify.crt>
```

- ex: openssl verify -CAfile  example-ca.crt client.crt (shows verification failed)
```
openssl verify -CAfile k8s.crt client.crt (Shows ok -  It means that client.crt is valid and trusted by the CA in k8s.crt)
```

  ### Configure Certificate authority:
- **Create a private key for certificate authority& CSR**
```
mkdir /root/certificates
cd /root/certificates
openssl genrsa -out ca.key 2048
openssl req new -key ca.key -subj "/CN=sai" -out ca.csr
```
**Self sign the CSR**
```
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt -days 1000
```
**Remove the CSR**

```
rm -f ca.csr
```
**Check contents of certificate**
```
openssl x509 -in client.crt -text -noout ( Shows info about certificate client.crt in human readable format)
```

## Day 4:  Workflow - issuance of signed certificates

- create .key - use .key and generate .csr - self sign the .csr and output is .crt - use this .crt for signing other certificates - verify whether the certificate used for signing another is matching or not.

  ```
  cd /root/certificates
  
  openssl genrsa -out ca.key 2048

  openssl req -new -key ca.key -sub "/CN=sai"  -out ca.csr

  openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt -days 1000

  openssl genrsa -out sai.key 2048
  
  openssl req -new -key sai.key -subj "/CN=zealvora" -out sai.csr

  openssl x509 -req -in sai.csr -CA ca.crt -CAkey ca.key -out sai.crt -days 1000

  openssl x509 -in sai.crt -text -noout

  openssl verify -CAfile ca.crt sai.crt

  rm -f zeal.crt sai.key sai.csr
  
  ```
  
