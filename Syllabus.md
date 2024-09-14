## CKS -- Syllabus

### Old (v1.30) - 10% - Cluster Setup
```
• Use Network security policies to restrict cluster level access
• Use CIS benchmark to review the security configuration of Kubernetes components (etcd, kubelet, kubedns, kubeapi)
• Properly set up Ingress objects with security control
• Protect node metadata and endpoints
• Minimize use of, and access to, GUI elements
• Verify platform binaries before deploying
```
### New (v1.31) - 15% - Cluster Setup
```
• Use Network security policies to restrict cluster level access
• Use CIS benchmark to review the security conﬁguration of Kubernetes components (etcd, kubelet, kubedns, kubeapi)
• Properly set up Ingress objects with TLS
• Protect node metadata and endpoints
• Verify platform binaries before deploying
```
### Old (v1.30) - 15% - Cluster Hardening
```
• Restrict access to Kubernetes API
• Use Role Based Access Controls to minimize exposure
• Exercise caution in using service accounts e.g. disable defaults, minimize permissions on newly created ones
• Update Kubernetes frequently
```
### New (v1.31) - 15% - Cluster Hardening
```
• Restrict access to Kubernetes API
• Use Role Based Access Controls to minimize exposure
• Exercise caution in using service accounts e.g. disable defaults, minimize permissions on newly created ones
• Upgrade Kubernetes to avoid vulnerabilities
```
### Old (v1.30) - 15% - System Hardening
```
• Minimize host OS footprint (reduce attack surface)
• Minimize IAM roles
• Minimize external access to the network
• Appropriately use kernel hardening tools such as AppArmor, seccomp
```
### New (v1.31) - 10% - System Hardening
```
• Minimize host OS footprint (reduce attack surface)
• Using least-privilege identity and access management
• Minimize external access to the network
• Appropriately use kernel hardening tools such as AppArmor, seccomp
```
### Old (v1.30) - 20% - Minimize Microservice Vulnerabilities
```
• Setup appropriate OS level security domains
• Manage kubernetes secrets
• Use container runtime sandboxes in multi-tenant environments (e.g. gvisor, kata containers)
• Implement pod to pod encryption by use of mTLS
```
### New (v1.31) - 20% - Minimize Microservice Vulnerabilities
```
• Use appropriate pod security standards
• Manage kubernetes secrets
• Understand and implement isolation techniques (multi-tenancy, sandboxed containers, etc.)
• Implement Pod-to-Pod encryption using Cilium
```
### Old (v1.30) - 20% - Supply Chain Security
```
• Minimize base image footprint
• Secure your supply chain: whitelist allowed image registries, sign and validate images
• Use static analysis of user workloads (e.g. kubernetes resources, docker files)
• Scan images for known vulnerabilities
```
### New (v1.31) - 20% - Supply Chain Security
```
• Minimize base image footprint
• Understand your supply chain (e.g. SBOM, CI/CD, artifact repositories)
• Secure your supply chain (permitted registries, sign and validate artifacts, etc.)
• Perform static analysis of user workloads and container images (e.g. Kubesec, KubeLinter)
```
### Old (v1.30) - 20% - Monitoring, Logging and Runtime Security
```
• Perform behavioral analytics of syscall process and file activities at the host and container level to detect malicious activities
• Detect threats within physical infrastructure, apps, networks, data, users and workloads
• Detect all phases of attack regardless where it occurs and how it spreads
• Perform deep analytical investigation and identification of bad actors within environment
• Ensure immutability of containers at runtime
• Use Audit Logs to monitor access
```
### New (v1.31) - 20% - Monitoring, Logging and Runtime Security
```
• Perform behavioral analytics to detect malicious activities
• Detect threats within physical infrastructure, apps, networks, data, users and workloads
• Investigate and identify phases of attack and bad actors within the environment
• Ensure immutability of containers at runtime
• Use Kubernetes audit logs to monitor access
```