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
  - stoped at 9 min
 

## Day 3 (30/10/2025)
- ETCD Security Guidelines
- Configure etcd binaries
- Overview of certificate authority
- 
- 
   
