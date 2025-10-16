# k8s-security

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
   
