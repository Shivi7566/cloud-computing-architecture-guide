# Module 6: Kubernetes & Cloud-Native Containers Masterclass

> **Part of the [Ultimate Cloud Computing Handbook](../README.md)** by [CloudKnowledge.in](https://cloudknowledge.in)
> Author: **Shivam Tiwari** (Senior IAM & Cloud Architect)

---

## Module 6: Kubernetes & Cloud-Native Containers Masterclass

        

          Containers packaged via Open Container Initiative (OCI) specifications and orchestrated via Kubernetes (K8s) represent the universal operating system of modern cloud computing.
        

        
### 6.1 Container Internals: Namespaces & Cgroups

        

          A container is not a lightweight virtual machine. A container is simply a standard Linux process isolated at the kernel level using two foundational Linux kernel subsystems:
        

        
          - Linux Namespaces (Boundary Isolation): Partitions system resources so that a process perceives its own private environment. Namespaces isolate:
            
              `pid` (Process IDs — the container process sees itself as PID 1)

              - `net` (Network interfaces, IP addresses, routing tables)

              - `mnt` (Filesystem mount points via OverlayFS)

              - `ipc` (Inter-process communication)

              - `uts` (Hostnames)

              - `user` (User and group ID mappings)

            
          
          - Control Groups (cgroups v2 - Resource Governance): Imposes strict hardware boundaries and metric accounting on CPU cycles, physical memory limits, I/O bandwidth, and network priority, preventing an errant process from causing system-wide Kernel Out-Of-Memory (OOM) panic.

        

        
### 6.2 Kubernetes Core Architecture

        

          A Kubernetes cluster operates as a master-worker distributed topology split into the Control Plane and Worker Nodes:
        

        
          
            

            Kubernetes High-Level Control Plane vs Data Plane
          
          
            
              kube-apiserver
              The RESTful entrypoint for all cluster management. Authenticates, validates, and mutates JSON/YAML manifests.

            
            
              etcd (Raft Consenus)
              
Highly available distributed key-value datastore persisting the complete declarative state of the entire cluster.

            
            
              kube-scheduler
              
Evaluates resource requests, affinity rules, and node taints to assign unscheduled pods to optimal worker nodes.

            
            
              kubelet & CRI
              
Node agent communicating with Containerd/CRI-O to pull images, initialize pods, and report node health.

            
          
        

        
### 6.3 Production Kubernetes Deployment Manifest

        

          The manifest below demonstrates an enterprise-grade Kubernetes Deployment adhering to best practices: explicit resource constraints, non-root security contexts, read-only root filesystems, and liveness/readiness probes:
        

        
          
            yaml
            Copy Code
          
          
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cloudknowledge-api
  namespace: production
  labels:
    app.kubernetes.io/name: cloudknowledge-api
    app.kubernetes.io/tier: backend
spec:
  replicas: 3
  revisionHistoryLimit: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 0
  selector:
    matchLabels:
      app.kubernetes.io/name: cloudknowledge-api
  template:
    metadata:
      labels:
        app.kubernetes.io/name: cloudknowledge-api
    spec:
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
            - weight: 100
              podAffinityTerm:
                labelSelector:
                  matchExpressions:
                    - key: app.kubernetes.io/name
                      operator: In
                      values: ["cloudknowledge-api"]
                topologyKey: "topology.kubernetes.io/zone"
      securityContext:
        runAsNonRoot: true
        runAsUser: 10001
        runAsGroup: 10001
        fsGroup: 10001
      containers:
        - name: api
          image: ghcr.io/cloudknowledge/api-service:v2.4.1
          imagePullPolicy: IfNotPresent
          securityContext:
            allowPrivilegeEscalation: false
            readOnlyRootFilesystem: true
            capabilities:
              drop: ["ALL"]
          ports:
            - containerPort: 8080
              name: http-web
          resources:
            requests:
              cpu: "250m"
              memory: "512Mi"
            limits:
              cpu: "1000m"
              memory: "1024Mi"
          livenessProbe:
            httpGet:
              path: /healthz
              port: 8080
            initialDelaySeconds: 15
            periodSeconds: 10
            timeoutSeconds: 2
            failureThreshold: 3
          readinessProbe:
            httpGet:
              path: /ready
              port: 8080
            initialDelaySeconds: 5
            periodSeconds: 5
            timeoutSeconds: 2
            failureThreshold: 2
```

        

        
### 6.4 Production Pod Autoscaling: HPA, VPA & Karpenter

        

          Autoscaling in Kubernetes operates at two distinct tiers: Pod scaling and Node infrastructure provisioning:
        

        
          - Horizontal Pod Autoscaler (HPA): Scales the number of pod replicas dynamically based on observed CPU utilization, memory pressure, or custom Prometheus metrics (e.g., SQS queue backlog depth).

          - Vertical Pod Autoscaler (VPA): Analyzes long-term container resource consumption and automatically adjusts CPU/memory `requests` and `limits` to optimize packing density.

          - Karpenter Node Autoscaler: Replaces the legacy Kubernetes Cluster Autoscaler. Karpenter watches for unschedulable pods, calculates their exact compute/memory requirements, and provisions right-sized, spot-discounted EC2 instances directly via AWS APIs in under 45 seconds, bypassing traditional Auto Scaling Group (ASG) latency.

        

        
### 6.5 Service Mesh Architecture: Istio & Linkerd

        

          As microservice counts grow past dozens of independent services, cross-cutting network concerns (mutual TLS, traffic splitting, distributed tracing, rate limiting) become difficult to implement inside individual application codebases.
        

        

          A Service Mesh solves this by injecting an ephemeral lightweight sidecar proxy (Envoy Proxy) into every Pod, intercepting all ingress and egress TCP traffic:
        

        
          - Automatic Zero-Trust mTLS: Every microservice-to-microservice communication is transparently upgraded to mutual TLS encryption with dynamic X.509 cryptographic certificate rotation, satisfying strict PCI-DSS and HIPAA requirements.

          - Canary Deployments: Operators can shift 5% of production traffic to a new version based on HTTP request headers or cookie values without modifying DNS records.

          - Ambient Mesh (Sidecar-less): Modern service mesh implementations (Istio Ambient) run shared node-level L4 ztunnels, eliminating the memory overhead of injecting an Envoy container into every single individual pod.

---

### 🔗 Essential Resources
- 🌐 **Main Platform:** [CloudKnowledge.in](https://cloudknowledge.in)
- 🖨️ **Print & Desk Cheat Sheets:** [PrintKrlo.in](https://printkrlo.in) (Doorstep delivery for diagrams & notes)
- 📺 **Video Labs & Walkthroughs:** [YouTube: @Cloud-Knowledge](https://youtube.com/@Cloud-Knowledge)
- 🔙 **[Return to Table of Contents](../README.md)**
